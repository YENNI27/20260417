<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

// 狀態變數
const isCountdown = ref(false)
const showZhuyin = ref(false)
const isDarkMode = ref(false)
const isRedWarning = ref(false) // 新增：最後一分鐘警告狀態

const currentTime = ref('')
const countdownSeconds = ref(3600) // 預設倒數 60 分鐘

// 使用者輸入資料
const examData = ref({
  startTime: '',
  endTime: '',
  subject: '',
  proctor: ''
})

// 時間更新與倒數計時邏輯
let timerInterval = null
const decorations = ref([]) // 儲存飄落裝飾物的陣列

const updateTime = () => {
  const now = new Date()
  currentTime.value = now.toLocaleTimeString('zh-TW', { hour12: false })
  
  if (isCountdown.value) {
    if (countdownSeconds.value > 0) {
      countdownSeconds.value--
    }
    // 倒數且剩餘小於等於 60 秒時，觸發紅字警告
    isRedWarning.value = countdownSeconds.value <= 60 && countdownSeconds.value > 0
  } else {
    isRedWarning.value = false
  }
}

onMounted(() => {
  updateTime()
  timerInterval = setInterval(updateTime, 1000)
  
  // 初始化背景飄落的可愛裝飾 (櫻花、星星、閃亮亮等)
  const symbols = ['🌸', '⭐', '✨', '🫧', '🎀']
  for (let i = 0; i < 25; i++) {
    decorations.value.push({
      id: i,
      symbol: symbols[Math.floor(Math.random() * symbols.length)],
      left: `${Math.random() * 100}vw`,
      animationDuration: `${Math.random() * 15 + 10}s`, // 隨機落下時間 10~25秒
      animationDelay: `-${Math.random() * 25}s`, // 負延遲讓動畫一開始就散佈在畫面上
      fontSize: `${Math.random() * 1.5 + 1}rem`,
      opacity: Math.random() * 0.4 + 0.3
    })
  }
})

onUnmounted(() => {
  if (timerInterval) clearInterval(timerInterval)
})

// 格式化倒數時間
const formatCountdown = (secs) => {
  const h = Math.floor(secs / 3600).toString().padStart(2, '0')
  const m = Math.floor((secs % 3600) / 60).toString().padStart(2, '0')
  const s = (secs % 60).toString().padStart(2, '0')
  return `${h}:${m}:${s}`
}

// 按鈕功能切換
const toggleTimer = () => { isCountdown.value = !isCountdown.value }
const toggleZhuyin = () => { showZhuyin.value = !showZhuyin.value }
const toggleTheme = () => { isDarkMode.value = !isDarkMode.value }
</script>

<template>
  <div :class="['app-wrapper', { 'dark-theme': isDarkMode, 'show-zhuyin': showZhuyin }]">
    
    <!-- 背景飄落裝飾 -->
    <div class="decorations-container">
      <span
        v-for="item in decorations"
        :key="item.id"
        class="decoration-item"
        :style="{
          left: item.left,
          animationDuration: item.animationDuration,
          animationDelay: item.animationDelay,
          fontSize: item.fontSize,
          opacity: item.opacity
        }"
      >{{ item.symbol }}</span>
    </div>

    <div class="container">
      
      <!-- 標題與連結區塊 -->
      <header class="header">
        <a href="https://www.et.tku.edu.tw/" target="_blank" class="btn link-btn">
          <ruby>淡<rt>ㄉㄢˋ</rt></ruby>
          <ruby>江<rt>ㄐㄧㄤ</rt></ruby>
          <ruby>教<rt>ㄐㄧㄠˋ</rt></ruby>
          <ruby>育<rt>ㄩˋ</rt></ruby>
          <ruby>科<rt>ㄎㄜ</rt></ruby>
          <ruby>技<rt>ㄐㄧˋ</rt></ruby>
          <ruby>學<rt>ㄒㄩㄝˊ</rt></ruby>
          <ruby>系<rt>ㄒㄧˋ</rt></ruby>
        </a>
        <div class="title-container">
          <h1 class="title">
            <ruby>互<rt>ㄏㄨˋ</rt></ruby>
            <ruby>動<rt>ㄉㄨㄥˋ</rt></ruby>
            <ruby>程<rt>ㄔㄥˊ</rt></ruby>
            <ruby>式<rt>ㄕˋ</rt></ruby>
            <ruby>設<rt>ㄕㄜˋ</rt></ruby>
            <ruby>計<rt>ㄐㄧˋ</rt></ruby>
          </h1>
          <span class="subtitle">413730143</span>
        </div>
      </header>

      <!-- 功能按鈕區塊 -->
      <div class="controls">
        <button @click="toggleTimer" class="btn">
          <ruby>切<rt>ㄑㄧㄝ</rt></ruby>
          <ruby>換<rt>ㄏㄨㄢˋ</rt></ruby>
          <ruby>時<rt>ㄕˊ</rt></ruby>
          <ruby>間<rt>ㄐㄧㄢ</rt></ruby>
          /
          <ruby>倒<rt>ㄉㄠˋ</rt></ruby>
          <ruby>數<rt>ㄕㄨˇ</rt></ruby>
        </button>
        <button @click="toggleZhuyin" class="btn">
          <ruby>切<rt>ㄑㄧㄝ</rt></ruby>
          <ruby>換<rt>ㄏㄨㄢˋ</rt></ruby>
          <ruby>注<rt>ㄓㄨˋ</rt></ruby>
          <ruby>音<rt>ㄧㄣ</rt></ruby>
        </button>
        <button @click="toggleTheme" class="btn">
          <ruby>切<rt>ㄑㄧㄝ</rt></ruby>
          <ruby>換<rt>ㄏㄨㄢˋ</rt></ruby>
          <ruby>主<rt>ㄓㄨˇ</rt></ruby>
          <ruby>題<rt>ㄊㄧˊ</rt></ruby>
        </button>
      </div>

      <!-- 時間顯示區塊 -->
      <div class="time-display">
        <h2 :class="['time-text', { 'warning-red': isRedWarning }]">
          {{ isCountdown ? formatCountdown(countdownSeconds) : currentTime }}
        </h2>
        <p class="time-label">
          <template v-if="isCountdown">
            <ruby>剩<rt>ㄕㄥˋ</rt></ruby>
            <ruby>餘<rt>ㄩˊ</rt></ruby>
            <ruby>時<rt>ㄕˊ</rt></ruby>
            <ruby>間<rt>ㄐㄧㄢ</rt></ruby>
          </template>
          <template v-else>
            <ruby>現<rt>ㄒㄧㄢˋ</rt></ruby>
            <ruby>在<rt>ㄗㄞˋ</rt></ruby>
            <ruby>時<rt>ㄕˊ</rt></ruby>
            <ruby>間<rt>ㄐㄧㄢ</rt></ruby>
          </template>
        </p>
      </div>

      <!-- 資料輸入區塊 -->
      <div class="input-section">
        <div class="input-group">
          <label>
            <ruby>時<rt>ㄕˊ</rt></ruby>
            <ruby>間<rt>ㄐㄧㄢ</rt></ruby>
          </label>
          <div class="time-range">
            <input v-model="examData.startTime" type="time" />
            <span class="time-separator">~</span>
            <input v-model="examData.endTime" type="time" />
          </div>
        </div>
        <div class="input-group">
          <label>
            <ruby>科<rt>ㄎㄜ</rt></ruby>
            <ruby>目<rt>ㄇㄨˋ</rt></ruby>
          </label>
          <input v-model="examData.subject" type="text" placeholder="輸入考試科目" />
        </div>
        <div class="input-group">
          <label>
            <ruby>監<rt>ㄐㄧㄢ</rt></ruby>
            <ruby>考<rt>ㄎㄠˇ</rt></ruby>
            <ruby>老<rt>ㄌㄠˇ</rt></ruby>
            <ruby>師<rt>ㄕ</rt></ruby>
          </label>
          <input v-model="examData.proctor" type="text" placeholder="輸入監考老師姓名" />
        </div>
      </div>

      <!-- 資料顯示區塊 -->
      <div class="display-section">
        <div class="info-card">
          <p>
            <strong>
              <ruby>時<rt>ㄕˊ</rt></ruby>
              <ruby>間<rt>ㄐㄧㄢ</rt></ruby>：
            </strong> 
            <span v-if="examData.startTime && examData.endTime">{{ examData.startTime }} ~ {{ examData.endTime }}</span>
            <span v-else>尚未輸入</span>
          </p>
          <p>
            <strong>
              <ruby>科<rt>ㄎㄜ</rt></ruby>
              <ruby>目<rt>ㄇㄨˋ</rt></ruby>：
            </strong> 
            {{ examData.subject || '尚未輸入' }}
          </p>
          <p>
            <strong>
              <ruby>監<rt>ㄐㄧㄢ</rt></ruby>
              <ruby>考<rt>ㄎㄠˇ</rt></ruby>
              <ruby>老<rt>ㄌㄠˇ</rt></ruby>
              <ruby>師<rt>ㄕ</rt></ruby>：
            </strong> 
            {{ examData.proctor || '尚未輸入' }}
          </p>
        </div>
      </div>

    </div>
  </div>
</template>

<style>
/* === 全域重置與基礎設定 === */
body {
  margin: 0;
  font-family: 'Quicksand', 'Nunito', 'PingFang TC', '微軟正黑體', 'Comic Sans MS', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* === 可愛佈景主題設定 (馬卡龍漸層) === */
.app-wrapper {
  min-height: 100vh;
  background: radial-gradient(circle at top left, #fff0f3, #f3e6ff);
  color: #6d597a;
  transition: background 0.5s ease, color 0.5s ease;
  padding: 4vh 5vw;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}
.app-wrapper.dark-theme {
  background: radial-gradient(circle at top left, #2b214a, #1a1625);
  color: #e5b9e8;
}

/* === 注音符號設定 (防位移、可愛直式排版) === */
.show-zhuyin ruby {
  display: inline-flex;
  align-items: center;
}
rt {
  display: block; /* 始終佔據空間，防止位移 */
  writing-mode: vertical-rl;
  text-orientation: upright; /* 確保音標保持直立，不跟著旋轉 */
  font-size: 0.75em; /* 顯著放大注音 */
  margin-left: 6px;
  color: #ff748c;
  font-weight: 800;
  line-height: 1;
  letter-spacing: 2px;
  opacity: 0; 
  visibility: hidden;
  transition: all 0.3s cubic-bezier(0.68, -0.55, 0.265, 1.55); /* 可愛彈跳動畫 */
  transform: translateX(-10px);
}
.show-zhuyin rt {
  opacity: 1;
  visibility: visible;
  transform: translateX(0);
}
.dark-theme .show-zhuyin rt {
  color: #f4a261;
}

/* === 背景飄落動畫 === */
.decorations-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none; /* 確保不影響點擊輸入 */
  z-index: 0;
  overflow: hidden;
}
.decoration-item {
  position: absolute;
  top: -10vh;
  user-select: none;
  animation: fall linear infinite;
}
@keyframes fall {
  0% { transform: translateY(-10vh) rotate(0deg); }
  100% { transform: translateY(110vh) rotate(360deg); }
}

/* === 容器與版面佈局 === */
.container {
  max-width: 1400px;
  margin: 0 auto;
  width: 100%;
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  position: relative;
  z-index: 1; /* 確保輸入框與按鈕顯示在裝飾物上方 */
}

/* === 標題與連結區域 (使用 Grid 解決重疊問題) === */
.header {
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  margin-bottom: 60px;
  padding: 10px 0;
  width: 100%;
}
.title {
  grid-column: 2;
  justify-self: center;
  font-size: 2.6rem;
  margin: 0;
  text-align: center;
  font-weight: 800;
  letter-spacing: 4px;
  text-shadow: 0 10px 30px rgba(0,0,0,0.05);
}
.dark-theme .title {
  text-shadow: 0 10px 30px rgba(0,0,0,0.5);
}
.link-btn {
  grid-column: 1;
  justify-self: start;
  text-decoration: none;
  font-size: 1.1rem;
  font-weight: 600;
  padding: 14px 28px;
  background: rgba(255, 255, 255, 0.4);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.6);
  border-radius: 12px;
  color: #334155;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.05);
  transition: all 0.4s cubic-bezier(0.16, 1, 0.3, 1);
}
.link-btn:hover {
  background: rgba(255, 255, 255, 0.8);
  transform: translateY(-3px) scale(1.02);
  box-shadow: 0 12px 30px rgba(0, 0, 0, 0.1);
}
.dark-theme .link-btn {
  background: rgba(30, 41, 59, 0.5);
  border: 1px solid rgba(255, 255, 255, 0.1);
  color: #f1f5f9;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
}
.dark-theme .link-btn:hover {
  background: rgba(30, 41, 59, 0.8);
  border-color: rgba(255, 255, 255, 0.2);
}

/* === 按鈕樣式 (高級霧面玻璃感) === */
.controls {
  display: flex;
  justify-content: center;
  gap: 24px;
  flex-wrap: wrap;
  margin-bottom: 60px;
}
.btn {
  padding: 16px 32px;
  font-size: 1.15rem;
  font-weight: 600;
  cursor: pointer;
  background: rgba(255, 255, 255, 0.4);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.6);
  color: #334155;
  border-radius: 16px;
  box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.05);
  transition: all 0.4s cubic-bezier(0.16, 1, 0.3, 1);
  display: inline-flex;
  align-items: center;
}
.btn:hover { 
  transform: translateY(-4px);
  background: rgba(255, 255, 255, 0.9);
  box-shadow: 0 15px 35px -5px rgba(0, 0, 0, 0.1);
}
.dark-theme .btn {
  background: rgba(30, 41, 59, 0.5);
  border: 1px solid rgba(255, 255, 255, 0.1);
  color: #f1f5f9;
  box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.3);
}
.dark-theme .btn:hover { 
  background: rgba(30, 41, 59, 0.9);
  border-color: rgba(255, 255, 255, 0.2);
}

/* === 時間顯示 (極簡無邊框質感) === */
.time-display {
  text-align: center;
  margin-bottom: 60px;
  padding: 60px 30px;
  background: rgba(255, 255, 255, 0.3);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.5);
  border-radius: 32px;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.03);
}
.dark-theme .time-display { 
  background: rgba(15, 23, 42, 0.4); 
  border-color: rgba(255, 255, 255, 0.05);
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.2);
}
.time-text {
  font-size: 8.5rem;
  margin: 0;
  font-family: 'SF Pro Display', 'Helvetica Neue', monospace;
  font-weight: 200; /* 極細字體展現高級感 */
  letter-spacing: 8px;
  text-shadow: 0 15px 25px rgba(0, 0, 0, 0.05);
  transition: color 0.3s;
}
.dark-theme .time-text {
  text-shadow: 0 15px 25px rgba(0, 0, 0, 0.4);
}
/* 最後一分鐘紅字警告 */
.time-text.warning-red {
  color: #ef4444;
  font-weight: 400;
  text-shadow: 0 0 20px rgba(239, 68, 68, 0.4);
}
.time-label {
  font-size: 1.4rem;
  margin: 20px 0 0 0;
  opacity: 0.6;
  font-weight: 500;
  letter-spacing: 4px;
}

/* === 輸入區塊 === */
.input-section {
  display: flex;
  gap: 30px;
  margin-bottom: 50px;
}
.input-group {
  display: flex;
  flex-direction: column;
  flex: 1;
  gap: 12px;
}
.input-group label {
  font-size: 1.2rem;
  font-weight: 800;
  color: #ff8fab;
  padding-left: 5px;
}
.dark-theme .input-group label { color: #e5b9e8; }

.time-range {
  display: flex;
  align-items: center;
  gap: 10px;
}
.time-separator {
  font-weight: 900;
  color: #ffafcc;
  font-size: 1.5rem;
}
.dark-theme .time-separator { color: #e5b9e8; }

input {
  width: 100%;
  padding: 15px 20px;
  font-size: 1.15rem;
  border: 4px solid #a2d2ff;
  border-radius: 25px;
  background: #fff;
  box-shadow: 0 5px 0 #a2d2ff;
  outline: none;
  transition: all 0.3s ease;
  color: #6d597a;
  font-family: inherit;
  font-weight: 700;
}
input:focus { 
  transform: translateY(3px);
  border-color: #ffafcc;
  box-shadow: 0 2px 0 #ffafcc;
}
.dark-theme input {
  background: #2b214a;
  border-color: #5d4982;
  box-shadow: 0 5px 0 #5d4982;
  color: #e5b9e8;
}
.dark-theme input:focus { 
  border-color: #9d4edd;
  box-shadow: 0 2px 0 #9d4edd;
}

/* === 顯示資訊區塊 === */
.display-section {
  display: flex;
  justify-content: center;
}
.info-card {
  width: 100%;
  padding: 40px;
  background: #fff;
  border: 4px solid #ffafcc;
  border-radius: 24px;
  box-shadow: 0 8px 0 #ffafcc;
  display: flex;
  flex-direction: column;
  gap: 15px;
}
.dark-theme .info-card {
  background: #3a2e5d;
  border-color: #5d4982;
  box-shadow: 0 8px 0 #5d4982;
}
.info-card p {
  margin: 0;
  font-size: 1.4rem;
  display: flex;
  align-items: center;
  color: #6d597a;
  font-weight: 700;
}
.dark-theme .info-card p { color: #e5b9e8; }
.info-card strong {
  min-width: 130px;
  color: #ff748c;
  font-weight: 900;
}
.dark-theme .info-card strong { color: #f4a261; }

/* === 手機版排版調整 (響應式) === */
@media (max-width: 1100px) {
  .header {
    display: flex;
    flex-direction: column;
    gap: 25px;
  }
  .link-btn {
    align-self: center; /* 手機版將按鈕置中 */
  }
  .title {
    font-size: 2.5rem;
  }
  .subtitle {
    font-size: 1rem;
  }
  .time-text {
    font-size: 5.5rem;
  }
  .input-section {
    flex-direction: column;
  }
  .info-card p {
    flex-direction: column;
    align-items: flex-start;
    gap: 8px;
  }
}
</style>
