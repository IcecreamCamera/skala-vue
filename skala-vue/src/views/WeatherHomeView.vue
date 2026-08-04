<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import axios from 'axios' // 🟢 Axios 수입

import BaseDashboardCard from '../components/exercise/BaseDashboardCard.vue'
import SearchBar from '../components/exercise/SearchBar.vue'
import WeatherCard from '../components/exercise/WeatherCard.vue'

const weatherList = ref([])
const router = useRouter()
const route = useRoute()
const isLoading = ref(false)

// 💡 OpenWeatherMap 필수 연동 규격 정의
const API_KEY = 'a9a51b0de5bdd376686910e584c10f5c'
const BASE_URL = 'https://api.openweathermap.org/data/2.5/weather'


const fetchRealTimeWeather = async () => {
  isLoading.value = true
  try {
    const [seoulRes, suwonRes, busanRes, gangneungRes, stpetersburgRes, tampaRes, hochiminhRes, newyorkRes, helsinkiRes, wellingtonRes] = await Promise.all([
      axios.get(`${BASE_URL}?q=Seoul&appid=${API_KEY}&units=metric&lang=kr`),
      axios.get(`${BASE_URL}?q=Suwon&appid=${API_KEY}&units=metric&lang=kr`),
      axios.get(`${BASE_URL}?q=Busan&appid=${API_KEY}&units=metric&lang=kr`),
      axios.get(`${BASE_URL}?q=Gangneung&appid=${API_KEY}&units=metric&lang=kr`),
      axios.get(`${BASE_URL}?q=Saint%20Petersburg&appid=${API_KEY}&units=metric&lang=kr`),
      axios.get(`${BASE_URL}?q=Tampa,US&appid=${API_KEY}&units=metric&lang=kr`),
      axios.get(`${BASE_URL}?q=Ho%20Chi%20Minh&appid=${API_KEY}&units=metric&lang=kr`),
      axios.get(`${BASE_URL}?q=New%20York&appid=${API_KEY}&units=metric&lang=kr`),
      axios.get(`${BASE_URL}?q=Helsinki&appid=${API_KEY}&units=metric&lang=kr`),
      axios.get(`${BASE_URL}?q=Wellington,NZ&appid=${API_KEY}&units=metric&lang=kr`)
    ])
    weatherList.value = [
      { id: 'city_01', name: '서울', temp: seoulRes.data.main.temp, status: seoulRes.data.weather[0].main, icon: seoulRes.data.weather[0].icon },
      { id: 'city_02', name: '수원', temp: suwonRes.data.main.temp, status: suwonRes.data.weather[0].main, icon: suwonRes.data.weather[0].icon },
      { id: 'city_03', name: '부산', temp: busanRes.data.main.temp, status: busanRes.data.weather[0].main, icon: busanRes.data.weather[0].icon },
      { id: 'city_04', name: '강릉', temp: gangneungRes.data.main.temp, status: gangneungRes.data.weather[0].main, icon: gangneungRes.data.weather[0].icon },
      { id: 'city_05', name: '상트페테르부르크', temp: stpetersburgRes.data.main.temp, status: stpetersburgRes.data.weather[0].main, icon: stpetersburgRes.data.weather[0].icon },
      { id: 'city_06', name: '플로리다 탬파', temp: tampaRes.data.main.temp, status: tampaRes.data.weather[0].main, icon: tampaRes.data.weather[0].icon },
      { id: 'city_07', name: '호치민', temp: hochiminhRes.data.main.temp, status: hochiminhRes.data.weather[0].main, icon: hochiminhRes.data.weather[0].icon },
      { id: 'city_08', name: '뉴욕', temp: newyorkRes.data.main.temp, status: newyorkRes.data.weather[0].main, icon: newyorkRes.data.weather[0].icon },
      { id: 'city_09', name: '헬싱키', temp: helsinkiRes.data.main.temp, status: helsinkiRes.data.weather[0].main, icon: helsinkiRes.data.weather[0].icon },
      { id: 'city_10', name: '웰링턴', temp: wellingtonRes.data.main.temp, status: wellingtonRes.data.weather[0].main, icon: wellingtonRes.data.weather[0].icon }
    ]
    console.log('🟢 [API 통신 완료] 메인 대시보드 실시간 기상 장부 동기화:', weatherList.value)
  } catch (error) {
    console.error('🔴 날씨 API 연동 실패:', error)
  } finally {
    isLoading.value = false
  }
}


const searchQuery = ref('')
const selectedCityInfo = ref('카드를 클릭하거나 검색해 보세요.')

// 초기 마운트 시 주소창의 쿼리(?search=) 스트링 읽어서 상태 복원 (KeepAlive를 적용해야만 동작함)
onMounted(() => {
  if (route.query.search) {
    searchQuery.value = route.query.search
  }
  // 🟢 [고도화] 컴포넌트 장착 직후 기상청 실시간 데이터를 호출합니다.
  fetchRealTimeWeather()
})

// 타이핑될 때마다 주소창의 쿼리 스트링 값을 실시간 푸시 개편 (현재 큰 의미없음)
watch(searchQuery, (newQuery) => {
  router.push({
    path: route.path,
    query: { search: newQuery || undefined },
  })
})

const filteredWeatherList = computed(() => {
  const query = searchQuery.value.trim()
  if (!query) return weatherList.value
  return weatherList.value.filter((item) => item.name.includes(query))
})

// 자식 카드 컴포넌트의 상세보기 신호를 받으면 해당 ID 주소로 라우터 점프 실행
const handleDetailJump = (id) => {
  router.push(`/weather/${id}`)
}
</script>

<template>
  <div class="dashboard-wrapper">
    <BaseDashboardCard>
      <h3><img src = "/svg/search.svg" alt="검색" /> 도시 검색</h3>
      <SearchBar :current-query="searchQuery" @update-query="(val) => (searchQuery = val)" />
    </BaseDashboardCard>

    <BaseDashboardCard>
      <h3><img src = "/svg/city.svg" alt="도시" /> 지역별 날씨 현황</h3>
      <p v-if="isLoading" style="text-align: center; color: #3498db; font-weight: bold; padding: 20px 0"> <img src = '/svg/refresh.svg' alt = "로딩중" /> 글로벌 기상 위성으로부터 실시간 기상 데이터를 수신 중입니다...</p>
      <section class = 'list-box'>
        <WeatherCard v-for="item in filteredWeatherList" :key="item.id" :city-item="item" @select-card="(msg) => (selectedCityInfo = msg)" @click-detail="handleDetailJump(item.id)" />
        <p v-if="filteredWeatherList.length === 0" style="text-align: center; color: #e74c3c; padding: 10px 0"><img src = "/svg/xmark.svg" alt = "X" /> 결과와 일치하는 도시가 없습니다.</p>
      </section>
    </BaseDashboardCard>
    <div class="status-bar">{{ selectedCityInfo }}</div>
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
</style>
