<script setup>
/* import WeatherMockup from './components/exercise/WeatherMockup.vue'
import WeatherComposition from './components/exercise/WeatherComposition.vue'
import WeatherParent from './components/exercise/WeatherParent.vue' */
import {ref, onMounted } from 'vue'
import UnitToggler from './components/exercise/UnitToggler.vue'

const isDark = ref(false)

const toggleTheme = () => {
  isDark.value = !isDark.value
  const theme = isDark.value ? 'dark' : 'light'
  document.documentElement.setAttribute('data-theme', theme)
  localStorage.setItem('theme', theme)
}

onMounted(() => {
  const savedTheme = localStorage.getItem('theme')
  const systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches

  if (savedTheme === 'dark' || (!savedTheme && systemPrefersDark)) {
    isDark.value = true
    document.documentElement.setAttribute('data-theme', 'dark')
  } else {
    isDark.value = false
    document.documentElement.setAttribute('data-theme', 'light')
  }
})
</script>

<template>
  <!--<div class="app-container">
    <h1>⛅ 과제 1: 날씨 (Mockup)</h1>
    <hr />
    <WeatherMockup />
  </div>
  <div class="app-container">
    <h1>⛅ 과제 2: 날씨 (컴포지션)</h1>
    <hr />
    <WeatherComposition />
  </div>
  <div class="app-container">
    <h1>⛅ 과제 3: 날씨 (컴포넌트)</h1>
    <hr />
    <WeatherParent />
  </div>
  <div class="app-container">
    <h1>⛅ 과제 4: 라우터적용</h1>
    <hr />
    <div class="dashboard-wrapper">
      <nav class="navigation-bar">
        <RouterLink to="/" class="nav-item"><img src="/svg/weather.svg" alt = '날씨' /> 날씨 대시보드</RouterLink>
        <span class="divider">|</span>
        <RouterLink to="/about" class="nav-item"><img src = '/svg/info.svg' alt = "서비스 소개" /> 서비스 소개</RouterLink>
      </nav>
      <main>
        <RouterView />
        <RouterView v-slot="{ Component }">
          <KeepAlive>
            <component :is="Component" />
          </KeepAlive>
        </RouterView>
      </main>
    </div>
  </div>   -->
  
    <!--<h1>⛅ 과제 5: 스토어적용</h1>
    <hr />-->
    <div class="dashboard-wrapper">
      <nav class="navigation-bar">
        <div class = "nav-links">
          <RouterLink to="/" class="nav-item"><img src="/svg/weather.svg" alt = '날씨' /> 날씨 대시보드</RouterLink>
          <span class="divider">|</span>
          <RouterLink to="/about" class="nav-item"> <img src = '/svg/info.svg' alt = "서비스 소개" /> 서비스 소개</RouterLink>
        </div>
        <div class = "toggle-groups">
          <UnitToggler />
          <button @click="toggleTheme" class="theme-toggle-btn">
            <img v-if="isDark" src="/svg/moon.svg" alt="다크 모드" />
            <img v-else src="/svg/sun.svg" alt="라이트 모드" />
          </button>
        </div>
      </nav>
      <main>
        <RouterView />
      </main>
    </div>
</template>

<style>
/* ⚠️ 외부 스타일 파일(예: 버튼 디자인 뭉치)을 이 방 안으로 쏙 가리켜 가져옵니다 */
@import '@/assets/exercise.css';
</style>
