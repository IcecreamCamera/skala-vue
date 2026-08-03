<script setup>
import { ref } from 'vue'

// 4일차 API 연동을 대비한 가상의 백엔드 데이터 배열 (v-for 및 :key 실습용)
const weatherList = ref([
  { id: 'city_01', name: '서울', temp: 24, status: '🌧️비' },
  { id: 'city_02', name: '수원', temp: 28, status: '☀️맑음' },
  { id: 'city_03', name: '부산', temp: 31, status: '⛅구름많음' },
  { id: 'city_04', name: '강릉', temp: 22, status: '☁️흐림'},
  { id: 'city_05', name: '상트페테르부르크', temp: 4, status: '☀️맑음'}
])

// 검색어 및 알림창 제어용 데이터 (v-model 대용 한글 처리 및 이벤트 실습용)
const searchQuery = ref('')
const selectedCityInfo = ref('카드를 클릭하거나 검색해 보세요.')

// 알림 대행 함수 (window 객체 격리 우회)
const showDetail = (cityName, status, temp) => {
  window.alert(`${cityName}의 현재 날씨는 [${status}] 상태이며 기온은 ${temp}°C 입니다.`)
}
</script>

<template>
  <div class="dashboard-wrapper">
    <section class="search-box">
      <h3>🔍 도시 검색(search)</h3>
      <!-- input type="text" v-model="searchQuery" placeholder="검색할 도시 이름 입력" / -->
      <input type="text" :value="searchQuery" @input="(e) => (searchQuery = e.target.value)" placeholder="검색할 도시 이름 입력" />
      <p>
        검색 중인 도시: <strong>{{ searchQuery }}</strong>
      </p>
    </section>

    <section class="list-box">
      <h3>🏙️ 지역별 날씨 현황</h3>

      <div v-for="item in weatherList" :key="item.id" v-show = "item.name.includes(searchQuery)" class="weather-card" @click="selectedCityInfo = `${item.name}이 선택되었습니다.`">
        <h4>{{ item.name }} ({{ item.status }})</h4>
        <p>현재 기온: {{ item.temp }}°C</p>
        <span v-if="item.temp >= 25" class="badge hot">🔥 더움 (25도 이상)</span>
        <span v-else-if="item.temp <25 && item.temp >= 5" class = "badge cool">💨 선선함 (25도 미만 5도 이상)</span>
        <span v-else class = "badge cold">❄️ 추움 (5도 미만)</span>  
        <!-- v-else-if 이용 추움 추가 -->
        <button class="btn-detail" @click.stop="showDetail(item.name, item.status, item.temp)">상세보기</button>
      </div>
      
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
section {
  margin-bottom: 32px;
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
  padding: 24px;
  border-radius: 18px;
  margin-bottom: 16px;
  cursor: pointer;
  border: 2px solid transparent;
  display: grid;
  grid-template-columns: 1fr auto;
  align-items: center;
  gap: 12px;
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
  font-size: 1.2rem;
  font-weight: 800;
  margin: 0;
}

.weather-card p {
  grid-column: 1 / 2;
  font-size: 0.95rem;
  font-weight: 500;
  margin: 4px 0 12px 0;
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
  background-color: #27AE60;
  color: #FFFFFF;
  border: none;
  padding: 14px 18px;
  border-radius: 12px;
  font-weight: 800;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.15s ease;
  white-space: nowrap;
}

.btn-detail:hover {
  background-color: #219653;
  transform: scale(1.03);
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

