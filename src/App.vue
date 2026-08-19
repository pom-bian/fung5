<script setup lang="ts">
import { computed, ref } from 'vue'

type Font = { name: string; family: string; mood: string }
const fonts: Font[] = [
  { name: 'Bungee', family: 'Bungee', mood: '大聲、活潑' }, { name: 'Cormorant Garamond', family: 'Cormorant Garamond', mood: '優雅、文藝' },
  { name: 'DM Mono', family: 'DM Mono', mood: '精準、技術感' }, { name: 'Fraunces', family: 'Fraunces', mood: '溫暖、有表現力' },
  { name: 'IBM Plex Serif', family: 'IBM Plex Serif', mood: '聰明、值得信賴' }, { name: 'Manrope', family: 'Manrope', mood: '乾淨、親切' },
  { name: 'Pacifico', family: 'Pacifico', mood: '輕鬆、陽光' }, { name: 'Playfair Display', family: 'Playfair Display', mood: '經典、精緻' },
  { name: 'Space Grotesk', family: 'Space Grotesk', mood: '現代、好奇' }, { name: 'Unbounded', family: 'Unbounded', mood: '未來感、醒目' },
]
const samples = ['make room for good ideas', 'small details, big feeling', 'there is no wrong type']
const round = ref(1), score = ref(0), streak = ref(0), selected = ref<string | null>(null), gameOver = ref(false), currentIndex = ref(8)
const current = computed(() => fonts[currentIndex.value])
const sample = computed(() => samples[(round.value - 1) % samples.length])
const choices = computed(() => {
  const others = fonts.filter((font) => font.name !== current.value.name).slice((round.value * 2) % 6, ((round.value * 2) % 6) + 3)
  return [current.value, ...others].sort((a, b) => a.name.localeCompare(b.name))
})
const progress = computed(() => `${Math.min(round.value, 10).toString().padStart(2, '0')} / 10`)
function choose(font: Font) { if (selected.value || gameOver.value) return; selected.value = font.name; if (font.name === current.value.name) { score.value += 100; streak.value += 1 } else streak.value = 0 }
function nextRound() { if (round.value >= 10) { gameOver.value = true; return }; round.value += 1; currentIndex.value = (currentIndex.value + 3) % fonts.length; selected.value = null }
function restart() { round.value = 1; score.value = 0; streak.value = 0; selected.value = null; gameOver.value = false; currentIndex.value = 8 }
</script>

<template>
  <main>
    <nav aria-label="主要導覽"><a class="brand" href="./"><span class="brand-mark">Aa</span><span>type<span class="brand-dot">.</span>guess</span></a><div class="nav-meta"><span class="live-dot"></span> 小小字體遊戲</div><div class="top-score"><span>分數</span><strong>{{ score.toString().padStart(4, '0') }}</strong></div></nav>
    <section v-if="!gameOver" class="game-shell">
      <header class="game-heading"><div><p class="eyebrow">第 {{ progress }} 回合</p><h1>這是什麼<em>字體？</em></h1></div><div class="streak" :class="{ active: streak > 1 }"><span>✦</span> 連續答對 {{ streak }} 題</div></header>
      <div class="progress-track" aria-hidden="true"><span :style="{ width: `${round * 10}%` }"></span></div>
      <article class="sample-card"><div class="card-label"><span>你認得出這個字體嗎？</span><span class="card-number">0{{ round }}</span></div><p class="font-sample" :style="{ fontFamily: `'${current.family}', sans-serif` }">{{ sample }}</p><div class="sample-footer"><span>每種字體都有自己的個性。</span><span>仔細選擇 <b>↓</b></span></div></article>
      <div class="choices-heading"><span>選出你的答案</span><span>答對得 100 分</span></div>
      <div class="choices" role="group" aria-label="Font choices"><button v-for="font in choices" :key="font.name" class="choice" :class="{ correct: selected && font.name === current.name, wrong: selected === font.name && font.name !== current.name }" @click="choose(font)"><span class="choice-letter">{{ String.fromCharCode(65 + choices.indexOf(font)) }}</span><span class="choice-copy"><strong>{{ font.name }}</strong><small>{{ font.mood }}</small></span><span v-if="selected && font.name === current.name" class="choice-result">✓</span><span v-else-if="selected === font.name" class="choice-result">×</span><span v-else class="choice-arrow">↗</span></button></div>
      <div v-if="selected" class="answer-row" aria-live="polite"><span v-if="selected === current.name" class="answer-good">眼力不錯！這是 {{ current.name }}。</span><span v-else class="answer-bad">正確答案是 {{ current.name }}，繼續挑戰！</span><button class="next-button" @click="nextRound">{{ round === 10 ? '查看結果' : '下一回合' }} <span>→</span></button></div>
    </section>
    <section v-else class="finish-shell"><p class="eyebrow">遊戲完成 · 共 10 回合</p><h1>你很有<br /><em>字型感。</em></h1><div class="final-score"><span>最後得分</span><strong>{{ score.toString().padStart(4, '0') }}</strong><small>10 題答對 {{ score / 100 }} 題</small></div><button class="restart-button" @click="restart">再玩一次 <span>↗</span></button></section>
    <footer><span>© 2025 type.guess</span><span>獻給喜歡字體的你 <i>♥</i></span><span>10 種字體 · 輕鬆玩一下</span></footer>
  </main>
</template>
