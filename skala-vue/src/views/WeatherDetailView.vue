<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useConfigStore } from '@/stores/configStore'
import axios from 'axios'

const route = useRoute()
const router = useRouter()
const configStore = useConfigStore()


const cityData = ref(null)
const isLoading = ref(false)

const cityMapping = {
  city_01: { english: 'Seoul', korean: '서울' },
  city_02: { english: 'Suwon', korean: '수원' },
  city_03: { english: 'Busan', korean: '부산' },
  city_04: { english: 'Gangneung', korean: '강릉'},
  city_05: { english: 'Saint Petersburg', korean: '상트페테르부르크' },
  city_06: { english: 'Tampa', korean: '탬파' },
  city_07: { english: 'Ho Chi Minh City', korean: '호치민' },
  city_08: { english: 'New York', korean: '뉴욕' },
  city_09: { english: 'Helsinki', korean: '헬싱키' },
  city_10: { english: 'Wellington', korean: '웰링턴'}
}


onMounted(async () => {
  const id = route.params.cityId
  // 🔥 1순위: cityMapping (기본 10개 도시) / 2순위: URL 쿼리 파라미터 (검색 추가 도시)
  const targetCity = cityMapping[id] || (
    route.query.q 
      ? { english: route.query.q, korean: route.query.name || route.query.q }
      : null
  )

  if (targetCity) {
    isLoading.value = true
    try {
      const API_KEY = import.meta.env.VITE_API_KEY
      // 🟢 [고도화] 가짜 Mock 객체 대신, 실제 고유 타깃 도시 주소를 정밀 저격 호출
      const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${targetCity.english}&appid=${API_KEY}&units=metric&lang=kr`)

      const raw = response.data
      // 화면 템플릿 구조가 깨지지 않도록 오픈웨더 JSON 알맹이를 정확히 역매핑 유치
      cityData.value = {
        name: targetCity.korean,
        temp: raw.main.temp, // 섭씨 온도 원본 기록
        feels_like: raw.main.feels_like,
        temp_min: raw.main.temp_min,
        temp_max: raw.main.temp_max,
        pressure: raw.main.pressure,
        status: raw.weather[0].description,
        humidity: `${raw.main.humidity}%`,
        wind: `${raw.wind.speed}m/s`,
        icon: raw.weather[0].icon
      }
    } catch (error) {
      console.error('🔴 상세 정보 로딩 중 네트워크 에러 발생:', error)
    } finally {
      isLoading.value = false
    }
  }
})

const displayTemp = computed(() => {
  if (!cityData.value) return 0
  const rawTemp = cityData.value.temp // 원본 섭씨 온도
  if (configStore.unit === 'fahrenheit') {
    return Math.round((rawTemp * 9) / 5 + 32) // 화씨 공식 적용
  }
  return rawTemp // celsius 상태일 땐 원본 반환
})

const weatherClass = computed(() => {
  if (!cityData.value) return ''
  const temp = cityData.value.temp
  if (temp >= 25) return 'hot'
  if (temp < 25 && temp >= 5) return 'cool'
  return 'cold'
})
</script>

<template>
  <div class="detail-container" :class = "weatherClass">
    <h3> <img src = '/svg/graph.svg' alt="지역별"/> 지역별 상세 기상 관측 정보(실시간)</h3>
    <hr />
    <div v-if="isLoading" style="text-align: center; padding: 20px 0; color: #7f8c8d">데이터베이스로부터 상세 정보를 동기화하는 중입니다...</div>

    <div v-if="cityData" class="info-card">
      <h4> <img src='/svg/location.svg' alt="위치" /> 지정 지역: {{ cityData.name }}</h4>
      
      <!-- 좌측 텍스트와 우측 아이콘을 나란히 배치하기 위한 래퍼(Wrapper) 박스 -->
      <div class="weather-content-wrapper">
        <div class="weather-info-text">
          <p>
            실시간 기온: <strong>{{ displayTemp }}{{ configStore.unitSymbol }}</strong>
          </p>
          <p>체감 온도: {{ cityData.feels_like }}{{ configStore.unitSymbol }}</p>
          <p>최저 기온: {{ cityData.temp_min }}{{ configStore.unitSymbol }}</p>
          <p>최고 기온: {{ cityData.temp_max }}{{ configStore.unitSymbol }}</p>
          <p>기상 현황: {{ cityData.status }}</p>
          <p>대기 습도: {{ cityData.humidity }}</p>
          <p>현재 풍속: {{ cityData.wind }}</p>
          <p>기압: {{ cityData.pressure }} hPa</p>
        </div>

        <!-- 📌 동그라미 친 위치: cityData.icon이 존재할 때 실시간 날씨 아이콘 출력 -->
        <div class="weather-icon-box">
          <img 
            v-if="cityData.icon"
            :src="'https://openweathermap.org/img/wn/' + cityData.icon + '@2x.png'" 
            :alt="cityData.status" 
            class="detail-weather-icon" 
          />
        </div>
      </div>
    </div>
    <div v-else>
      <p>해당 지역의 상세 데이터 장부가 존재하지 않거나 에러가 발생했습니다.</p>
    </div>

    <button @click="router.push('/')" class="back-btn">← 메인 대시보드로 돌아가기</button>
  </div>
</template>

<style scoped>
/* 1. 전체 카드 컨테이너: 은은하고 부드러운 화이트 보드 레이아웃 */
.detail-container {
  max-width: 550px;
  margin: 30px auto;
  background: #ebf3ed;
  padding: 30px;
  border-radius: 16px;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.04);
  border: 1px solid #f0f0f0;
}

/* 2. 대제목 정렬 스타일 */
h3 {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 1.3rem;
  color: #1e293b;
  font-weight: 700;
  margin-top: 0;
  margin-bottom: 25px;
}

h3 img {
  width: 24px;
  height: 24px;
}

h4 img {
  width: 24px;
  height: 24px;
}

.detail-container {
  max-width: 550px;
  margin: 30px auto;
  padding: 30px;
  border-radius: 18px;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.03);
  transition: all 0.3s ease; /* 색상이 부드럽게 바뀌도록 애니메이션 */
}

/* 🌟 [WeatherCard와 색상 100% 동기화] */
.detail-container.hot {
  background-color: #FFF0ED; 
  border: 1px solid #FFD6CE;
}
.detail-container.hot h3 { color: #D32F2F; }

.detail-container.cool {
  background-color: #EBF5FF; 
  border: 1px solid #CCE5FF;
}
.detail-container.cool h3 { color: #1A5276; }

.detail-container.cold {
  background-color: #F0F4F8; 
  border: 1px solid #D5E1ED;
}
.detail-container.cold h3 { color: #396fa8; }

/* 내부 info-card는 반투명 화이트를 주면 배경색이 은은하게 비쳐서 훨씬 예쁩니다 */
.info-card {
  padding: 24px;
  border-radius: 14px;
  margin-bottom: 25px;
  border: 1px solid rgba(0, 0, 0, 0.05);
}

/* 2. 🌟 외부가 .hot(분홍)일 때 안쪽 박스 스타일 (살짝 진한 오렌지/살구빛 연출) */
.detail-container.hot .info-card {
  background-color: #FFE5E0; /* 겉 배경(#FFF0ED)보다 살짝 더 진한 톤 */
  border: 1px solid #FFC4B8;
}

/* 3. 🌟 외부가 .cool(푸른색)일 때 안쪽 박스 스타일 (살짝 진한 연하늘빛 연출) */
.detail-container.cool .info-card {
  background-color: #D6EBFF; /* 겉 배경(#EBF5FF)보다 살짝 더 진한 톤 */
  border: 1px solid #B3D7FF;
}

/* 4. 🌟 외부가 .cold(회색)일 때 안쪽 박스 스타일 (살짝 진한 블루그레이 연출) */
.detail-container.cold .info-card {
  background-color: #E1E9F0; /* 겉 배경(#F0F4F8)보다 살짝 더 진한 톤 */
  border: 1px solid #C4D3E3;
}

/* 4. 지정 지역 타이틀 하단 경계선 */
.info-card h4 {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 1.15rem;
  color: #0f172a;
  margin-top: 0;
  margin-bottom: 20px;
  padding-bottom: 12px;
  border-bottom: 1px solid #cbd5e1;
}

.info-card h4 img {
  width: 22px;
  height: 22px;
}

/* 5. 정보 텍스트: 항목은 왼쪽, 수치는 오른쪽 정렬 효과 */
.info-card p {
  font-size: 0.95rem;
  color: #475569;
  margin: 14px 0;
  display: flex;
  align-items: center;
}

/* 데이터 수치 디자인 강조 */
.info-card p strong {
  color: #0f172a;
  font-weight: 600;
  font-size: 1.05rem;
}

/* 데이터가 없을 때 띄우는 경고창 */
.error-card {
  padding: 20px;
  background: #fef2f2;
  border: 1px solid #fee2e2;
  border-radius: 12px;
  color: #ef4444;
  text-align: center;
  margin-bottom: 25px;
}

/* 6. 메인으로 돌아가기 버튼 디자인 */
.back-btn {
  display: inline-flex;
  align-items: center;
  width: 100%;
  justify-content: center;
  padding: 12px;
  
  color: #ffffff;
  border: none;
  border-radius: 10px;
  font-size: 0.95rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
}
.detail-container.hot .back-btn {background: #FF6347; }

.detail-container.cool .back-btn {background: #4A90E2; }

.detail-container.cold .back-btn {background: #06407e; }


.back-btn:hover {
  background: #1e293b;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(30, 41, 59, 0.15);
}

.weather-content-wrapper {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
}

.weather-info-text {
  flex: 1;
}

/* 📌 아이콘을 감싸는 박스와 크기 지정 */
.weather-icon-box {
  display: flex;
  justify-content: center;
  align-items: center;
  padding-right: 20px; /* 우측 여백 조정 */
}

.detail-weather-icon {
  width: 130px;        /* 상세 페이지에 맞게 카드보다 더 시원하고 크게 설정 */
  height: 130px;
  object-fit: contain;
  filter: drop-shadow(0 3px 4px rgba(15, 23, 42, 0.25)) 
          drop-shadow(0 8px 16px rgba(15, 23, 42, 0.18)); /* 아이콘에 부드러운 그림자 효과 */
  animation: floatWeather 3s ease-in-out infinite;
}
@keyframes floatWeather {
  0% { transform: translateY(0px); }
  50% { transform: translateY(-8px); }
  100% { transform: translateY(0px); }
}
</style>