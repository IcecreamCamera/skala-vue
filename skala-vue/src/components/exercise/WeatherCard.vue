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

// 2. 상위로 송신할 커스텀 이벤트 식별자 등록 ('delete-card' 추가)
const emit = defineEmits(['select-card', 'click-detail', 'delete-card'])
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
    <!-- 1. 우측 상단 절대 위치 또는 카드 내부에 배치할 삭제 버튼 (이벤트 전파 차단 .stop 필수) -->
    <button class="btn-delete" @click.stop="emit('delete-card')">✕</button>

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
    <span v-else-if="cityItem.temp < 25 && cityItem.temp >= 5" class="badge cool">💨 선선함</span>
    <span v-else class="badge cold">❄️ 추움</span>  

    <button class="btn-detail" @click.stop="emit('click-detail', cityItem.id)">상세</button>
    
  </div>
</template>


<style scoped>
/* ==========================================================================
   1. 기본 액션 버튼 스타일 (우측 상단 ✕ 삭제 버튼)
   ========================================================================== */
.btn-delete {
  position: absolute;
  top: 14px;
  right: 14px;
  background: none;
  border: none;
  color: #a0aec0;
  font-size: 16px;
  cursor: pointer;
  padding: 4px;
  line-height: 1;
  z-index: 10; /* 날씨 아이콘 둥둥 뜨는 애니메이션보다 위에 보이도록 배치 */
  transition: color 0.2s, transform 0.2s;
}

.btn-delete:hover {
  color: #e74c3c; /* 마우스 올리면 빨간색으로 변경 */
  transform: scale(1.15);
}

/* ==========================================================================
   2. 메인 날씨 카드 레이아웃 (Flex 구조)
   ========================================================================== */
.weather-card {
  position: relative;
  padding: 20px;
  padding-bottom: 20px; 
  min-height: 190px;    
  border-radius: 18px;
  cursor: pointer;
  border: 2px solid transparent;
  box-sizing: border-box;
  
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow: 0 4px 12px rgba(44, 62, 80, 0.03);

  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 10px;
}

/* ==========================================================================
   3. 🟢 [핵심] 상세 버튼 스타일 (조그만 버튼 형태로 우측 하단 강제 안착)
   ========================================================================== */
.btn-detail {
  /* 🟢 relative를 absolute로 바꾸어야 카드의 오른쪽 아래 구석으로 이동합니다 */
  position: absolute;
  bottom: 20px; /* 카드 바닥 테두리로부터 띄울 간격 */
  right: 20px;  /* 카드 오른쪽 테두리로부터 띄울 간격 */

  /* 보내주신 크기 그대로 고정 */
  width: 70px;
  height: 32px;
  
  background-color: #27AE60;
  color: #FFFFFF;
  border: none;
  padding: 10px 16px; 
  border-radius: 12px;
  font-weight: 800;
  font-size: 0.85rem;
  cursor: pointer;
  transition: all 0.15s ease;
  white-space: nowrap; 
}


.btn-detail:hover {
  background-color: #219653;
  transform: scale(1.05); /* 마우스 올렸을 때 살짝 커지는 이펙트 */
}

/* ==========================================================================
   4. 조건부 배경 및 타이틀 컬러 스키마 (기존 디자인 100% 유지)
   ========================================================================== */
/* 🔥 더움 상태 */
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

/* 💨 선선함 상태 */
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

/* ❄️ 추움 상태 */
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

/* ==========================================================================
   5. 내부 텍스트 및 기상 아이콘 요소 (기존 디자인 100% 유지)
   ========================================================================== */
.weather-card h4 {
  font-size: 1.15rem;
  font-weight: 800;
  margin: 0;
  white-space: normal;  
  word-break: keep-all;
}

.status-text {
  font-size: 0.9rem;
  font-weight: 500;
  opacity: 0.8;
}

.weather-card p {
  font-size: 0.95rem;
  font-weight: 500;
  margin: 2px 0;
  color: #566573;
}

/* 배지 공통 및 개별 스타일 */
.badge {
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
  color: #FFFFFF;
}

.badge.cold {
  background-color: #06407e;
  color: #FFFFFF;
}

/* 날씨 아이콘 및 둥둥 뜨는 애니메이션 */
.weather-icon {
  width: 84px;         
  height: 84px;        
  display: inline-block;
  object-fit: contain;
  vertical-align: middle; 
  margin: 8px auto;      

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
