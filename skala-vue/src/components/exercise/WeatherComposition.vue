<script setup>
import { ref, computed, watch, watchEffect } from 'vue'

// 1. [1일차 데이터] 가상의 백엔드 데이터 배열
const weatherList = ref([
  { id: 'city_01', name: '서울', temp: 24, status: '비', image: '/svg/rain.svg' },
  { id: 'city_02', name: '수원', temp: 28, status: '맑음', image:'/svg/sunny.svg' },
  { id: 'city_03', name: '부산', temp: 31, status: '구름많음', image: '/svg/partly_cloudy.svg' },
  { id: 'city_04', name: '강릉', temp: 22, status: '흐림', image: '/svg/cloudy.svg' },
  { id: 'city_05', name: '상트페테르부르크', temp: 2, status: '눈', image: '/svg/snow.svg' },
  { id: 'city_06', name: '플로리다 템파', temp: 22, status: '천둥번개', image: '/svg/storm.svg' },
  { id: 'city_07', name: '호치민', temp: 30, status: '비', image: '/svg/rain.svg' },
  { id: 'city_08', name: '뉴욕', temp: 21, status: '맑음', image: '/svg/sunny.svg' },
  { id: 'city_09', name: '헬싱키', temp: -2, status: '구름많음', image: '/svg/partly_cloudy.svg' }
])

// 2. [1일차 데이터] 검색어 및 알림창 제어용 데이터
const searchQuery = ref('')
const selectedCityInfo = ref('카드를 클릭하거나 검색해 보세요.')



// 3. [2일차 추가] computed를 활용한 실시간 검색 필터링 연산기 (★핵심)
const filteredWeatherList = computed(() => {
  // 사용자가 입력한 검색어의 앞뒤 공백을 제거합니다.
  const query = searchQuery.value.trim()

  // 검색어가 비어있다면 원본 weatherList를 그대로 보여줍니다.
  if (!query) {
    return weatherList.value
  }

  // 검색어가 포함된 도시만 칼같이 필터링하여 실시간으로 뱉어냅니다.
  return weatherList.value.filter((item) => item.name.includes(query))
})

// 4. [2일차 추가] watch를 활용한 선택 도시 추적 센서
// selectedCityInfo의 문구 변화를 감시하여 후속 로그를 처리합니다.
watch(selectedCityInfo, (newInfo) => {
  console.log(`👁️‍🗨️ [watch 감지] 상태 바 문구가 업데이트되었습니다 -> "${newInfo}"`)
})

// 5. [2일차 추가] watchEffect를 활용한 자동 의존성 API 로그 시뮬레이션
// 타이핑할 때마다 변하는 searchQuery를 AI CCTV처럼 자동 추적합니다.
watchEffect(() => {
  console.log(`🤖 [watchEffect 자동 호출] 현재 검색어 '${searchQuery.value}'에 매칭되는 API 데이터를 필터링합니다.`)
})

// 알림 대행 함수 (window 객체 격리 우회)
const showDetail = (cityName, status, temp) => {
  window.alert(`${cityName}의 현재 날씨는 [${status}] 상태이며 기온은 ${temp}°C 입니다.`)
}
</script>

<template>
  <div class="dashboard-wrapper">
    <section class="search-box">
      <h3>🔍 도시 검색</h3>
      <input type="text" :value="searchQuery" @input="(e) => (searchQuery = e.target.value)" placeholder="검색할 도시 이름 입력" />
      <p>
        검색 중인 도시: <strong>{{ searchQuery }}</strong>
      </p>
    </section>
    <h3>🏙️ 지역별 날씨 현황</h3>
    
    <section class="list-box">
      <div v-for="item in filteredWeatherList" :key="item.id" class="weather-card" @click="selectedCityInfo = `${item.name}이 선택되었습니다.`">
        <h4>
          {{ item.name }} <br /> 
          <span class="status-text">{{ item.status }}</span> <br />
          <!-- 주소 글자는 지우고, 순수한 이미지 태그만 아래에 넣습니다 -->
          <img :src="item.image" :alt="item.status" class="weather-icon" />
        </h4>
        
        <p>현재 기온: {{ item.temp }}°C</p>
        <span v-if="item.temp >= 25" class="badge hot">🔥 더움</span>
        <span v-else-if="item.temp <25 && item.temp >= 5" class = "badge cool">💨 선선함</span>
        <span v-else class = "badge cold">❄️ 추움</span>  
        <!-- v-else-if 이용 추움 추가 -->
        <button class="btn-detail" @click.stop="showDetail(item.name, item.status, item.temp)">상세</button>
      </div>

      <p v-if="filteredWeatherList.length === 0" style="text-align: center; color: #e74c3c; padding: 10px 0">😭 검색 결과와 일치하는 도시가 없습니다.</p>
    </section>

    <div class="status-bar">
      {{ selectedCityInfo }}
    </div>
  </div>
</template>

<style scoped>
/* 프리텐다드 폰트 적용 */
@import url('https://googleapis.com');

.dashboard-wrapper {
  font-family: 'Pretendard', sans-serif;
  background-color: #EBF3ED;
  color: #2C3E50;
  padding: 32px;
  
  /* 💥 이 부분을 수정합니다 💥 */
  width: 100%;           /* 너비를 100%로 설정 */
  max-width: 100%;       /* 최대 너비 제한을 해제하거나 100%로 변경 */
  margin: 0;             /* 바깥 여백 제거 */
  box-sizing: border-box; /* 패딩이 너비에 포함되도록 설정 */
  
  border: 1px solid #D1E2D6;
  border-radius: 24px;
  box-shadow: 0 10px 30px rgba(44, 62, 80, 0.08);
  letter-spacing: -0.04em;
}

/* 헤더 타이틀 */
h3 {
  font-size: 1.3rem;
  font-weight: 800;
  color: #27AE60;
  margin: 0 0 16px 0;
  display: flex;
  align-items: center;
  gap: 8px;
}

/* 섹션 공통 */
.list-box {
  margin-bottom: 32px;
  display: grid;
  /* 💡 핵심: 카드가 최소 220px을 유지하되, 공간이 남으면 자동으로 늘어나며 채웁니다 */
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 16px;
}


/* 검색 상자 */
.search-box {
  position: relative;
}

.search-box input[type="text"] {
  width: 100%;
  padding: 16px 20px;
  font-size: 1rem;
  font-weight: 600;
  background-color: #FFFFFF;
  border: 2px solid #D1E2D6;
  border-radius: 14px;
  color: #2C3E50;
  outline: none;
  box-sizing: border-box;
  transition: all 0.2s ease;
  letter-spacing: -0.02em;
}

.search-box input[type="text"]:focus {
  border-color: #27AE60;
  box-shadow: 0 0 0 3px rgba(39, 174, 96, 0.15);
}

.search-box p {
  margin: 12px 0 0 4px;
  font-size: 0.9rem;
  color: #7F8C8D;
}

.search-box strong {
  color: #27AE60;
  font-weight: 600;
  margin-left: 4px;
}

.weather-card {
  padding: 20px;
  border-radius: 18px;
  cursor: pointer;
  border: 2px solid transparent;
  display: grid;
  grid-template-columns: 1fr auto; /* 왼쪽은 텍스트 영역, 오른쪽은 버튼 영역 */
  align-items: center;
  gap: 12px;
  
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow: 0 4px 12px rgba(44, 62, 80, 0.03);
  
  /* 세로 방향 정렬로 변경 */
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 10px;
  
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow: 0 4px 12px rgba(44, 62, 80, 0.03);
}


.weather-card:has(.hot) {
  background-color: #FFF0ED; 
  border: 1px solid #FFD6CE;
}
.weather-card:has(.hot):hover {
  transform: translateY(-4px);
  border-color: #FF6347; 
  box-shadow: 0 12px 24px rgba(255, 99, 71, 0.15);
}
.weather-card:has(.hot) h4 {
  color: #D32F2F;
}

.weather-card:has(.cool) {
  background-color: #EBF5FF; 
  border: 1px solid #CCE5FF;
}
.weather-card:has(.cool):hover {
  transform: translateY(-4px);
  border-color: #4A90E2;
  box-shadow: 0 12px 24px rgba(74, 144, 226, 0.15);
}
.weather-card:has(.cool) h4 {
  color: #1A5276;
}


.weather-card:has(.cold) {
  background-color: #F0F4F8; 
  border: 1px solid #D5E1ED;
}
.weather-card:has(.cold):hover {
  transform: translateY(-4px);
  border-color: #5D6D7E; 
  box-shadow: 0 12px 24px rgba(93, 109, 126, 0.15);
}
.weather-card:has(.cold) h4 {
  color: #396fa8;
}


.weather-card h4 {
  grid-column: 1 / 2;
  font-size: 1.15rem;
  font-weight: 800;
  margin: 0;
  white-space: normal;  /* 강제 한 줄 고정을 풀고 자동 줄바꿈 허용 */
  word-break: keep-all; /* 단어 단위로 깔끔하게 쪼개져서 내려가도록 설정 */
}

.weather-card p {
  grid-column: 1 / 2;
  font-size: 0.95rem;
  font-weight: 500;
  margin: 2px 0;
  color: #566573;
}


.badge {
  grid-column: 1 / 2;
  justify-self: start;
  padding: 6px 12px;
  font-size: 0.75rem;
  font-weight: 700;
  border-radius: 8px;
  letter-spacing: -0.01em;
}

.badge.hot {
  background-color: #FF6347;
  color: #FFFFFF;
}

.badge.cool {
  background-color: #4A90E2;
}

.badge.cold {
  background-color: #06407e;
  color: #FFFFFF;
}


.btn-detail {
  grid-column: 2 / 3;
  grid-row: 1 / 4;
  margin-top: auto;
  
  background-color: #27AE60;
  color: #FFFFFF;
  border: none;
  padding: 12px 16px;
  border-radius: 12px;
  font-weight: 800;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.15s ease;
  white-space: nowrap;
}

.btn-detail:hover {
  background-color: #219653;
  transform: scale(1);
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

/* 스타일 태그에 아이콘 크기 지정 여부를 확인하세요 */
.weather-icon {
  width: 42px;         /* 아이콘의 가로 크기 */
  height: 42px;        /* 아이콘의 세로 크기 */
  margin-top: 6px;     /* 글자와 아이콘 사이의 간격 */
  display: inline-block;
  object-fit: contain;
}

</style>

