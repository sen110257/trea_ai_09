<template>
  <div class="app-container">
    <!-- 背景音乐控制 -->
    <div class="music-control" @click="toggleMusic">
      <span class="music-icon">{{ isPlaying ? '🔊' : '🔇' }}</span>
    </div>

    <div class="container">
      <!-- 顶部标题和昵称区 -->
      <div class="card fade-in header-section">
        <h1 class="main-title">
          <span class="heart">❤️</span>
          我们的纪念日
          <span class="heart">❤️</span>
        </h1>
        <div class="couple-names">
          <span class="name">{{ boyName }}</span>
          <span class="love-icon">💕</span>
          <span class="name">{{ girlName }}</span>
        </div>
      </div>

      <!-- 情侣头像展示区 -->
      <div class="card fade-in avatar-section">
        <div class="avatar-container">
          <div class="avatar-wrapper">
            <img :src="boyAvatar" alt="男生头像" class="avatar" />
          </div>
          <div class="heart-middle">
            <span class="heart big-heart">💖</span>
          </div>
          <div class="avatar-wrapper">
            <img :src="girlAvatar" alt="女生头像" class="avatar" />
          </div>
        </div>
        <div class="together-days">
          <span class="days-number">{{ togetherDays }}</span>
          <span class="days-text">天</span>
        </div>
      </div>

      <!-- 恋爱统计区 -->
      <div class="card fade-in stats-section">
        <h2 class="section-title">恋爱统计</h2>
        <div class="stats-grid">
          <div class="stat-item">
            <div class="stat-number">{{ totalDays }}</div>
            <div class="stat-label">总天数</div>
          </div>
          <div class="stat-item">
            <div class="stat-number">{{ totalMonths }}</div>
            <div class="stat-label">总月数</div>
          </div>
          <div class="stat-item">
            <div class="stat-number">{{ totalWeeks }}</div>
            <div class="stat-label">总周数</div>
          </div>
        </div>
      </div>

      <!-- 实时倒计时模块 -->
      <div class="card fade-in countdown-section">
        <h2 class="section-title">距离下次纪念日</h2>
        <div class="countdown-display">
          <div class="countdown-item">
            <span class="countdown-number">{{ countdown.days }}</span>
            <span class="countdown-label">天</span>
          </div>
          <div class="countdown-item">
            <span class="countdown-number">{{ countdown.hours }}</span>
            <span class="countdown-label">时</span>
          </div>
          <div class="countdown-item">
            <span class="countdown-number">{{ countdown.minutes }}</span>
            <span class="countdown-label">分</span>
          </div>
          <div class="countdown-item">
            <span class="countdown-number">{{ countdown.seconds }}</span>
            <span class="countdown-label">秒</span>
          </div>
        </div>
        <div class="next-anniversary">
          <span class="next-title">下一个纪念日：</span>
          <span class="next-name">{{ nextAnniversary.name }}</span>
          <span class="next-date">{{ nextAnniversary.date }}</span>
        </div>
      </div>

      <!-- 纪念日清单 -->
      <div class="card fade-in anniversaries-section">
        <h2 class="section-title">重要纪念日</h2>
        <div class="anniversary-list">
          <div
            v-for="(anniversary, index) in anniversaries"
            :key="index"
            class="anniversary-item"
            :class="{
              'highlight': anniversary.isHighlight,
              'approaching': anniversary.isApproaching
            }"
          >
            <div class="anniversary-icon">{{ anniversary.icon }}</div>
            <div class="anniversary-info">
              <div class="anniversary-name">{{ anniversary.name }}</div>
              <div class="anniversary-date">{{ anniversary.date }}</div>
            </div>
            <div class="anniversary-countdown">
              <div v-if="anniversary.daysLeft === 0" class="today">今天</div>
              <div v-else class="days-left">{{ anniversary.daysLeft }}天</div>
            </div>
            <div v-if="anniversary.isApproaching" class="approaching-badge">
              临近提醒
            </div>
          </div>
        </div>
      </div>

      <!-- 情话语录轮播 -->
      <div class="card fade-in quotes-section">
        <h2 class="section-title">甜蜜情话</h2>
        <div class="quotes-container">
          <div class="quote-item">
            <span class="quote-mark">"</span>
            <span class="quote-text">{{ currentQuote }}</span>
            <span class="quote-mark">"</span>
          </div>
        </div>
        <div class="quote-indicators">
          <span
            v-for="(_, index) in loveQuotes"
            :key="index"
            class="indicator"
            :class="{ active: currentQuoteIndex === index }"
          ></span>
        </div>
      </div>

      <!-- 多宫格相册 -->
      <div class="card fade-in gallery-section">
        <h2 class="section-title">甜蜜相册</h2>
        <div class="gallery-grid">
          <div
            v-for="(photo, index) in galleryPhotos"
            :key="index"
            class="gallery-item"
          >
            <img :src="photo" alt="相册图片" class="gallery-image" />
          </div>
        </div>
      </div>

      <!-- 底部签名区 -->
      <div class="card fade-in signature-section">
        <div class="signature-text">
          <p>{{ signature }}</p>
        </div>
        <div class="signature-date">
          <span>始于 {{ startDate }}</span>
        </div>
        <div class="footer-hearts">
          <span class="heart">💕</span>
          <span class="heart">💗</span>
          <span class="heart">💖</span>
          <span class="heart">💗</span>
          <span class="heart">💕</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

// 基础数据配置
const boyName = ref('小哥哥')
const girlName = ref('小仙女')
const startDate = ref('2020-02-14')

// 头像（使用在线图片服务生成）
const boyAvatar = ref('https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=handsome%20young%20man%20portrait%20romantic%20soft%20pink%20background%20anime%20style&image_size=square')
const girlAvatar = ref('https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=beautiful%20young%20woman%20portrait%20romantic%20soft%20pink%20background%20anime%20style&image_size=square')

// 背景音乐状态
const isPlaying = ref(false)
let audio = null

// 倒计时数据
const countdown = ref({
  days: 0,
  hours: 0,
  minutes: 0,
  seconds: 0
})

// 当前时间
const currentTime = ref(new Date())

// 情话语录
const loveQuotes = ref([
  '你是我一生只会遇见一次的惊喜。',
  '我不想做你生命的插曲，只想做你生命最完美的结局。',
  '遇见你之前，我没有想过结婚；遇见你之后，结婚我没有想过别人。',
  '你是我的今天，以及所有的明天。',
  '我能想到最浪漫的事，就是和你一起慢慢变老。',
  '你是我枯水年纪里的一场雨，你来的酣畅淋漓，我淋的一病不起。',
  '你是我所有的少女情怀和心之所向。',
  '我对你的喜欢，就像日子一样，只增不减。'
])

const currentQuoteIndex = ref(0)
const currentQuote = computed(() => loveQuotes.value[currentQuoteIndex.value])

// 相册图片
const galleryPhotos = ref([
  'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=romantic%20couple%20walking%20on%20beach%20sunset%20pink%20sky%20romantic%20mood&image_size=square',
  'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=cute%20couple%20having%20coffee%20in%20cafe%20cozy%20warm%20atmosphere%20romantic&image_size=square',
  'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20under%20cherry%20blossom%20trees%20spring%20pink%20petals%20romantic%20date&image_size=square',
  'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20watching%20fireworks%20at%20night%20colorful%20lights%20romantic%20moment&image_size=square',
  'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20hiking%20on%20mountain%20top%20beautiful%20view%20adventure%20together&image_size=square',
  'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20dancing%20in%20rain%20romantic%20city%20street%20night%20lights&image_size=square',
  'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20cooking%20together%20in%20kitchen%20warm%20home%20cozy%20happy&image_size=square',
  'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20reading%20books%20together%20library%20quiet%20peaceful%20romantic&image_size=square',
  'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=couple%20at%20amusement%20park%20ferris%20wheel%20colorful%20fun%20happy%20moment&image_size=square'
])

// 底部签名
const signature = ref('愿岁月可回首，且以深情共白头。')

// 纪念日数据
const anniversaries = computed(() => {
  const today = new Date()
  const start = new Date(startDate.value)
  
  const anniversaryList = [
    {
      name: '相识纪念日',
      date: startDate.value,
      icon: '🌸',
      originalDate: start
    },
    {
      name: '表白纪念日',
      date: '2020-03-14',
      icon: '💌',
      originalDate: new Date('2020-03-14')
    },
    {
      name: '恋爱纪念日',
      date: startDate.value,
      icon: '💕',
      originalDate: start
    },
    {
      name: '男票生日',
      date: '1998-06-15',
      icon: '🎂',
      originalDate: new Date('1998-06-15'),
      isBirthday: true
    },
    {
      name: '女票生日',
      date: '1999-09-20',
      icon: '🎁',
      originalDate: new Date('1999-09-20'),
      isBirthday: true
    },
    {
      name: '情人节',
      date: `${today.getFullYear()}-02-14`,
      icon: '🌹',
      originalDate: new Date(`${today.getFullYear()}-02-14`)
    },
    {
      name: '七夕节',
      date: `${today.getFullYear()}-08-20`,
      icon: '✨',
      originalDate: new Date(`${today.getFullYear()}-08-20`)
    }
  ]
  
  return anniversaryList.map(item => {
    let anniversaryDate = new Date(item.originalDate)
    
    if (item.isBirthday) {
      anniversaryDate = new Date(today.getFullYear(), anniversaryDate.getMonth(), anniversaryDate.getDate())
      if (anniversaryDate < today) {
        anniversaryDate = new Date(today.getFullYear() + 1, anniversaryDate.getMonth(), anniversaryDate.getDate())
      }
    } else {
      const thisYear = new Date(today.getFullYear(), anniversaryDate.getMonth(), anniversaryDate.getDate())
      if (thisYear < today) {
        anniversaryDate = new Date(today.getFullYear() + 1, anniversaryDate.getMonth(), anniversaryDate.getDate())
      } else {
        anniversaryDate = thisYear
      }
    }
    
    const diffTime = anniversaryDate - today
    const daysLeft = Math.ceil(diffTime / (1000 * 60 * 60 * 24))
    
    return {
      ...item,
      daysLeft: daysLeft >= 0 ? daysLeft : 0,
      isHighlight: daysLeft === 0,
      isApproaching: daysLeft > 0 && daysLeft <= 7
    }
  }).sort((a, b) => a.daysLeft - b.daysLeft)
})

// 下一个纪念日
const nextAnniversary = computed(() => {
  const upcoming = anniversaries.value.find(a => a.daysLeft >= 0)
  return upcoming || { name: '无', date: '' }
})

// 恋爱总天数
const togetherDays = computed(() => {
  const start = new Date(startDate.value)
  const today = new Date()
  const diffTime = Math.abs(today - start)
  return Math.floor(diffTime / (1000 * 60 * 60 * 24))
})

// 总月数
const totalMonths = computed(() => {
  const start = new Date(startDate.value)
  const today = new Date()
  let months = (today.getFullYear() - start.getFullYear()) * 12
  months += today.getMonth() - start.getMonth()
  return months
})

// 总周数
const totalWeeks = computed(() => {
  return Math.floor(togetherDays.value / 7)
})

// 总天数
const totalDays = computed(() => {
  return togetherDays.value
})

// 计算倒计时
const calculateCountdown = () => {
  const today = new Date()
  const next = anniversaries.value.find(a => a.daysLeft >= 0)
  
  if (!next) return
  
  let nextDate
  if (next.isBirthday) {
    const original = new Date(next.originalDate)
    nextDate = new Date(today.getFullYear(), original.getMonth(), original.getDate(), 0, 0, 0)
    if (nextDate <= today) {
      nextDate = new Date(today.getFullYear() + 1, original.getMonth(), original.getDate(), 0, 0, 0)
    }
  } else {
    const original = new Date(next.originalDate)
    nextDate = new Date(today.getFullYear(), original.getMonth(), original.getDate(), 0, 0, 0)
    if (nextDate <= today) {
      nextDate = new Date(today.getFullYear() + 1, original.getMonth(), original.getDate(), 0, 0, 0)
    }
  }
  
  const diff = nextDate - today
  
  if (diff <= 0) {
    countdown.value = { days: 0, hours: 0, minutes: 0, seconds: 0 }
    return
  }
  
  const days = Math.floor(diff / (1000 * 60 * 60 * 24))
  const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60))
  const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60))
  const seconds = Math.floor((diff % (1000 * 60)) / 1000)
  
  countdown.value = { days, hours, minutes, seconds }
}

// 切换音乐
const toggleMusic = () => {
  isPlaying.value = !isPlaying.value
  
  if (isPlaying.value) {
    if (!audio) {
      audio = new Audio('https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3')
      audio.loop = true
    }
    audio.play().catch(e => console.log('音频播放失败:', e))
  } else {
    if (audio) {
      audio.pause()
    }
  }
}

// 定时器
let countdownInterval = null
let quoteInterval = null

onMounted(() => {
  calculateCountdown()
  
  countdownInterval = setInterval(() => {
    currentTime.value = new Date()
    calculateCountdown()
  }, 1000)
  
  quoteInterval = setInterval(() => {
    currentQuoteIndex.value = (currentQuoteIndex.value + 1) % loveQuotes.value.length
  }, 5000)
})

onUnmounted(() => {
  if (countdownInterval) {
    clearInterval(countdownInterval)
  }
  if (quoteInterval) {
    clearInterval(quoteInterval)
  }
  if (audio) {
    audio.pause()
  }
})
</script>

<style scoped>
.app-container {
  min-height: 100vh;
  position: relative;
}

/* 音乐控制 */
.music-control {
  position: fixed;
  top: 20px;
  right: 20px;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: var(--white);
  box-shadow: var(--shadow);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 1000;
  transition: all 0.3s ease;
}

.music-control:hover {
  transform: scale(1.1);
}

.music-icon {
  font-size: 24px;
  animation: spin 3s linear infinite;
  animation-play-state: paused;
}

.music-control:active .music-icon {
  animation-play-state: running;
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

/* 头部区域 */
.header-section {
  text-align: center;
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.9), rgba(255, 227, 236, 0.9));
}

.main-title {
  font-size: 24px;
  font-weight: 700;
  color: var(--primary-color);
  margin-bottom: 15px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.couple-names {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
  font-size: 18px;
}

.name {
  font-weight: 600;
  color: var(--text-color);
  padding: 8px 20px;
  background: linear-gradient(135deg, var(--secondary-color), var(--accent-color));
  border-radius: 20px;
  color: white;
  box-shadow: var(--shadow);
}

.love-icon {
  font-size: 24px;
  animation: heartbeat 1.5s ease-in-out infinite;
}

/* 头像区域 */
.avatar-section {
  text-align: center;
}

.avatar-container {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
  margin-bottom: 20px;
}

.avatar-wrapper {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  padding: 4px;
  background: linear-gradient(135deg, var(--primary-color), var(--accent-color));
  box-shadow: var(--shadow);
}

.avatar {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  object-fit: cover;
}

.heart-middle {
  display: flex;
  align-items: center;
  justify-content: center;
}

.big-heart {
  font-size: 32px;
  animation: heartbeat 1.2s ease-in-out infinite;
}

.together-days {
  display: flex;
  align-items: baseline;
  justify-content: center;
  gap: 5px;
}

.days-number {
  font-size: 48px;
  font-weight: 700;
  color: var(--primary-color);
  text-shadow: 2px 2px 4px rgba(255, 107, 157, 0.3);
}

.days-text {
  font-size: 20px;
  font-weight: 600;
  color: var(--text-light);
}

/* 统计区域 */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
}

.stat-item {
  text-align: center;
  padding: 15px;
  background: linear-gradient(135deg, rgba(255, 107, 157, 0.1), rgba(255, 179, 193, 0.2));
  border-radius: 12px;
  transition: transform 0.3s ease;
}

.stat-item:hover {
  transform: translateY(-3px);
}

.stat-number {
  font-size: 32px;
  font-weight: 700;
  color: var(--primary-color);
  margin-bottom: 5px;
}

.stat-label {
  font-size: 14px;
  color: var(--text-light);
  font-weight: 500;
}

/* 倒计时区域 */
.countdown-display {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 20px;
}

.countdown-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: linear-gradient(135deg, var(--primary-color), var(--accent-color));
  padding: 15px 12px;
  border-radius: 12px;
  min-width: 65px;
  box-shadow: var(--shadow);
}

.countdown-number {
  font-size: 28px;
  font-weight: 700;
  color: var(--white);
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
}

.countdown-label {
  font-size: 12px;
  color: rgba(255, 255, 255, 0.9);
  margin-top: 5px;
  font-weight: 500;
}

.next-anniversary {
  text-align: center;
  font-size: 14px;
  color: var(--text-light);
}

.next-title {
  font-weight: 500;
}

.next-name {
  font-weight: 600;
  color: var(--primary-color);
  margin: 0 5px;
}

.next-date {
  font-weight: 500;
  color: var(--text-color);
}

/* 纪念日清单 */
.anniversary-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.anniversary-item {
  display: flex;
  align-items: center;
  padding: 15px;
  background: linear-gradient(135deg, rgba(255, 229, 236, 0.5), rgba(255, 255, 255, 0.8));
  border-radius: 12px;
  position: relative;
  transition: all 0.3s ease;
  border: 2px solid transparent;
}

.anniversary-item:hover {
  transform: translateX(5px);
  box-shadow: var(--shadow);
}

.anniversary-item.highlight {
  background: linear-gradient(135deg, var(--primary-color), var(--accent-color));
  border-color: var(--primary-color);
}

.anniversary-item.highlight .anniversary-name,
.anniversary-item.highlight .anniversary-date,
.anniversary-item.highlight .days-left,
.anniversary-item.highlight .today {
  color: var(--white);
}

.anniversary-item.approaching {
  border-color: var(--secondary-color);
  animation: pulse 2s ease-in-out infinite;
}

@keyframes pulse {
  0%, 100% {
    box-shadow: 0 0 0 0 rgba(255, 107, 157, 0.4);
  }
  50% {
    box-shadow: 0 0 0 10px rgba(255, 107, 157, 0);
  }
}

.anniversary-icon {
  font-size: 28px;
  margin-right: 15px;
}

.anniversary-info {
  flex: 1;
}

.anniversary-name {
  font-size: 16px;
  font-weight: 600;
  color: var(--text-color);
  margin-bottom: 3px;
}

.anniversary-date {
  font-size: 13px;
  color: var(--text-light);
}

.anniversary-countdown {
  text-align: right;
}

.days-left {
  font-size: 18px;
  font-weight: 700;
  color: var(--primary-color);
}

.today {
  font-size: 18px;
  font-weight: 700;
  color: var(--primary-color);
  animation: heartbeat 1s ease-in-out infinite;
}

.approaching-badge {
  position: absolute;
  top: -8px;
  right: 10px;
  background: var(--primary-color);
  color: var(--white);
  font-size: 10px;
  padding: 3px 8px;
  border-radius: 10px;
  font-weight: 600;
}

/* 情话语录 */
.quotes-container {
  min-height: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 15px;
}

.quote-item {
  text-align: center;
  padding: 0 20px;
}

.quote-mark {
  font-size: 24px;
  color: var(--secondary-color);
  font-weight: 700;
}

.quote-text {
  font-size: 16px;
  color: var(--text-color);
  line-height: 1.8;
  font-weight: 500;
  font-style: italic;
  margin: 0 10px;
}

.quote-indicators {
  display: flex;
  justify-content: center;
  gap: 8px;
}

.indicator {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--secondary-color);
  transition: all 0.3s ease;
}

.indicator.active {
  width: 24px;
  border-radius: 4px;
  background: var(--primary-color);
}

/* 相册区域 */
.gallery-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}

.gallery-item {
  aspect-ratio: 1;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: var(--shadow);
  transition: transform 0.3s ease;
}

.gallery-item:hover {
  transform: scale(1.05);
}

.gallery-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

/* 签名区域 */
.signature-section {
  text-align: center;
  background: linear-gradient(135deg, rgba(255, 107, 157, 0.1), rgba(255, 179, 193, 0.3));
}

.signature-text {
  margin-bottom: 15px;
}

.signature-text p {
  font-size: 18px;
  font-weight: 600;
  color: var(--primary-color);
  font-style: italic;
  line-height: 1.6;
}

.signature-date {
  font-size: 14px;
  color: var(--text-light);
  margin-bottom: 15px;
}

.footer-hearts {
  display: flex;
  justify-content: center;
  gap: 10px;
}

.footer-hearts .heart {
  font-size: 20px;
  animation: heartbeat 1.5s ease-in-out infinite;
}

.footer-hearts .heart:nth-child(2) {
  animation-delay: 0.2s;
}

.footer-hearts .heart:nth-child(3) {
  animation-delay: 0.4s;
}

.footer-hearts .heart:nth-child(4) {
  animation-delay: 0.6s;
}

.footer-hearts .heart:nth-child(5) {
  animation-delay: 0.8s;
}

/* 响应式适配 */
@media (max-width: 480px) {
  .main-title {
    font-size: 20px;
  }
  
  .name {
    font-size: 14px;
    padding: 6px 15px;
  }
  
  .avatar-wrapper {
    width: 80px;
    height: 80px;
  }
  
  .days-number {
    font-size: 36px;
  }
  
  .stat-number {
    font-size: 24px;
  }
  
  .countdown-item {
    padding: 12px 10px;
    min-width: 55px;
  }
  
  .countdown-number {
    font-size: 22px;
  }
  
  .gallery-grid {
    grid-template-columns: repeat(3, 1fr);
    gap: 8px;
  }
}

@media (max-width: 360px) {
  .countdown-display {
    gap: 8px;
  }
  
  .countdown-item {
    padding: 10px 8px;
    min-width: 50px;
  }
  
  .countdown-number {
    font-size: 18px;
  }
  
  .countdown-label {
    font-size: 11px;
  }
}
</style>
