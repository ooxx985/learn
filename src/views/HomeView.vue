<script setup lang="ts">
import { computed, ref } from 'vue'

type Place = { name: string; location: string; tag: string; note: string; tone: string; icon: string }
const places: Place[] = [
  { name: '云上书店', location: '中环 · 2.4 km', tag: '阅读', note: '一杯咖啡，一段安静的下午', tone: 'peach', icon: '☕' },
  { name: '海边散步', location: '西环 · 4.1 km', tag: '户外', note: '把烦恼交给轻柔的海风', tone: 'blue', icon: '☁' },
  { name: '周末市集', location: '上环 · 1.8 km', tag: '探索', note: '发现新鲜、有趣的小物件', tone: 'yellow', icon: '✦' },
  { name: '深夜唱片店', location: '旺角 · 5.2 km', tag: '音乐', note: '在旋律里遇见另一个自己', tone: 'purple', icon: '♫' },
]
const categories = ['推荐', '附近', '热门', '新发现']
const activeCategory = ref('推荐')
const activePlace = ref(0)
const saved = ref<number[]>([])
const toast = ref('')
let toastTimer: ReturnType<typeof setTimeout>
const currentPlace = computed(() => places[activePlace.value]!)
function notify(message: string) { toast.value = message; clearTimeout(toastTimer); toastTimer = setTimeout(() => (toast.value = ''), 2200) }
function selectCategory(category: string) { activeCategory.value = category; notify(`已切换至「${category}」内容`) }
function move(direction: number) { activePlace.value = (activePlace.value + direction + places.length) % places.length }
function toggleSave(index: number) { saved.value = saved.value.includes(index) ? saved.value.filter((item) => item !== index) : [...saved.value, index]; notify(saved.value.includes(index) ? '已收藏到你的灵感清单' : '已取消收藏') }
</script>

<template>
  <main class="page-shell">
    <header class="topbar">
      <a class="brand" href="#top" aria-label="Morrow 首页"><span class="brand-mark">m</span><span>Morrow</span></a>
      <nav aria-label="主导航"><a class="nav-link is-active" href="#discover">发现</a><a class="nav-link" href="#saved">收藏</a></nav>
      <button class="avatar" type="button" aria-label="打开个人资料" @click="notify('个人资料功能即将上线')">W</button>
    </header>
    <section id="top" class="hero">
      <p class="eyebrow">星期五，10 月 2 日</p><h1>今天，想去哪里？</h1><p class="hero-copy">从城市的日常里，收集一点小小的惊喜。</p>
      <button class="primary-button" type="button" @click="notify('正在为你生成今日灵感…')">给我一个灵感 <span>→</span></button>
      <div class="hero-orb orb-one"></div><div class="hero-orb orb-two"></div><div class="hero-spark spark-one">✦</div><div class="hero-spark spark-two">✦</div>
    </section>
    <section id="discover" class="content-section" aria-labelledby="discover-title">
      <div class="section-heading"><div><p class="eyebrow">为你挑选</p><h2 id="discover-title">此刻的好去处</h2></div><button class="text-button" type="button" @click="notify('更多精选正在整理中')">查看全部 <span>→</span></button></div>
      <div class="filters" aria-label="内容筛选"><button v-for="category in categories" :key="category" class="filter" :class="{ selected: activeCategory === category }" type="button" @click="selectCategory(category)">{{ category }}</button></div>
      <div class="showcase" aria-live="polite">
        <article class="featured-card" :class="`tone-${currentPlace.tone}`">
          <div class="card-art" aria-hidden="true"><div class="sun"></div><div class="mountain mountain-back"></div><div class="mountain mountain-front"></div><span class="art-icon">{{ currentPlace.icon }}</span></div>
          <div class="card-body"><span class="pill">{{ currentPlace.tag }}</span><button class="save-button" type="button" :aria-label="`收藏 ${currentPlace.name}`" @click="toggleSave(activePlace)">{{ saved.includes(activePlace) ? '♥' : '♡' }}</button><h3>{{ currentPlace.name }}</h3><p class="location">⌖ {{ currentPlace.location }}</p><p class="note">{{ currentPlace.note }}</p></div>
        </article>
        <div class="slider-controls"><button type="button" aria-label="上一个地点" @click="move(-1)">←</button><div class="dots" aria-label="幻灯片位置"><button v-for="(_, index) in places" :key="index" type="button" :class="{ active: index === activePlace }" :aria-label="`显示第 ${index + 1} 个地点`" @click="activePlace = index"></button></div><button type="button" aria-label="下一个地点" @click="move(1)">→</button></div>
      </div>
    </section>
    <section id="saved" class="bottom-card"><span class="small-icon">✦</span><div><strong>把喜欢的地方存下来</strong><p>随时回来看一看，给自己一个出发的理由。</p></div><button type="button" @click="notify(`你已收藏 ${saved.length} 个地点`)">我的清单</button></section>
    <Transition name="toast"><p v-if="toast" class="toast" role="status">{{ toast }}</p></Transition>
  </main>
</template>
