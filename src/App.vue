<script setup lang="ts">
import { computed, ref } from 'vue'

type Font = { name: string; family: string; mood: string; category: string; clue: string; use: string }
const fonts: Font[] = [
  { name: 'Bungee', family: 'Bungee', mood: '大聲、活潑', category: '展示字體', clue: '粗厚、圓角、像招牌一樣有重量。', use: '活動標題、海報、遊戲品牌' }, { name: 'Cormorant Garamond', family: 'Cormorant Garamond', mood: '優雅、文藝', category: '襯線字體', clue: '筆畫對比強，細長的襯線帶來古典感。', use: '書籍、精品、文化類品牌' },
  { name: 'DM Mono', family: 'DM Mono', mood: '精準、技術感', category: '等寬字體', clue: '每個字母佔相同寬度，排列整齊像程式碼。', use: '程式碼、數據、技術介面' }, { name: 'Fraunces', family: 'Fraunces', mood: '溫暖、有表現力', category: '襯線字體', clue: '輪廓柔和、粗細有變化，帶有復古印刷感。', use: '雜誌、食品、生活風格品牌' },
  { name: 'IBM Plex Serif', family: 'IBM Plex Serif', mood: '聰明、值得信賴', category: '襯線字體', clue: '結構清楚、襯線穩定，閱讀時很可靠。', use: '文章、教育、企業內容' }, { name: 'Manrope', family: 'Manrope', mood: '乾淨、親切', category: '無襯線字體', clue: '沒有襯線，字面乾淨，圓潤細節讓它很親近。', use: '網站、App、現代品牌' },
  { name: 'Pacifico', family: 'Pacifico', mood: '輕鬆、陽光', category: '手寫字體', clue: '連筆流暢，像用筆寫出的簽名，個性很強。', use: '餐飲、婚禮、休閒品牌' }, { name: 'Playfair Display', family: 'Playfair Display', mood: '經典、精緻', category: '襯線字體', clue: '粗細反差明顯，直立筆畫像高級時尚雜誌。', use: '時尚、標題、精品品牌' },
  { name: 'Space Grotesk', family: 'Space Grotesk', mood: '現代、好奇', category: '無襯線字體', clue: '幾何骨架中帶有不規則細節，理性但不無聊。', use: '科技、新創、數位產品' }, { name: 'Unbounded', family: 'Unbounded', mood: '未來感、醒目', category: '展示字體', clue: '字寬誇張、形狀幾何，遠看就有強烈未來感。', use: '科技活動、音樂、遊戲標題' },
]
const rounds = [
  { sample: 'make room for good ideas', target: 'Space Grotesk', options: ['Space Grotesk', 'Manrope', 'DM Mono', 'Unbounded'] },
  { sample: 'small details, big feeling', target: 'Bungee', options: ['Bungee', 'Unbounded', 'Pacifico', 'Manrope'] },
  { sample: 'there is no wrong type', target: 'Playfair Display', options: ['Playfair Display', 'Cormorant Garamond', 'Fraunces', 'IBM Plex Serif'] },
  { sample: 'build something useful', target: 'DM Mono', options: ['DM Mono', 'Space Grotesk', 'IBM Plex Serif', 'Unbounded'] },
  { sample: 'good things take time', target: 'Pacifico', options: ['Pacifico', 'Fraunces', 'Bungee', 'Cormorant Garamond'] },
  { sample: 'made for curious people', target: 'Cormorant Garamond', options: ['Cormorant Garamond', 'Playfair Display', 'Fraunces', 'Pacifico'] },
  { sample: 'the future feels friendly', target: 'Unbounded', options: ['Unbounded', 'Bungee', 'Space Grotesk', 'DM Mono'] },
  { sample: 'ideas worth sharing', target: 'IBM Plex Serif', options: ['IBM Plex Serif', 'Cormorant Garamond', 'Manrope', 'DM Mono'] },
  { sample: 'keep it beautifully simple', target: 'Manrope', options: ['Manrope', 'Space Grotesk', 'IBM Plex Serif', 'Unbounded'] },
  { sample: 'a little more feeling', target: 'Fraunces', options: ['Fraunces', 'Playfair Display', 'Cormorant Garamond', 'Pacifico'] },
]
const round = ref(1), score = ref(0), streak = ref(0), selected = ref<string | null>(null), gameOver = ref(false)
const current = computed(() => fonts.find((font) => font.name === rounds[round.value - 1].target)!)
const sample = computed(() => rounds[round.value - 1].sample)
const choices = computed(() => rounds[round.value - 1].options.map((name) => fonts.find((font) => font.name === name)!))
const selectedFont = computed(() => fonts.find((font) => font.name === selected.value))
const progress = computed(() => `${Math.min(round.value, 10).toString().padStart(2, '0')} / 10`)
function choose(font: Font) { if (selected.value || gameOver.value) return; selected.value = font.name; if (font.name === current.value.name) { score.value += 100; streak.value += 1 } else streak.value = 0 }
function nextRound() { if (round.value >= 10) { gameOver.value = true; return }; round.value += 1; selected.value = null }
function restart() { round.value = 1; score.value = 0; streak.value = 0; selected.value = null; gameOver.value = false }
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
      <div v-if="selected" class="answer-row" aria-live="polite"><div class="answer-copy"><strong v-if="selected === current.name" class="answer-good">眼力不錯！這是 {{ current.name }}。</strong><strong v-else class="answer-bad">正確答案是 {{ current.name }}。</strong><p><b>{{ current.category }}</b> · {{ current.clue }}適合用在{{ current.use }}。</p><p v-if="selected !== current.name && selectedFont">你選的是 {{ selectedFont.name }}，它比較偏向「{{ selectedFont.mood }}」；這題要找的是 {{ current.mood }} 的字體。</p></div><button class="next-button" @click="nextRound">{{ round === 10 ? '查看結果' : '下一回合' }} <span>→</span></button></div>
    </section>
    <section v-else class="finish-shell"><p class="eyebrow">遊戲完成 · 共 10 回合</p><h1>你很有<br /><em>字型感。</em></h1><div class="final-score"><span>最後得分</span><strong>{{ score.toString().padStart(4, '0') }}</strong><small>10 題答對 {{ score / 100 }} 題</small></div><button class="restart-button" @click="restart">再玩一次 <span>↗</span></button></section>
    <footer><span>© 2025 type.guess</span><span>獻給喜歡字體的你 <i>♥</i></span><span>10 種字體 · 輕鬆玩一下</span></footer>
  </main>
</template>
