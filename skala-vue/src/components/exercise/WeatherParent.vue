<script setup>
import { ref, computed, watch, watchEffect } from 'vue'
// 1. 컴포넌트 파일명 국룰 표기법(PascalCase) 매칭 수입
import BaseDashboardCard from './BaseDashboardCard.vue'
import SearchBar from './SearchBar.vue'
import WeatherCard from './WeatherCard.vue'

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

const searchQuery = ref('')
const selectedCityInfo = ref('카드를 클릭하거나 검색해 보세요.')

// 기존 핵심 비즈니스 로직(computed, watch)의 소유권은 안전하게 부모 콘텍스트가 격리 유지
const filteredWeatherList = computed(() => {
  const query = searchQuery.value.trim()
  if (!query) return weatherList.value
  return weatherList.value.filter((item) => item.name.includes(query))
})

watch(selectedCityInfo, (newInfo) => {
  console.log(`👁️‍🗨️ [watch 감지] 상태 바 문구가 업데이트되었습니다 -> "${newInfo}"`)
})

watchEffect(() => {
  console.log(`🤖 [watchEffect 자동 호출] 현재 검색어 '${searchQuery.value}'에 매칭되는 API 데이터를 필터링합니다.`)
})


</script>

<template>
  <div class="dashboard-wrapper">
    <BaseDashboardCard>
      <h3><img src = "/svg/search.svg" alt="검색" /> 도시 검색</h3>
      <SearchBar :current-query="searchQuery" @update-query="(val) => (searchQuery = val)" />
    </BaseDashboardCard>

    <BaseDashboardCard>
      <h3><img src = "/svg/city.svg" alt="도시" /> 지역별 날씨 현황</h3>
      <section class="list-box">
        <WeatherCard v-for="item in filteredWeatherList" :key="item.id" :city-item="item" @select-card="(msg) => (selectedCityInfo = msg)" @click-detail="showDetail" />
        <p v-if="filteredWeatherList.length === 0" style="text-align: center; color: #e74c3c; padding: 10px 0">😭 검색 결과와 일치하는 도시가 없습니다.</p>
      </section>
    </BaseDashboardCard>

    <div class="status-bar">
      {{ selectedCityInfo }}
    </div>
  </div>
</template>

<style scoped>

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

.list-box {
  margin-bottom: 32px;
  display: grid;
  /* 💡 핵심: 카드가 최소 220px을 유지하되, 공간이 남으면 자동으로 늘어나며 채웁니다 */
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
