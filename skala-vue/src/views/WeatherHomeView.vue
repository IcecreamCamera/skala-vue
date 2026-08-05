<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import axios from 'axios'

import BaseDashboardCard from '../components/exercise/BaseDashboardCard.vue'
import SearchBar from '../components/exercise/SearchBar.vue'
import WeatherCard from '../components/exercise/WeatherCard.vue'

const weatherList = ref([])
const router = useRouter()
const route = useRoute()
const isLoading = ref(false)

const API_KEY = 'a9a51b0de5bdd376686910e584c10f5c'
const BASE_URL = 'https://api.openweathermap.org/data/2.5/weather'

// 초기 기본 도시 목록 정의 (영문 쿼리명과 한글 노출명 매핑)
const DEFAULT_CITIES = [
  { q: 'Seoul', name: '서울' },
  { q: 'Suwon', name: '수원' },
  { q: 'Busan', name: '부산' },
  { q: 'Gangneung', name: '강릉' },
  { q: 'Saint Petersburg', name: '상트페테르부르크' },
  { q: 'Tampa,US', name: '플로리다 탬파' },
  { q: 'Ho Chi Minh', name: '호치민' },
  { q: 'New York', name: '뉴욕' },
  { q: 'Helsinki', name: '헬싱키' },
  { q: 'Wellington,NZ', name: '웰링턴' }
]

const CITY_NAME_MAP = {
  '서울': 'Seoul',
  '수원': 'Suwon',
  '부산': 'Busan',
  '강릉': 'Gangneung',
  '상트페테르부르크': 'Saint Petersburg',
  '탬파': 'Tampa,US',
  '호치민': 'Ho Chi Minh',
  '뉴욕': 'New York',
  '헬싱키': 'Helsinki',
  '웰링턴': 'Wellington,NZ',
  // 자주 검색할 만한 도시 추가
  '도쿄': 'Tokyo',
  '오사카': 'Osaka',
  '런던': 'London',
  '파리': 'Paris',
  '베이징': 'Beijing',
  '상하이': 'Shanghai',
  '방콕': 'Bangkok',
  '싱가포르': 'Singapore',
  '로스앤젤레스': 'Los Angeles',
  '시드니': 'Sydney',
  '세종': 'Sejong',
  '제주': 'Jeju',
  '광주': 'Gwangju',
  '대전': 'Daejeon',
  '대구': 'Daegu',
  '울산': 'Ulsan',
  '인천': 'Incheon',
  '하노이': 'Hanoi',
  '마닐라': 'Manila',
  '멜버른': 'Melbourne',
  '밴쿠버': 'Vancouver',
  '토론토': 'Toronto',
  '리우데자네이루': 'Rio de Janeiro',
  '멕시코시티': 'Mexico City',
  '두바이': 'Dubai',
  '이스탄불': 'Istanbul',
  '카이로': 'Cairo',
  '케이프타운': 'Cape Town',
  '모스크바': 'Moscow',
  '바르셀로나': 'Barcelona',
  '밀라노': 'Milan',
  '뮌헨': 'Munich',
  '프라하': 'Prague',
  '부다페스트': 'Budapest',
  '아테네': 'Athens',
  '리스본': 'Lisbon',
  '오슬로': 'Oslo',
  '스톡홀름': 'Stockholm',
  '코펜하겐': 'Copenhagen',
  '오타와': 'Ottawa',
  '몬트리올': 'Montreal',
  '마드리드': 'Madrid',
  '바르샤바': 'Warsaw',
  '부에노스아이레스': 'Buenos Aires',
  '상파울루': 'Sao Paulo',
  '울란바토르': 'Ulaanbaatar',
}

// 1. 초기 10개 도시 날씨 가져오기 (반복문 처리로 코드 경량화)
const fetchRealTimeWeather = async () => {
  isLoading.value = true
  try {
    const promises = DEFAULT_CITIES.map(city => 
      axios.get(`${BASE_URL}?q=${encodeURIComponent(city.q)}&appid=${API_KEY}&units=metric&lang=kr`)
    )
    
    const responses = await Promise.all(promises)
    
    weatherList.value = responses.map((res, index) => ({
      id: `city_0${index + 1}`,
      name: DEFAULT_CITIES[index].name,
      temp: res.data.main.temp,
      status: res.data.weather[0].main,
      icon: res.data.weather[0].icon
    }))

    console.log('🟢 [API 통신 완료] 메인 대시보드 실시간 기상 장부 동기화:', weatherList.value)
  } catch (error) {
    console.error('🔴 날씨 API 연동 실패:', error)
  } finally {
    isLoading.value = false
  }
}

const searchQuery = ref('')
const selectedCityInfo = ref('카드를 클릭하거나 검색해 보세요.')

//검색 실패 모달
const showErrorModal = ref(false)
const errorMessage = ref('')


// 2. 사용자가 검색한 새로운 도시를 API로 찾아 목록에 추가하는 함수
const handleSearchCity = async () => {

  // 한글 조합 확정 중 발생한 Enter는 무시
  if (event?.isComposing) return
  
  const query = searchQuery.value.trim()
  if (!query) return

  // 이미 검색 요청이 진행 중이면 중복 실행 방지
  if (isLoading.value) return

  // 중복 검색 방지 (이미 목록에 한글 이름이나 영문 입력값이 포함되어 있다면 추가 안 함)
  const isExist = weatherList.value.some(item => 
    item.name.toLowerCase() === query.toLowerCase()
  )
  if (isExist) {
    errorMessage.value = '이미 목록에 존재하는 도시입니다.'
    showErrorModal.value = true
    return
  }

  // 🔥 한글 입력이면 매핑 테이블에서 영문명으로 변환, 없으면 원본 그대로 시도
  const apiQuery = CITY_NAME_MAP[query] || query

  isLoading.value = true
  try {
    const res = await axios.get(`${BASE_URL}?q=${encodeURIComponent(apiQuery)}&appid=${API_KEY}&units=metric&lang=kr`)
    
    const REVERSE_CITY_MAP = Object.fromEntries(
      Object.entries(CITY_NAME_MAP).map(([kr, en]) => [en.split(',')[0], kr])
    )

    const newCity = {
      id: `city_search_${Date.now()}`, // 중복되지 않는 고유 ID 생성
      name: REVERSE_CITY_MAP[res.data.name] || res.data.name,            // API가 반환한 공식 도시 이름
      temp: res.data.main.temp,
      status: res.data.weather[0].main,
      icon: res.data.weather[0].icon
    }

    // 새 도시를 날씨 목록 맨 앞에 추가
    weatherList.value.unshift(newCity)
    selectedCityInfo.value = `새로운 도시 [${newCity.name}] 추가 완료!`
    
    // 검색창 비우기 (원치 않으시면 이 라인을 주석 처리하세요)
    searchQuery.value = '' 
  } catch (error) {
    console.error('도시 검색 실패:', error)
    errorMessage.value = '도시를 찾을 수 없습니다. 도시명을 정확히 입력하거나 영문으로 시도해 주세요. (예: London, Tokyo)'
    showErrorModal.value = true
  } finally {
    isLoading.value = false
  }
}

// 모달 닫기 함수
const closeErrorModal = () => {
  showErrorModal.value = false
}

// 3. [신규 기능] 카드의 삭제 버튼(X)을 눌러 해당 도시를 목록에서 제거하는 함수
const handleRemoveCity = (id) => {
  // 1. 삭제하기 전에 해당 도시 정보를 먼저 찾기
  const targetCity = weatherList.value.find((item) => item.id === id)
  
  // 2. 목록에서 제거
  weatherList.value = weatherList.value.filter((item) => item.id !== id)
  
  // 3. 찾은 도시 이름으로 메시지 표시
  if (targetCity) {
    selectedCityInfo.value = `${targetCity.name}이(가) 목록에서 삭제되었습니다.`
  }
}

onMounted(() => {
  if (route.query.search) {
    searchQuery.value = route.query.search
  }
  fetchRealTimeWeather()
})

watch(searchQuery, (newQuery) => {
  router.push({
    path: route.path,
    query: { search: newQuery || undefined },
  })
})

// 입력값에 따라 리스트를 필터링하는 computed
const filteredWeatherList = computed(() => {
  const query = searchQuery.value.trim()
  if (!query) return weatherList.value
  return weatherList.value.filter((item) => 
    item.name.toLowerCase().includes(query.toLowerCase())
  )
})

const handleDetailJump = (id) => {
  router.push(`/weather/${id}`)
}
</script>

<template>
  <div class="dashboard-wrapper">
    <BaseDashboardCard>
      <h3><img src="/svg/search.svg" alt="검색" /> 도시 검색</h3>
      <SearchBar 
        :current-query="searchQuery" 
        @update-query="(val) => (searchQuery = val)" 
        @keyup.enter="handleSearchCity($event)"
      />
      <p style="font-size: 12px; color: #7f8c8d; margin-top: 5px; margin-left: 30px;">
        목록에 없는 새로운 도시를 영어로 입력 후 [Enter]를 누르면 날씨가 추가됩니다.
      </p>
    </BaseDashboardCard>

    <BaseDashboardCard>
      <h3><img src="/svg/city.svg" alt="도시" /> 지역별 날씨 현황</h3>
      <p v-if="isLoading" style="text-align: center; color: #3498db; font-weight: bold; padding: 20px 0">
        <img src='/svg/refresh.svg' alt="로딩중" /> 글로벌 기상 위성으로부터 실시간 기상 데이터를 수신 중입니다...
      </p>
      <section class='list-box'>
        <WeatherCard 
          v-for="item in filteredWeatherList" 
          :key="item.id" 
          :city-item="item" 
          @select-card="(msg) => (selectedCityInfo = msg)" 
          @click-detail="handleDetailJump(item.id)" 
          @delete-card="handleRemoveCity(item.id)"
        />
        <p v-if="filteredWeatherList.length === 0" style="text-align: center; color: #e74c3c; padding: 10px 0">
          <img src="/svg/xmark.svg" alt="X" /> 결과와 일치하는 도시가 없습니다.
        </p>
      </section>
    </BaseDashboardCard>
    <div class="status-bar">{{ selectedCityInfo }}</div>

    <!-- 검색 실패 모달-->
    <Teleport to="body">
      <div v-if="showErrorModal" class="modal-overlay" @click="closeErrorModal">
        <div class="modal-box" @click.stop>
          <div class="modal-icon">⚠️</div>
          <h4>검색 실패</h4>
          <p>{{ errorMessage }}</p>
          <button class="modal-close-btn" @click="closeErrorModal">확인</button>
        </div>
      </div>
    </Teleport>
  </div>
</template>


<style scoped>

@import url('https://googleapis.com');

.dashboard-wrapper {
  font-family: 'Pretendard', sans-serif;
  background-color: #EBF3ED;
  color: #2C3E50;
  padding: 32px;
  
  width: 100%;          
  max-width: 100%;      
  margin: 0;           
  box-sizing: border-box; 
  
  border: 1px solid #D1E2D6;
  border-radius: 24px;
  box-shadow: 0 10px 30px rgba(44, 62, 80, 0.08);
  letter-spacing: -0.04em;
}

h3 {
  font-size: 1.3rem;
  font-weight: 800;
  color: #27AE60;
  margin: 0 0 16px 0;
  display: flex;
  align-items: center;
  gap: 8px;
}

h3 img {
  width: 24px;
  height: 24px;
}

p img {
  width: 20px;
  height: 20px;
  vertical-align: middle;
  margin-right: 6px;
}

section p img{
  width: 24px;
  height: 24px;
  vertical-align: middle;
}

.list-box {
  margin-bottom: 32px;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 16px;
}

.status-bar {
  background-color: rgba(255, 255, 255, 0.6);
  border: 2px dashed #D1E2D6;
  padding: 16px;
  text-align: center;
  border-radius: 14px;
  font-size: 0.9rem;
  font-weight: 600;
  color: #566573;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-box {
  background: white;
  border-radius: 12px;
  padding: 24px 28px;
  width: 320px;
  max-width: 90%;
  text-align: center;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.2);
  animation: modal-pop 0.2s ease-out;
}

.modal-icon {
  font-size: 36px;
  margin-bottom: 8px;
}

.modal-box h4 {
  margin: 0 0 8px;
  color: #e74c3c;
  font-size: 18px;
}

.modal-box p {
  margin: 0 0 20px;
  color: #555;
  font-size: 14px;
  line-height: 1.5;
}

.modal-close-btn {
  background: #3498db;
  color: white;
  border: none;
  padding: 8px 24px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  font-weight: bold;
}

.modal-close-btn:hover {
  background: #2980b9;
}

@keyframes modal-pop {
  from {
    opacity: 0;
    transform: scale(0.9);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

</style>
