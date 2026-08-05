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


.switch-track {
  position: relative;
  width: 56px;
  height: 28px;

  background: linear-gradient(135deg, #ffbcba 0%, #ff8a85 100%); 
  border-radius: 20px;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
  box-shadow: 0 4px 12px rgba(255, 138, 133, 0.2);
}


.switch-track.is-active {
  background: linear-gradient(135deg, #a3e635 0%, #10b981 100%);
  box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3);
}


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


.switch-track.is-active .switch-handle {
  transform: translateX(28px);
}

.mini-unit {
  font-size: 11px;
  font-weight: 800;
  color: #ff6b66; 
  transition: color 0.3s;
}


.switch-track.is-active .mini-unit {
  color: #10b981;
}


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
