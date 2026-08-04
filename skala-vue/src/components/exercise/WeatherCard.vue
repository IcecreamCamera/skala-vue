<script setup>
import { computed } from 'vue'
import { useConfigStore } from '../../stores/configStore'
// 1. 상위로부터 단방향 주입받을 객체 데이터 규격 검수 (매크로)
const props = defineProps({
  cityItem: {
    type: Object,
    required: true,
  },
})

// 2. 상위로 송신할 두 가지 경로의 커스텀 이벤트 식별자 등록 (매크로)
const emit = defineEmits(['select-card', 'click-detail'])

/* const showDetail = (cityName, status, temp) => {
  window.alert(`${cityName}의 현재 날씨는 [${status}] 상태이며 기온은 ${temp}°C 입니다.`)
} */
const configStore = useConfigStore()
// 🔥 [핵심 미션] 스토어의 상태값이 'fahrenheit'일 때만 화씨 공식 적용 연산
const displayTemp = computed(() => {
  const rawTemp = props.cityItem.temp // 기본 원본 데이터는 섭씨 숫자
  if (configStore.unit === 'fahrenheit') {
    return Math.round((rawTemp * 9) / 5 + 32) // 화씨 변환 연산
  }
  return rawTemp // 'celsius'일 때는 원본 그대로 반환
})



</script>

<template>
  <div class="weather-card" @click="emit('select-card', `${cityItem.name}이(가) 선택되었습니다.`)">
    <h4>

      {{ cityItem.name }} <br /> 
      <span class="status-text">{{ cityItem.status }}</span> <br />
      <!-- 🟢 src 앞에 콜론(:) 필수, 중간 경로와 올바른 변수 매핑 적용 -->
      <img 
        v-if="cityItem.icon"
        :src="'https://openweathermap.org/img/wn/' + cityItem.icon + '@2x.png'" 
        :alt="cityItem.status" 
        class="weather-icon" 
      />

    </h4>
    
    <p>현재 기온: {{ displayTemp }}{{ configStore.unitSymbol }}</p>
    <span v-if="cityItem.temp >= 25" class="badge hot">🔥 더움</span>
    <span v-else-if="cityItem.temp <25 && cityItem.temp >= 5" class = "badge cool">💨 선선함</span>
    <span v-else class = "badge cold">❄️ 추움</span>  
    <!-- v-else-if 이용 추움 추가 -->
    <button class="btn-detail" @click.stop="emit('click-detail', cityItem.id)">상세</button>
    <!--"showDetail(cityItem.name, cityItem.status, cityItem.temp)" window alert용-->
  </div>
</template>


<style scoped>
.weather-card {
  padding: 20px;
  border-radius: 18px;
  cursor: pointer;
  border: 2px solid transparent;
  display: grid;
  grid-template-columns: 1fr auto;
  align-items: center;
  gap: 12px;
  
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow: 0 4px 12px rgba(44, 62, 80, 0.03);

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
  white-space: normal;  
  word-break: keep-all;
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

.weather-icon {
  width: 84px;         
  height: 84px;        
  display: inline-block;
  object-fit: contain;
  vertical-align: middle; 
  margin: 8px auto;      

  /* 💡 밝은 배경용 강한 그림자 조합 */
  /* 첫 번째 라인은 형태를 꽉 잡아주고, 두 번째 라인은 넓고 진하게 퍼지도록 설계했습니다 */
  filter: drop-shadow(0 3px 4px rgba(15, 23, 42, 0.25)) 
          drop-shadow(0 8px 16px rgba(15, 23, 42, 0.18));

  animation: floatWeather 3s ease-in-out infinite;
}

@keyframes floatWeather {
  0% { transform: translateY(0px); }
  50% { transform: translateY(-8px); }
  100% { transform: translateY(0px); }
}


</style>