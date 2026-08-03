<script setup>
// 1. 상위로 입력 텍스트를 전달할 커스텀 이벤트 등록 (매크로)
defineEmits(['update-query'])

// 2. 상위로부터 현재 검색 상태 값을 수신 (한글 동기화 상태 유지용)
defineProps({
  currentQuery: {
    type: String,
    default: '',
  },
})
</script>

<template>
  <div class="search-box">
    <h3>🔍 도시 검색</h3>
    <input type="text" :value="currentQuery" @input="$emit('update-query', $event.target.value)" placeholder="검색할 도시 이름 입력" />
    <p>
      검색 중인 도시: <strong>{{ currentQuery }}</strong>
    </p>
  </div>
</template>


<style scoped>
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

</style>