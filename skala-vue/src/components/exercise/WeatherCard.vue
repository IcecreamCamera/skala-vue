<script setup>
// 1. 상위로부터 단방향 주입받을 객체 데이터 규격 검수 (매크로)
defineProps({
  cityItem: {
    type: Object,
    required: true,
  },
})

// 2. 상위로 송신할 두 가지 경로의 커스텀 이벤트 식별자 등록 (매크로)
const emit = defineEmits(['select-card', 'click-detail'])

const showDetail = (cityName, status, temp) => {
  window.alert(`${cityName}의 현재 날씨는 [${status}] 상태이며 기온은 ${temp}°C 입니다.`)
}

</script>

<template>
  <div class="weather-card" @click="emit('select-card', `${cityItem.name}이 선택되었습니다.`)">
    <h4>
      {{ cityItem.name }} <br /> 
      <span class="status-text">{{ cityItem.status }}</span> <br />
      <!-- 주소 글자는 지우고, 순수한 이미지 태그만 아래에 넣습니다 -->
      <img :src="cityItem.image" :alt="cityItem.status" class="weather-icon" />
    </h4>
    
    <p>현재 기온: {{ cityItem.temp }}°C</p>
    <span v-if="cityItem.temp >= 25" class="badge hot">🔥 더움</span>
    <span v-else-if="cityItem.temp <25 && cityItem.temp >= 5" class = "badge cool">💨 선선함</span>
    <span v-else class = "badge cold">❄️ 추움</span>  
    <!-- v-else-if 이용 추움 추가 -->
    <button class="btn-detail" @click.stop="showDetail(cityItem.name, cityItem.status, cityItem.temp)">상세</button>
  </div>
</template>


<style scoped>
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


/* 스타일 태그에 아이콘 크기 지정 여부를 확인하세요 */
.weather-icon {
  width: 42px;         /* 아이콘의 가로 크기 */
  height: 42px;        /* 아이콘의 세로 크기 */
  margin-top: 6px;     /* 글자와 아이콘 사이의 간격 */
  display: inline-block;
  object-fit: contain;
}


</style>