<script setup>
import { useConfigStore } from '@/stores/configStore'
const configStore = useConfigStore()
</script>

<template>
  <div class="weather-unit-container" @click="configStore.toggleUnit">
    <div class="switch-track" :class="{ 'is-active': configStore.unit === 'fahrenheit' }">
      <div class="switch-handle">
        <span class="mini-unit">{{ configStore.unit === 'celsius' ? '℃' : '℉' }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* 1. 전체 컨테이너 */
.weather-unit-container {
  margin-left: auto; 
  display: inline-flex; 
  align-items: center; 
  background: rgba(255, 255, 255, 0.4);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  padding: 5px; 
  border-radius: 30px;
  border: 1px solid rgba(255, 255, 255, 0.6);
  box-shadow: 0 4px 15px rgba(75, 101, 132, 0.05);
  cursor: pointer;
  user-select: none;
  transition: transform 0.2s ease;
}

/* 2. 🌟 토글 스위치 트랙 (레일) */
.switch-track {
  position: relative;
  width: 56px;
  height: 28px;
  /* ⭕ [변경]: 섭씨(기본) 상태일 때 은은하고 예쁜 연분홍/소프트 레드 그라데이션 적용 */
  background: linear-gradient(135deg, #ffbcba 0%, #ff8a85 100%); 
  border-radius: 20px;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
  box-shadow: 0 4px 12px rgba(255, 138, 133, 0.2);
}

/* 🔥 화씨 활성화 시 기존의 시그니처 초록 그라데이션 */
.switch-track.is-active {
  background: linear-gradient(135deg, #a3e635 0%, #10b981 100%);
  box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3);
}

/* 3. 움직이는 원형 손잡이 (Handle) */
.switch-handle {
  position: absolute;
  top: 3px;
  left: 3px;
  width: 22px;
  height: 22px;
  background: #ffffff;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.15);
  transition: transform 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}

/* 화씨 상태일 때 우측 슬라이딩 거리 지정 */
.switch-track.is-active .switch-handle {
  transform: translateX(28px);
}

/* 4. 손잡이 내부 미니 단위 표시 */
.mini-unit {
  font-size: 11px;
  font-weight: 800;
  /* ⭕ [변경]: 기본 섭씨일 때 글자색을 겉 배경과 어울리는 소프트 레드로 지정 */
  color: #ff6b66; 
  transition: color 0.3s;
}

/* 화씨일 때 초록색 글자로 전환 */
.switch-track.is-active .mini-unit {
  color: #10b981;
}

/* 호버 시 부드러운 리액션 효과 */
.weather-unit-container:hover {
  transform: translateY(-1px);
}
.weather-unit-container:hover .switch-track {
  box-shadow: 0 5px 14px rgba(255, 138, 133, 0.35);
}
.weather-unit-container:hover .switch-track.is-active {
  box-shadow: 0 5px 14px rgba(16, 185, 129, 0.4);
}
</style>
