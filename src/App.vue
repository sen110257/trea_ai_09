<template>
  <div class="app-container">
    <!-- 背景音乐控制 -->
    <div 
      class="music-control" 
      @click="toggleMusic"
      @touchstart="handleTouchStart"
      @touchend="handleTouchEnd"
    >
      <span class="music-icon">{{ isPlaying ? '🎵' : '🔇' }}</span>
      <div v-if="isPlaying" class="music-waves">
        <span class="wave"></span>
        <span class="wave"></span>
        <span class="wave"></span>
      </div>
    </div>

    <div class="container">
      <!-- 顶部标题和昵称区 -->
      <div class="card fade-in header-section">
        <h1 class="main-title">
          <span class="title-decoration">✦</span>
          <span class="title-text">我们的纪念日</span>
          <span class="title-decoration">✦</span>
        </h1>
        <div class="couple-names">
          <div class="name-tag boy">
            <span class="name-icon">👦</span>
            <span class="name">{{ boyName }}</span>
          </div>
          <div class="love-connector">
            <span class="heart large-heart">💕</span>
          </div>
          <div class="name-tag girl">
            <span class="name-icon">👧</span>
            <span class="name">{{ girlName }}</span>
          </div>
        </div>
      </div>

      <!-- 情侣头像展示区 -->
      <div class="card fade-in avatar-section">
        <div class="avatar-container">
          <div class="avatar-wrapper boy-avatar">
            <div class="avatar-placeholder" v-if="!boyAvatarLoaded">
              <span class="placeholder-icon">👦</span>
            </div>
            <img 
              :src="boyAvatar" 
              alt="男生头像" 
              class="avatar"
              :class="{ 'avatar-loaded': boyAvatarLoaded }"
              @load="boyAvatarLoaded = true"
              @error="handleBoyAvatarError"
            />
          </div>
          <div class="heart-center">
            <div class="heart-ring">
              <span class="heart beating-heart">💖</span>
            </div>
          </div>
          <div class="avatar-wrapper girl-avatar">
            <div class="avatar-placeholder" v-if="!girlAvatarLoaded">
              <span class="placeholder-icon">👧</span>
            </div>
            <img 
              :src="girlAvatar" 
              alt="女生头像" 
              class="avatar"
              :class="{ 'avatar-loaded': girlAvatarLoaded }"
              @load="girlAvatarLoaded = true"
              @error="handleGirlAvatarError"
            />
          </div>
        </div>
        <div class="together-badge">
          <span class="together-label">我们已经在一起</span>
          <div class="days-display">
            <span class="days-number">{{ togetherDays }}</span>
            <span class="days-unit">天</span>
          </div>
        </div>
      </div>

      <!-- 恋爱统计区 -->
      <div class="card fade-in stats-section">
        <h2 class="section-title">恋爱时光</h2>
        <div class="stats-grid">
          <div class="stat-card" @click="handleStatClick">
            <div class="stat-icon">📅</div>
            <div class="stat-value">{{ totalDays }}</div>
            <div class="stat-label">总天数</div>
          </div>
          <div class="stat-card" @click="handleStatClick">
            <div class="stat-icon">🌙</div>
            <div class="stat-value">{{ totalMonths }}</div>
            <div class="stat-label">总月数</div>
          </div>
          <div class="stat-card" @click="handleStatClick">
            <div class="stat-icon">✨</div>
            <div class="stat-value">{{ totalWeeks }}</div>
            <div class="stat-label">总周数</div>
          </div>
        </div>
      </div>

      <!-- 实时倒计时模块 -->
      <div class="card fade-in countdown-section">
        <h2 class="section-title">距离下次纪念日</h2>
        <div class="countdown-wrapper">
          <div class="countdown-display">
            <div class="countdown-unit">
              <div class="countdown-box">
                <span class="countdown-number">{{ formatNumber(countdown.days) }}</span>
              </div>
              <span class="countdown-label">天</span>
            </div>
            <div class="countdown-separator">
              <span class="separator-dot">:</span>
            </div>
            <div class="countdown-unit">
              <div class="countdown-box">
                <span class="countdown-number">{{ formatNumber(countdown.hours) }}</span>
              </div>
              <span class="countdown-label">时</span>
            </div>
            <div class="countdown-separator">
              <span class="separator-dot">:</span>
            </div>
            <div class="countdown-unit">
              <div class="countdown-box">
                <span class="countdown-number">{{ formatNumber(countdown.minutes) }}</span>
              </div>
              <span class="countdown-label">分</span>
            </div>
            <div class="countdown-separator">
              <span class="separator-dot">:</span>
            </div>
            <div class="countdown-unit">
              <div class="countdown-box">
                <span class="countdown-number">{{ formatNumber(countdown.seconds) }}</span>
              </div>
              <span class="countdown-label">秒</span>
            </div>
          </div>
        </div>
        <div class="next-anniversary-info" @click="handleAnniversaryClick">
          <div class="next-icon">{{ nextAnniversary.icon }}</div>
          <div class="next-details">
            <span class="next-name">{{ nextAnniversary.name }}</span>
            <span class="next-date">{{ nextAnniversary.date }}</span>
          </div>
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
              'is-today': anniversary.isHighlight,
              'is-approaching': anniversary.isApproaching,
              'is-far': !anniversary.isHighlight && !anniversary.isApproaching
            }"
            @click="handleAnniversaryItemClick(anniversary)"
            @touchstart="handleTouchStart"
            @touchend="handleTouchEnd"
          >
            <div class="anniversary-left">
              <div class="anniversary-icon-wrapper" :class="{
                'icon-highlight': anniversary.isHighlight,
                'icon-approaching': anniversary.isApproaching
              }">
                <span class="anniversary-icon">{{ anniversary.icon }}</span>
              </div>
              <div class="anniversary-info">
                <div class="anniversary-name">{{ anniversary.name }}</div>
                <div class="anniversary-date">{{ anniversary.date }}</div>
              </div>
            </div>
            <div class="anniversary-right">
              <div class="days-count" :class="{
                'days-today': anniversary.isHighlight,
                'days-soon': anniversary.isApproaching
              }">
                <template v-if="anniversary.isHighlight">
                  <span class="today-text">今天</span>
                  <span class="sparkle">✨</span>
                </template>
                <template v-else>
                  <span class="days-num">{{ anniversary.daysLeft }}</span>
                  <span class="days-text">天</span>
                </template>
              </div>
              <div v-if="anniversary.isApproaching" class="approaching-tag">
                <span class="tag-icon">💫</span>
                <span class="tag-text">即将到来</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 情话语录轮播 -->
      <div class="card fade-in quotes-section">
        <h2 class="section-title">甜蜜情话</h2>
        <div 
          class="quotes-slider"
          ref="quotesSliderRef"
          @touchstart="handleQuoteTouchStart"
          @touchmove="handleQuoteTouchMove"
          @touchend="handleQuoteTouchEnd"
          @click="handleQuoteClick"
        >
          <div 
            class="quotes-track" 
            :style="{ transform: `translateX(${quoteTranslateX}px)` }"
          >
            <div
              v-for="(quote, index) in loveQuotes"
              :key="index"
              class="quote-slide"
            >
              <div class="quote-content">
                <span class="quote-mark left">❝</span>
                <p class="quote-text">{{ quote }}</p>
                <span class="quote-mark right">❞</span>
              </div>
            </div>
          </div>
        </div>
        <div class="quote-controls">
          <button 
            class="quote-btn prev" 
            @click="prevQuote"
            @touchstart.prevent
          >
            ‹
          </button>
          <div class="quote-indicators">
            <span
              v-for="(_, index) in loveQuotes"
              :key="index"
              class="indicator-dot"
              :class="{ active: currentQuoteIndex === index }"
              @click="goToQuote(index)"
            ></span>
          </div>
          <button 
            class="quote-btn next" 
            @click="nextQuote"
            @touchstart.prevent
          >
            ›
          </button>
        </div>
      </div>

      <!-- 多宫格相册 -->
      <div class="card fade-in gallery-section">
        <h2 class="section-title">甜蜜相册</h2>
        <div class="gallery-container">
          <div class="gallery-tabs">
            <button 
              class="tab-btn" 
              :class="{ active: galleryViewMode === 'grid' }"
              @click="galleryViewMode = 'grid'"
            >
              📷 网格
            </button>
            <button 
              class="tab-btn" 
              :class="{ active: galleryViewMode === 'slider' }"
              @click="galleryViewMode = 'slider'"
            >
              🎠 轮播
            </button>
          </div>

          <!-- 网格视图 -->
          <div v-if="galleryViewMode === 'grid'" class="gallery-grid-view">
            <div
              v-for="(photo, index) in galleryPhotos"
              :key="index"
              class="gallery-grid-item"
              @click="openGalleryPreview(index)"
            >
              <div class="photo-placeholder" v-if="!photoLoaded[index]">
                <span class="photo-icon">🖼️</span>
              </div>
              <img
                :src="photo"
                :alt="'相册图片' + (index + 1)"
                class="gallery-photo"
                :class="{ 'photo-loaded': photoLoaded[index] }"
                @load="handlePhotoLoad(index)"
                @error="handlePhotoError(index)"
                loading="lazy"
              />
              <div class="photo-overlay">
                <span class="view-icon">👁️</span>
              </div>
            </div>
          </div>

          <!-- 轮播视图 -->
          <div v-else class="gallery-slider-view">
            <div 
              class="gallery-slider-wrapper"
              ref="gallerySliderRef"
              @touchstart="handleGalleryTouchStart"
              @touchmove="handleGalleryTouchMove"
              @touchend="handleGalleryTouchEnd"
            >
              <div 
                class="gallery-track"
                :style="{ transform: `translateX(${galleryTranslateX}px)` }"
              >
                <div
                  v-for="(photo, index) in galleryPhotos"
                  :key="index"
                  class="gallery-slide"
                >
                  <div class="slide-placeholder" v-if="!photoLoaded[index]">
                    <span class="slide-icon">🖼️</span>
                    <span class="slide-index">{{ index + 1 }} / {{ galleryPhotos.length }}</span>
                  </div>
                  <img
                    :src="photo"
                    :alt="'相册图片' + (index + 1)"
                    class="slide-photo"
                    :class="{ 'slide-loaded': photoLoaded[index] }"
                    @load="handlePhotoLoad(index)"
                    @error="handlePhotoError(index)"
                  />
                  <div class="slide-caption">
                    <span class="caption-text">甜蜜瞬间 #{{ index + 1 }}</span>
                  </div>
                </div>
              </div>
            </div>
            <div class="gallery-indicators">
              <span
                v-for="(_, index) in galleryPhotos"
                :key="index"
                class="gallery-dot"
                :class="{ active: currentGalleryIndex === index }"
                @click="goToGallery(index)"
              ></span>
            </div>
            <div class="gallery-nav">
              <button class="nav-btn prev" @click="prevGallery">
                ‹
              </button>
              <span class="gallery-counter">{{ currentGalleryIndex + 1 }} / {{ galleryPhotos.length }}</span>
              <button class="nav-btn next" @click="nextGallery">
                ›
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- 底部签名区 -->
      <div class="card fade-in signature-section">
        <div class="signature-decoration">
          <span class="deco-line"></span>
          <span class="deco-heart">💝</span>
          <span class="deco-line"></span>
        </div>
        <div class="signature-content">
          <p class="signature-text">{{ signature }}</p>
        </div>
        <div class="signature-footer">
          <div class="start-date">
            <span class="date-label">故事开始于</span>
            <span class="date-value">{{ startDate }}</span>
          </div>
          <div class="footer-hearts">
            <span class="mini-heart">🤍</span>
            <span class="mini-heart">💕</span>
            <span class="mini-heart">💗</span>
            <span class="mini-heart">💖</span>
            <span class="mini-heart">💗</span>
            <span class="mini-heart">💕</span>
            <span class="mini-heart">🤍</span>
          </div>
        </div>
      </div>

      <!-- 底部安全区域 -->
      <div class="safe-area-bottom"></div>
    </div>

    <!-- 相册预览弹窗 -->
    <div 
      v-if="showGalleryPreview" 
      class="gallery-preview-modal"
      @click="closeGalleryPreview"
    >
      <div class="preview-container" @click.stop>
        <button class="close-btn" @click="closeGalleryPreview">×</button>
        <img 
          :src="galleryPhotos[previewIndex]" 
          :alt="'预览图片'"
          class="preview-image"
        />
        <div class="preview-info">
          <span class="preview-index">{{ previewIndex + 1 }} / {{ galleryPhotos.length }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue'

// 基础数据配置
const boyName = ref('温柔小哥哥')
const girlName = ref('可爱小仙女')
const startDate = ref('2020-02-14')

// 头像加载状态
const boyAvatarLoaded = ref(false)
const girlAvatarLoaded = ref(false)

// 备用头像（使用更稳定的在线图片服务）
const boyAvatar = ref('https://picsum.photos/seed/coupleboy1/200/200')
const girlAvatar = ref('https://picsum.photos/seed/couplegirl1/200/200')

// 头像错误处理
const handleBoyAvatarError = () => {
  boyAvatar.value = 'https://picsum.photos/seed/boybackup/200/200'
}

const handleGirlAvatarError = () => {
  girlAvatar.value = 'https://picsum.photos/seed/girlbackup/200/200'
}

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

// 情话语录
const loveQuotes = ref([
  '你是我一生只会遇见一次的惊喜。',
  '我不想做你生命的插曲，只想做你生命最完美的结局。',
  '遇见你之前，我没有想过结婚；遇见你之后，结婚我没有想过别人。',
  '你是我的今天，以及所有的明天。',
  '我能想到最浪漫的事，就是和你一起慢慢变老。',
  '你是我枯水年纪里的一场雨，你来的酣畅淋漓，我淋的一病不起。',
  '你是我所有的少女情怀和心之所向。',
  '我对你的喜欢，就像日子一样，只增不减。',
  '春风十里，不及相遇有你；晴空万里，不及心中有你。',
  '人世间有百媚千红，唯独你是我情之所钟。'
])

const currentQuoteIndex = ref(0)
const quoteTranslateX = ref(0)
let quoteStartX = 0
let quoteCurrentX = 0
let quoteIsDragging = false

const quotesSliderRef = ref(null)

// 相册配置
const galleryViewMode = ref('slider')
const currentGalleryIndex = ref(0)
const galleryTranslateX = ref(0)
const photoLoaded = ref([])
const showGalleryPreview = ref(false)
const previewIndex = ref(0)

let galleryStartX = 0
let galleryCurrentX = 0
let galleryIsDragging = false

const gallerySliderRef = ref(null)

// 相册图片（使用稳定的picsum服务，带seed确保一致性）
const galleryPhotos = ref([
  'https://picsum.photos/seed/love1/400/400',
  'https://picsum.photos/seed/love2/400/400',
  'https://picsum.photos/seed/love3/400/400',
  'https://picsum.photos/seed/love4/400/400',
  'https://picsum.photos/seed/love5/400/400',
  'https://picsum.photos/seed/love6/400/400',
  'https://picsum.photos/seed/love7/400/400',
  'https://picsum.photos/seed/love8/400/400',
  'https://picsum.photos/seed/love9/400/400'
])

// 初始化图片加载状态
const initPhotoLoaded = () => {
  photoLoaded.value = galleryPhotos.value.map(() => false)
}

initPhotoLoaded()

const handlePhotoLoad = (index) => {
  photoLoaded.value[index] = true
}

const handlePhotoError = (index) => {
  galleryPhotos.value[index] = `https://picsum.photos/seed/backup${index}/400/400`
}

// 相册预览
const openGalleryPreview = (index) => {
  previewIndex.value = index
  showGalleryPreview.value = true
}

const closeGalleryPreview = () => {
  showGalleryPreview.value = false
}

// 相册滑动控制
const handleGalleryTouchStart = (e) => {
  galleryIsDragging = true
  galleryStartX = e.touches[0].clientX
  galleryCurrentX = galleryTranslateX.value
}

const handleGalleryTouchMove = (e) => {
  if (!galleryIsDragging) return
  const diff = e.touches[0].clientX - galleryStartX
  const maxDrag = window.innerWidth * 0.3
  
  if (currentGalleryIndex.value === 0 && diff > 0) {
    galleryTranslateX.value = galleryCurrentX + diff * 0.5
  } else if (currentGalleryIndex.value === galleryPhotos.value.length - 1 && diff < 0) {
    galleryTranslateX.value = galleryCurrentX + diff * 0.5
  } else {
    galleryTranslateX.value = galleryCurrentX + diff
  }
}

const handleGalleryTouchEnd = () => {
  if (!galleryIsDragging) return
  galleryIsDragging = false
  
  const diff = galleryTranslateX.value - galleryCurrentX
  const threshold = 50
  
  if (diff > threshold && currentGalleryIndex.value > 0) {
    prevGallery()
  } else if (diff < -threshold && currentGalleryIndex.value < galleryPhotos.value.length - 1) {
    nextGallery()
  } else {
    updateGalleryTranslate()
  }
}

const updateGalleryTranslate = () => {
  const sliderWidth = gallerySliderRef.value?.offsetWidth || window.innerWidth
  galleryTranslateX.value = -currentGalleryIndex.value * sliderWidth
}

const prevGallery = () => {
  if (currentGalleryIndex.value > 0) {
    currentGalleryIndex.value--
    updateGalleryTranslate()
  }
}

const nextGallery = () => {
  if (currentGalleryIndex.value < galleryPhotos.value.length - 1) {
    currentGalleryIndex.value++
    updateGalleryTranslate()
  }
}

const goToGallery = (index) => {
  currentGalleryIndex.value = index
  updateGalleryTranslate()
}

// 监听轮播视图变化
watch(galleryViewMode, (newMode) => {
  if (newMode === 'slider') {
    setTimeout(() => {
      updateGalleryTranslate()
    }, 100)
  }
})

// 情话语录滑动控制
const handleQuoteTouchStart = (e) => {
  quoteIsDragging = true
  quoteStartX = e.touches[0].clientX
  quoteCurrentX = quoteTranslateX.value
}

const handleQuoteTouchMove = (e) => {
  if (!quoteIsDragging) return
  const diff = e.touches[0].clientX - quoteStartX
  
  if (currentQuoteIndex.value === 0 && diff > 0) {
    quoteTranslateX.value = quoteCurrentX + diff * 0.5
  } else if (currentQuoteIndex.value === loveQuotes.value.length - 1 && diff < 0) {
    quoteTranslateX.value = quoteCurrentX + diff * 0.5
  } else {
    quoteTranslateX.value = quoteCurrentX + diff
  }
}

const handleQuoteTouchEnd = () => {
  if (!quoteIsDragging) return
  quoteIsDragging = false
  
  const diff = quoteTranslateX.value - quoteCurrentX
  const threshold = 50
  
  if (diff > threshold && currentQuoteIndex.value > 0) {
    prevQuote()
  } else if (diff < -threshold && currentQuoteIndex.value < loveQuotes.value.length - 1) {
    nextQuote()
  } else {
    updateQuoteTranslate()
  }
}

const updateQuoteTranslate = () => {
  const sliderWidth = quotesSliderRef.value?.offsetWidth || window.innerWidth
  quoteTranslateX.value = -currentQuoteIndex.value * sliderWidth
}

const prevQuote = () => {
  if (currentQuoteIndex.value > 0) {
    currentQuoteIndex.value--
    updateQuoteTranslate()
  }
}

const nextQuote = () => {
  if (currentQuoteIndex.value < loveQuotes.value.length - 1) {
    currentQuoteIndex.value++
  } else {
    currentQuoteIndex.value = 0
  }
  updateQuoteTranslate()
}

const goToQuote = (index) => {
  currentQuoteIndex.value = index
  updateQuoteTranslate()
}

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
      name: '小哥哥生日',
      date: '1998-06-15',
      icon: '🎂',
      originalDate: new Date('1998-06-15'),
      isBirthday: true
    },
    {
      name: '小仙女生日',
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
    },
    {
      name: '520表白日',
      date: `${today.getFullYear()}-05-20`,
      icon: '💝',
      originalDate: new Date(`${today.getFullYear()}-05-20`)
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
  return upcoming || { name: '暂无纪念日', date: '', icon: '💫' }
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

// 格式化数字（两位数）
const formatNumber = (num) => {
  return num.toString().padStart(2, '0')
}

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
      audio.volume = 0.5
    }
    audio.play().catch(e => console.log('音频播放失败:', e))
  } else {
    if (audio) {
      audio.pause()
    }
  }
}

// 触摸反馈处理
const handleTouchStart = (e) => {
  const target = e.currentTarget
  target.classList.add('touch-active')
}

const handleTouchEnd = (e) => {
  const target = e.currentTarget
  target.classList.remove('touch-active')
}

// 点击反馈处理
const handleStatClick = () => {
  // 点击动画效果
}

const handleAnniversaryClick = () => {
  // 点击动画效果
}

const handleAnniversaryItemClick = (anniversary) => {
  console.log('点击纪念日:', anniversary.name)
}

const handleQuoteClick = () => {
  nextQuote()
}

// 定时器
let countdownInterval = null
let quoteInterval = null

onMounted(() => {
  calculateCountdown()
  updateQuoteTranslate()
  
  countdownInterval = setInterval(() => {
    calculateCountdown()
  }, 1000)
  
  // 情话自动轮播
  quoteInterval = setInterval(() => {
    if (!quoteIsDragging) {
      nextQuote()
    }
  }, 6000)
  
  // 初始化轮播位置
  setTimeout(() => {
    updateQuoteTranslate()
    if (galleryViewMode.value === 'slider') {
      updateGalleryTranslate()
    }
  }, 200)
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
  top: 16px;
  right: 16px;
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background: linear-gradient(145deg, rgba(255, 255, 255, 0.95), rgba(250, 240, 240, 0.95));
  box-shadow: 0 4px 20px rgba(212, 165, 165, 0.25);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 1000;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  border: 1px solid rgba(212, 165, 165, 0.2);
  -webkit-tap-highlight-color: transparent;
}

.music-control:hover {
  transform: scale(1.1);
  box-shadow: 0 6px 25px rgba(212, 165, 165, 0.35);
}

.music-control:active {
  transform: scale(0.95);
}

.music-icon {
  font-size: 22px;
  position: relative;
  z-index: 1;
}

.music-waves {
  position: absolute;
  bottom: -2px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 2px;
}

.wave {
  width: 3px;
  height: 12px;
  background: linear-gradient(to top, var(--primary-color), var(--secondary-color));
  border-radius: 2px;
  animation: wave 0.8s ease-in-out infinite;
}

.wave:nth-child(1) { animation-delay: 0s; }
.wave:nth-child(2) { animation-delay: 0.2s; }
.wave:nth-child(3) { animation-delay: 0.4s; }

@keyframes wave {
  0%, 100% { height: 4px; transform: scaleY(1); }
  50% { height: 12px; transform: scaleY(1.2); }
}

/* 头部区域 */
.header-section {
  text-align: center;
  background: linear-gradient(145deg, rgba(255, 255, 255, 0.98), rgba(250, 245, 245, 0.95));
  position: relative;
  overflow: visible;
}

.header-section::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 60%;
  height: 1px;
  background: linear-gradient(90deg, transparent, rgba(212, 165, 165, 0.3), transparent);
}

.main-title {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  margin-bottom: 20px;
}

.title-decoration {
  font-size: 14px;
  color: var(--secondary-color);
  opacity: 0.7;
}

.title-text {
  font-size: 20px;
  font-weight: 600;
  color: var(--primary-color);
  letter-spacing: 2px;
}

.couple-names {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
}

.name-tag {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  border-radius: 25px;
  background: linear-gradient(135deg, rgba(245, 230, 224, 0.8), rgba(250, 240, 240, 0.9));
  box-shadow: 0 2px 10px rgba(212, 165, 165, 0.15);
  border: 1px solid rgba(212, 165, 165, 0.2);
  transition: all 0.3s ease;
}

.name-tag:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(212, 165, 165, 0.25);
}

.name-tag.boy {
  background: linear-gradient(135deg, rgba(230, 240, 250, 0.6), rgba(245, 245, 255, 0.8));
}

.name-tag.girl {
  background: linear-gradient(135deg, rgba(250, 235, 240, 0.6), rgba(255, 245, 250, 0.8));
}

.name-icon {
  font-size: 18px;
}

.name {
  font-size: 15px;
  font-weight: 600;
  color: var(--text-primary);
}

.love-connector {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
}

.large-heart {
  font-size: 28px;
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
  gap: 16px;
  margin-bottom: 20px;
}

.avatar-wrapper {
  width: 90px;
  height: 90px;
  border-radius: 50%;
  padding: 3px;
  background: linear-gradient(135deg, var(--secondary-color), var(--primary-color));
  box-shadow: 0 4px 20px rgba(212, 165, 165, 0.3);
  position: relative;
  overflow: hidden;
}

.avatar-wrapper::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(45deg, transparent 40%, rgba(255, 255, 255, 0.4) 50%, transparent 60%);
  animation: shimmer 3s ease-in-out infinite;
}

.avatar-placeholder {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  background: linear-gradient(135deg, var(--soft-pink), var(--warm-peach));
  display: flex;
  align-items: center;
  justify-content: center;
}

.placeholder-icon {
  font-size: 36px;
}

.avatar {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  object-fit: cover;
  opacity: 0;
  transition: opacity 0.5s ease;
  position: relative;
  z-index: 1;
}

.avatar-loaded {
  opacity: 1;
}

.heart-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

.heart-ring {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.9), rgba(250, 240, 240, 0.9));
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 2px 15px rgba(212, 165, 165, 0.2);
  animation: pulse-ring 2s ease-in-out infinite;
}

.beating-heart {
  font-size: 24px;
  animation: heartbeat 1.2s ease-in-out infinite;
}

@keyframes pulse-ring {
  0%, 100% {
    box-shadow: 0 0 0 0 rgba(212, 165, 165, 0.4);
  }
  50% {
    box-shadow: 0 0 0 8px rgba(212, 165, 165, 0);
  }
}

.together-badge {
  text-align: center;
  padding: 16px;
  background: linear-gradient(135deg, rgba(245, 230, 224, 0.5), rgba(250, 240, 240, 0.6));
  border-radius: 16px;
  border: 1px solid rgba(212, 165, 165, 0.15);
}

.together-label {
  font-size: 13px;
  color: var(--text-secondary);
  display: block;
  margin-bottom: 8px;
}

.days-display {
  display: flex;
  align-items: baseline;
  justify-content: center;
  gap: 6px;
}

.days-number {
  font-size: 42px;
  font-weight: 700;
  color: var(--primary-color);
  font-family: 'Georgia', serif;
  text-shadow: 2px 2px 4px rgba(212, 165, 165, 0.2);
}

.days-unit {
  font-size: 16px;
  font-weight: 500;
  color: var(--text-secondary);
}

/* 统计区域 */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
}

.stat-card {
  text-align: center;
  padding: 16px 10px;
  background: linear-gradient(145deg, rgba(255, 255, 255, 0.9), rgba(250, 245, 245, 0.95));
  border-radius: 16px;
  border: 1px solid rgba(212, 165, 165, 0.15);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  cursor: pointer;
  -webkit-tap-highlight-color: transparent;
}

.stat-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(212, 165, 165, 0.2);
}

.stat-card:active {
  transform: translateY(-2px) scale(0.98);
}

.stat-icon {
  font-size: 24px;
  margin-bottom: 8px;
}

.stat-value {
  font-size: 28px;
  font-weight: 700;
  color: var(--primary-color);
  margin-bottom: 4px;
  font-family: 'Georgia', serif;
}

.stat-label {
  font-size: 12px;
  color: var(--text-secondary);
  font-weight: 500;
}

/* 倒计时区域 */
.countdown-wrapper {
  margin-bottom: 20px;
}

.countdown-display {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 8px;
}

.countdown-unit {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.countdown-box {
  width: 60px;
  height: 60px;
  background: linear-gradient(145deg, var(--primary-color), var(--accent-color));
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 15px rgba(212, 165, 165, 0.3);
  position: relative;
  overflow: hidden;
}

.countdown-box::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 50%;
  background: linear-gradient(to bottom, rgba(255, 255, 255, 0.15), transparent);
  border-radius: 12px 12px 0 0;
}

.countdown-number {
  font-size: 24px;
  font-weight: 700;
  color: var(--white);
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
  position: relative;
  z-index: 1;
  font-family: 'Courier New', monospace;
}

.countdown-label {
  font-size: 11px;
  color: var(--text-secondary);
  margin-top: 6px;
  font-weight: 500;
}

.countdown-separator {
  display: flex;
  align-items: center;
  padding-bottom: 16px;
}

.separator-dot {
  font-size: 20px;
  font-weight: 700;
  color: var(--primary-color);
  animation: separator-pulse 1s ease-in-out infinite;
}

@keyframes separator-pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.3; }
}

.next-anniversary-info {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  padding: 12px 20px;
  background: linear-gradient(135deg, rgba(245, 230, 224, 0.6), rgba(250, 240, 240, 0.7));
  border-radius: 25px;
  border: 1px solid rgba(212, 165, 165, 0.2);
  cursor: pointer;
  transition: all 0.3s ease;
}

.next-anniversary-info:hover {
  transform: scale(1.02);
  box-shadow: 0 4px 15px rgba(212, 165, 165, 0.2);
}

.next-icon {
  font-size: 24px;
}

.next-details {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.next-name {
  font-size: 14px;
  font-weight: 600;
  color: var(--text-primary);
}

.next-date {
  font-size: 12px;
  color: var(--text-secondary);
}

/* 纪念日清单 */
.anniversary-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.anniversary-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 14px 16px;
  border-radius: 16px;
  position: relative;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  cursor: pointer;
  -webkit-tap-highlight-color: transparent;
  border: 2px solid transparent;
  overflow: visible;
}

.anniversary-item.is-far {
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.8), rgba(250, 245, 245, 0.9));
  border-color: rgba(212, 165, 165, 0.1);
}

.anniversary-item.is-far:hover {
  border-color: rgba(212, 165, 165, 0.3);
  transform: translateX(4px);
  box-shadow: 0 4px 15px rgba(212, 165, 165, 0.15);
}

.anniversary-item.is-approaching {
  background: linear-gradient(135deg, rgba(255, 245, 230, 0.9), rgba(255, 235, 220, 0.95));
  border-color: rgba(255, 180, 120, 0.5);
  box-shadow: 0 0 20px rgba(255, 180, 120, 0.2);
  animation: approaching-glow 2s ease-in-out infinite;
}

@keyframes approaching-glow {
  0%, 100% {
    box-shadow: 0 0 15px rgba(255, 180, 120, 0.2);
  }
  50% {
    box-shadow: 0 0 25px rgba(255, 180, 120, 0.4);
  }
}

.anniversary-item.is-today {
  background: linear-gradient(135deg, rgba(255, 215, 180, 0.95), rgba(255, 200, 150, 0.9));
  border-color: rgba(255, 150, 80, 0.6);
  box-shadow: 0 0 30px rgba(255, 150, 80, 0.3);
  animation: today-pulse 1.5s ease-in-out infinite;
}

@keyframes today-pulse {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.02);
  }
}

.anniversary-left {
  display: flex;
  align-items: center;
  gap: 12px;
}

.anniversary-icon-wrapper {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  background: linear-gradient(135deg, var(--soft-pink), var(--warm-peach));
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 2px 8px rgba(212, 165, 165, 0.2);
  transition: all 0.3s ease;
}

.anniversary-icon-wrapper.icon-highlight {
  background: linear-gradient(135deg, rgba(255, 180, 100, 0.8), rgba(255, 150, 80, 0.9));
  box-shadow: 0 0 15px rgba(255, 150, 80, 0.4);
}

.anniversary-icon-wrapper.icon-approaching {
  background: linear-gradient(135deg, rgba(255, 200, 150, 0.8), rgba(255, 180, 120, 0.9));
  animation: icon-bounce 0.6s ease-in-out infinite;
}

@keyframes icon-bounce {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.1); }
}

.anniversary-icon {
  font-size: 22px;
}

.anniversary-info {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.anniversary-name {
  font-size: 15px;
  font-weight: 600;
  color: var(--text-primary);
}

.is-today .anniversary-name {
  color: #8B4513;
}

.is-approaching .anniversary-name {
  color: #A0522D;
}

.anniversary-date {
  font-size: 12px;
  color: var(--text-muted);
}

.anniversary-right {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 4px;
}

.days-count {
  display: flex;
  align-items: baseline;
  gap: 2px;
}

.days-num {
  font-size: 22px;
  font-weight: 700;
  color: var(--primary-color);
}

.days-text {
  font-size: 12px;
  font-weight: 500;
  color: var(--text-secondary);
}

.days-soon .days-num {
  color: #D2691E;
  font-size: 24px;
}

.days-today {
  display: flex;
  align-items: center;
  gap: 4px;
}

.today-text {
  font-size: 18px;
  font-weight: 700;
  color: #CD853F;
}

.sparkle {
  font-size: 16px;
  animation: sparkle 0.8s ease-in-out infinite;
}

@keyframes sparkle {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(1.2); }
}

.approaching-tag {
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 3px 10px;
  background: linear-gradient(135deg, rgba(255, 180, 100, 0.9), rgba(255, 150, 80, 0.95));
  border-radius: 12px;
  font-size: 10px;
  font-weight: 600;
  color: white;
}

.tag-icon {
  font-size: 12px;
}

/* 情话语录 */
.quotes-slider {
  position: relative;
  overflow: hidden;
  border-radius: 16px;
  margin-bottom: 16px;
  cursor: pointer;
  -webkit-tap-highlight-color: transparent;
}

.quotes-track {
  display: flex;
  transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.quote-slide {
  min-width: 100%;
  padding: 0 10px;
}

.quote-content {
  text-align: center;
  padding: 20px;
  background: linear-gradient(135deg, rgba(250, 245, 245, 0.9), rgba(245, 240, 245, 0.95));
  border-radius: 16px;
  border: 1px solid rgba(212, 165, 165, 0.15);
  min-height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.quote-content::before {
  content: '';
  position: absolute;
  top: 10px;
  left: 15px;
  font-size: 40px;
  color: rgba(212, 165, 165, 0.15);
  font-family: 'Georgia', serif;
}

.quote-mark {
  font-size: 28px;
  color: var(--secondary-color);
  opacity: 0.5;
  font-family: 'Georgia', serif;
  align-self: flex-start;
}

.quote-mark.left {
  margin-right: 8px;
}

.quote-mark.right {
  margin-left: 8px;
  align-self: flex-end;
}

.quote-text {
  font-size: 15px;
  color: var(--text-primary);
  line-height: 1.8;
  font-weight: 500;
  font-style: italic;
  flex: 1;
}

.quote-controls {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
}

.quote-btn {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  border: 1px solid var(--secondary-color);
  background: rgba(255, 255, 255, 0.9);
  color: var(--primary-color);
  font-size: 18px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  -webkit-tap-highlight-color: transparent;
}

.quote-btn:hover {
  background: var(--primary-color);
  color: white;
  border-color: var(--primary-color);
}

.quote-indicators {
  display: flex;
  gap: 6px;
}

.indicator-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--secondary-color);
  opacity: 0.4;
  transition: all 0.3s ease;
  cursor: pointer;
}

.indicator-dot.active {
  width: 20px;
  border-radius: 4px;
  opacity: 1;
  background: linear-gradient(90deg, var(--primary-color), var(--accent-color));
}

/* 相册区域 */
.gallery-container {
  position: relative;
}

.gallery-tabs {
  display: flex;
  justify-content: center;
  gap: 12px;
  margin-bottom: 16px;
}

.tab-btn {
  padding: 8px 18px;
  border-radius: 20px;
  border: 1px solid var(--secondary-color);
  background: rgba(255, 255, 255, 0.8);
  color: var(--text-secondary);
  font-size: 13px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  -webkit-tap-highlight-color: transparent;
}

.tab-btn:hover {
  border-color: var(--primary-color);
  color: var(--primary-color);
}

.tab-btn.active {
  background: linear-gradient(135deg, var(--primary-color), var(--accent-color));
  color: white;
  border-color: transparent;
  box-shadow: 0 4px 15px rgba(212, 165, 165, 0.3);
}

/* 网格视图 */
.gallery-grid-view {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}

.gallery-grid-item {
  aspect-ratio: 1;
  border-radius: 12px;
  overflow: hidden;
  position: relative;
  cursor: pointer;
  box-shadow: 0 2px 10px rgba(212, 165, 165, 0.15);
  transition: all 0.3s ease;
  -webkit-tap-highlight-color: transparent;
}

.gallery-grid-item:hover {
  transform: scale(1.05);
  box-shadow: 0 6px 20px rgba(212, 165, 165, 0.25);
}

.photo-placeholder {
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, var(--soft-pink), var(--warm-peach));
  display: flex;
  align-items: center;
  justify-content: center;
}

.photo-icon {
  font-size: 32px;
}

.gallery-photo {
  width: 100%;
  height: 100%;
  object-fit: cover;
  opacity: 0;
  transition: opacity 0.5s ease;
}

.photo-loaded {
  opacity: 1;
}

.photo-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.gallery-grid-item:hover .photo-overlay {
  opacity: 1;
}

.view-icon {
  font-size: 24px;
}

/* 轮播视图 */
.gallery-slider-view {
  position: relative;
}

.gallery-slider-wrapper {
  overflow: hidden;
  border-radius: 16px;
  margin-bottom: 12px;
}

.gallery-track {
  display: flex;
  transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.gallery-slide {
  min-width: 100%;
  aspect-ratio: 4/3;
  position: relative;
  overflow: hidden;
}

.slide-placeholder {
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, var(--soft-pink), var(--warm-peach));
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.slide-icon {
  font-size: 48px;
}

.slide-index {
  font-size: 14px;
  color: var(--text-secondary);
}

.slide-photo {
  width: 100%;
  height: 100%;
  object-fit: cover;
  opacity: 0;
  transition: opacity 0.5s ease;
}

.slide-loaded {
  opacity: 1;
}

.slide-caption {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 12px;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.5), transparent);
}

.caption-text {
  font-size: 13px;
  color: white;
  font-weight: 500;
}

.gallery-indicators {
  display: flex;
  justify-content: center;
  gap: 6px;
  margin-bottom: 12px;
}

.gallery-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--secondary-color);
  opacity: 0.4;
  transition: all 0.3s ease;
  cursor: pointer;
}

.gallery-dot.active {
  width: 24px;
  border-radius: 4px;
  opacity: 1;
  background: linear-gradient(90deg, var(--primary-color), var(--accent-color));
}

.gallery-nav {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
}

.nav-btn {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border: 1px solid var(--secondary-color);
  background: rgba(255, 255, 255, 0.9);
  color: var(--primary-color);
  font-size: 20px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  -webkit-tap-highlight-color: transparent;
}

.nav-btn:hover {
  background: var(--primary-color);
  color: white;
  border-color: var(--primary-color);
}

.nav-btn:disabled {
  opacity: 0.3;
  cursor: not-allowed;
}

.gallery-counter {
  font-size: 14px;
  color: var(--text-secondary);
  font-weight: 500;
}

/* 签名区域 */
.signature-section {
  text-align: center;
  background: linear-gradient(145deg, rgba(250, 245, 245, 0.95), rgba(245, 240, 245, 0.9));
  border: 1px solid rgba(212, 165, 165, 0.15);
}

.signature-decoration {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  margin-bottom: 16px;
}

.deco-line {
  width: 40px;
  height: 1px;
  background: linear-gradient(90deg, transparent, var(--secondary-color), transparent);
}

.deco-heart {
  font-size: 20px;
  animation: heartbeat 1.5s ease-in-out infinite;
}

.signature-content {
  margin-bottom: 16px;
  padding: 0 20px;
}

.signature-text {
  font-size: 17px;
  font-weight: 500;
  color: var(--primary-color);
  font-style: italic;
  line-height: 1.8;
  letter-spacing: 0.5px;
}

.signature-footer {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.start-date {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.date-label {
  font-size: 12px;
  color: var(--text-muted);
}

.date-value {
  font-size: 14px;
  font-weight: 600;
  color: var(--text-primary);
}

.footer-hearts {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.mini-heart {
  font-size: 16px;
  opacity: 0.6;
  transition: opacity 0.3s ease;
}

.mini-heart:nth-child(2),
.mini-heart:nth-child(3),
.mini-heart:nth-child(4),
.mini-heart:nth-child(5),
.mini-heart:nth-child(6) {
  opacity: 1;
  animation: heartbeat 1.5s ease-in-out infinite;
}

.mini-heart:nth-child(3) { animation-delay: 0.2s; }
.mini-heart:nth-child(4) { animation-delay: 0.4s; }
.mini-heart:nth-child(5) { animation-delay: 0.6s; }
.mini-heart:nth-child(6) { animation-delay: 0.8s; }

/* 安全区域 */
.safe-area-bottom {
  height: 20px;
}

/* 相册预览弹窗 */
.gallery-preview-modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  animation: fadeIn 0.3s ease;
}

.preview-container {
  position: relative;
  max-width: 90%;
  max-height: 80%;
}

.close-btn {
  position: absolute;
  top: -40px;
  right: 0;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border: none;
  background: rgba(255, 255, 255, 0.2);
  color: white;
  font-size: 24px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.close-btn:hover {
  background: rgba(255, 255, 255, 0.3);
}

.preview-image {
  max-width: 100%;
  max-height: 80vh;
  border-radius: 12px;
  object-fit: contain;
}

.preview-info {
  position: absolute;
  bottom: -30px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 14px;
  color: rgba(255, 255, 255, 0.7);
}

/* 触摸反馈样式 */
.touch-active {
  transform: scale(0.98);
  opacity: 0.8;
}

/* 响应式适配 */
@media (max-width: 480px) {
  .title-text {
    font-size: 18px;
  }
  
  .name-tag {
    padding: 8px 16px;
  }
  
  .name {
    font-size: 13px;
  }
  
  .avatar-wrapper {
    width: 75px;
    height: 75px;
  }
  
  .heart-ring {
    width: 44px;
    height: 44px;
  }
  
  .beating-heart {
    font-size: 20px;
  }
  
  .days-number {
    font-size: 36px;
  }
  
  .stat-value {
    font-size: 24px;
  }
  
  .countdown-box {
    width: 52px;
    height: 52px;
  }
  
  .countdown-number {
    font-size: 20px;
  }
  
  .gallery-grid-view {
    gap: 8px;
  }
}

@media (max-width: 360px) {
  .avatar-wrapper {
    width: 65px;
    height: 65px;
  }
  
  .placeholder-icon {
    font-size: 28px;
  }
  
  .heart-ring {
    width: 38px;
    height: 38px;
  }
  
  .beating-heart {
    font-size: 18px;
  }
  
  .days-number {
    font-size: 32px;
  }
  
  .countdown-display {
    gap: 6px;
  }
  
  .countdown-box {
    width: 46px;
    height: 46px;
  }
  
  .countdown-number {
    font-size: 18px;
  }
  
  .countdown-label {
    font-size: 10px;
  }
}
</style>