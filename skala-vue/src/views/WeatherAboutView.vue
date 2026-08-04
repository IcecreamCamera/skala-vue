<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

const handleGoHome = () => {
  router.push('/')
}
</script>

<template>
  <div>
    <h3><img src="/svg/info.svg" alt = "소개" /> 서비스 소개</h3>
    <hr />

    <div class="description-box">
      <p>본 앱은 <strong>Vue 3</strong> 및 <strong>Vue Router 4</strong> 기반 제작된 실습용 기상 관측 대시보드 시스템입니다.</p>
      <ul>
        <li><code>components/exercise/</code> 폴더 내부의 독립 부품 연동</li>
        <li>클라이언트 사이드 라우팅을 통한 새로고침 없는 화면 전환</li>
        <li>URL 쿼리 스트링 매핑을 활용한 실시간 검색 상태 동기화</li>
      </ul>
    </div>

    <button @click="handleGoHome" class="home-btn">대시보드 홈으로 이동</button>
  </div>
</template>

<style scoped>
/* 1. 전체 컨테이너: 입체감 있는 네온 링크 그림자 효과 */
.intro-container {
  max-width: 600px;
  margin: 40px auto;
  background: #ffffff;
  padding: 40px;
  border-radius: 24px;
  box-shadow: 0 20px 50px rgba(52, 152, 219, 0.08), 0 4px 12px rgba(0, 0, 0, 0.01);
  border: 1px solid #eef2f5;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
}

/* 2. 헤더 스타일링 */
.header-zone {
  margin-bottom: 30px;
}

.info-badge {
  display: inline-block;
  font-size: 0.7rem;
  font-weight: 800;
  color: #3498db;
  background: #e8f4fc;
  padding: 4px 10px;
  border-radius: 20px;
  letter-spacing: 1px;
  margin-bottom: 8px;
}

h3 {
  display: flex;
  align-items: center; /* 아이콘과 글자 높낮이 맞추기 */
  gap: 8px;            /* 아이콘과 글자 사이 간격 */
  font-size: 1.35rem;   /* 너무 크지 않고 보기 좋은 크기 */
  color: #2c3e50;      /* 신뢰감을 주는 다크 네이비 톤 */
  font-weight: 800;    /* 굵고 명확한 폰트 두께 */
  margin-top: 0;
  margin-bottom: 25px; /* 박스와의 간격 정렬 */
}

h3 img {
  width: 24px;
  height: 24px;
}

/* 3. 설명 박스: 칙칙한 네이비를 빼고, 캡처본 톤에 맞춘 화사한 투명 스카이블루 그라데이션 적용 */
.description-box {
  background: linear-gradient(135deg, #f0f7ff 0%, #e6f2ff 100%);
  border: 1px solid #cce3ff;
  padding: 30px;
  border-radius: 18px;
  margin-bottom: 30px;
  position: relative;
  box-shadow: 0 8px 20px rgba(52, 152, 219, 0.03);
}

/* 텍스트 스타일: 화사한 배경 위에 가독성이 가장 좋은 다크 블루 톤 */
.main-lead {
  font-size: 1.05rem;
  color: #2c3e50; 
  line-height: 1.7;
  margin-top: 0;
  margin-bottom: 25px;
  font-weight: 500;
}

.highlight-text-vue {
  color: #35495e; /* 기존 쨍한 초록 대신 대시보드 톤과 어울리는 딥 블루 고정 */
  font-weight: 800;
  border-bottom: 2px solid #34495e30;
}

.highlight-text-router {
  color: #2c3e50;
  font-weight: 800;
  border-bottom: 2px solid #2c3e5030;
}

/* 4. 화려하지만 깔끔한 체크 리스트 디자인 */
.feature-list {
  list-style: none;
  padding-left: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.feature-list li {
  display: flex;
  align-items: center;
  gap: 14px;
  font-size: 0.95rem;
  color: #4f5d73; /* 눈이 편안한 다크 그레이 */
  font-weight: 500;
  transition: transform 0.2s ease;
}

/* 리스트 항목 마우스 올릴 때 효과 */
.feature-list li:hover {
  transform: translateX(4px);
  color: #2c3e50;
}

/* 체크 아이콘 감싸는 이펙트 상자 */
.bullet-wrapper {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 22px;
  height: 22px;
  background: #3498db; /* 메인 홈 버튼과 통일감을 주는 스카이블루 */
  border-radius: 50%;
  flex-shrink: 0;
}

.bullet-glow {
  position: absolute;
  width: 100%;
  height: 100%;
  background: #3498db;
  border-radius: 50%;
  opacity: 0.15;
  transform: scale(1.3);
  transition: transform 0.2s ease;
}

.feature-list li:hover .bullet-glow {
  transform: scale(1.6);
}

.bullet-icon {
  color: #ffffff; 
  font-size: 0.7rem;
  font-weight: 900;
}

/* 코드 강조 블록 디자인: 캡처본에 있던 이쁜 연노랑 바탕에 오렌지브라운 조합 유지 */
code {
  background: #fff9db; 
  padding: 3px 8px;
  border-radius: 6px;
  color: #d9480f; /* 튀지 않으면서 가독성 높은 차분한 오렌지 레드 */
  font-family: "Fira Code", Consolas, monospace;
  font-size: 0.88rem;
  font-weight: 700;
  border: 1px solid #ffe3e3;
  margin-right: 4px;
}

/* 5. 프리미엄 인라인 그라데이션 버튼 */
.home-btn {
  width: 100%;
  padding: 16px;
  background: linear-gradient(135deg, #3498db 0%, #2980b9 100%);
  color: #ffffff;
  border: none;
  border-radius: 14px;
  font-size: 1.02rem;
  font-weight: 800;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow: 0 8px 25px rgba(52, 152, 219, 0.3);
}

/* 버튼 마우스 호버 시 화려한 모션 이펙트 */
.home-btn:hover {
  background: linear-gradient(135deg, #2980b9 0%, #1f618d 100%);
  box-shadow: 0 12px 30px rgba(41, 128, 185, 0.45);
  transform: translateY(-2px);
}

.home-btn:hover .arrow {
  transform: translateX(4px);
}

.home-btn:active {
  transform: translateY(0);
}

.arrow {
  transition: transform 0.2s ease;
  font-weight: 900;
}
</style>