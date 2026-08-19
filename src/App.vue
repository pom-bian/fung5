<script setup lang="ts">
import { computed, ref } from 'vue'

type Font = { name: string; family: string; mood: string; category: string; clue: string; use: string }
type Answer = { round: number; selected: string; correct: string }
const fonts: Font[] = [
  { name: 'Bungee', family: 'Bungee', mood: '大聲、活潑', category: '展示字體', clue: '粗厚、圓角、像招牌一樣有重量。', use: '活動標題、海報、遊戲品牌' }, { name: 'Cormorant Garamond', family: 'Cormorant Garamond', mood: '優雅、文藝', category: '襯線字體', clue: '筆畫對比強，細長的襯線帶來古典感。', use: '書籍、精品、文化類品牌' },
  { name: 'DM Mono', family: 'DM Mono', mood: '精準、技術感', category: '等寬字體', clue: '每個字母佔相同寬度，排列整齊像程式碼。', use: '程式碼、數據、技術介面' }, { name: 'Fraunces', family: 'Fraunces', mood: '溫暖、有表現力', category: '襯線字體', clue: '輪廓柔和、粗細有變化，帶有復古印刷感。', use: '雜誌、食品、生活風格品牌' },
  { name: 'IBM Plex Serif', family: 'IBM Plex Serif', mood: '聰明、值得信賴', category: '襯線字體', clue: '結構清楚、襯線穩定，閱讀時很可靠。', use: '文章、教育、企業內容' }, { name: 'Manrope', family: 'Manrope', mood: '乾淨、親切', category: '無襯線字體', clue: '沒有襯線，字面乾淨，圓潤細節讓它很親近。', use: '網站、App、現代品牌' },
  { name: 'Pacifico', family: 'Pacifico', mood: '輕鬆、陽光', category: '手寫字體', clue: '連筆流暢，像用筆寫出的簽名，個性很強。', use: '餐飲、婚禮、休閒品牌' }, { name: 'Playfair Display', family: 'Playfair Display', mood: '經典、精緻', category: '襯線字體', clue: '粗細反差明顯，直立筆畫像高級時尚雜誌。', use: '時尚、標題、精品品牌' },
  { name: 'Space Grotesk', family: 'Space Grotesk', mood: '現代、好奇', category: '無襯線字體', clue: '幾何骨架中帶有不規則細節，理性但不無聊。', use: '科技、新創、數位產品' }, { name: 'Unbounded', family: 'Unbounded', mood: '未來感、醒目', category: '展示字體', clue: '字寬誇張、形狀幾何，遠看就有強烈未來感。', use: '科技活動、音樂、遊戲標題' },
]
const rounds = [
  { question: '哪個適合科技新創？', hint: '產品標題需要有個性，帶點實驗感。', translation: 'Best for a bold, curious tech product?', target: 'Space Grotesk', options: ['Space Grotesk', 'Manrope'] },
  { question: '哪個適合街頭活動？', hint: '海報需要從遠處就能吸引目光。', translation: 'Best for a poster that demands attention?', target: 'Bungee', options: ['Bungee', 'Manrope'] },
  { question: '哪個適合時尚雜誌？', hint: '封面需要高對比，帶有講究的時裝感。', translation: 'Best for a sharp, fashion-forward cover?', target: 'Playfair Display', options: ['Playfair Display', 'Cormorant Garamond'] },
  { question: '哪個適合程式碼編輯器？', hint: '文字需要清楚對齊，方便快速掃讀。', translation: 'Best for lining up code clearly?', target: 'DM Mono', options: ['DM Mono', 'Space Grotesk'] },
  { question: '哪個適合甜點店招牌？', hint: '想要手寫簽名感，整體輕鬆又親切。', translation: 'Best for a friendly, handwritten dessert sign?', target: 'Pacifico', options: ['Pacifico', 'Fraunces'] },
  { question: '哪個適合文學小說？', hint: '書封需要詩意，以及一點古典氣質。', translation: 'Best for a poetic, classical novel cover?', target: 'Cormorant Garamond', options: ['Cormorant Garamond', 'IBM Plex Serif'] },
  { question: '哪個適合未來感音樂祭？', hint: '主標題需要寬闊、強烈，像來自數位世界。', translation: 'Best for a wide, futuristic festival title?', target: 'Unbounded', options: ['Unbounded', 'Bungee'] },
  { question: '哪個適合數位閱讀？', hint: '長篇文章需要清爽、舒服，不增加閱讀負擔。', translation: 'Best for calm, comfortable digital reading?', target: 'Manrope', options: ['IBM Plex Serif', 'Manrope'] },
  { question: '哪個適合生活 App？', hint: '介面需要讓第一次使用者感到輕鬆。', translation: 'Best for an easygoing lifestyle app?', target: 'Manrope', options: ['Manrope', 'Space Grotesk'] },
  { question: '哪個適合復古食品品牌？', hint: '品牌需要溫暖，帶有手工印刷的感覺。', translation: 'Best for a warm, handmade food brand?', target: 'Fraunces', options: ['Fraunces', 'Playfair Display'] },
]
const round = ref(1), score = ref(0), streak = ref(0), selected = ref<string | null>(null), gameOver = ref(false), answerHistory = ref<Answer[]>([])
const current = computed(() => fonts.find((font) => font.name === rounds[round.value - 1].target)!)
const question = computed(() => rounds[round.value - 1].question)
const hint = computed(() => rounds[round.value - 1].hint)
const translation = computed(() => rounds[round.value - 1].translation)
const choices = computed(() => rounds[round.value - 1].options.map((name) => fonts.find((font) => font.name === name)!))
const progress = computed(() => `${Math.min(round.value, 10).toString().padStart(2, '0')} / 10`)
function currentFont(name: string) { return fonts.find((font) => font.name === name)! }
function choose(font: Font) { if (selected.value || gameOver.value) return; selected.value = font.name; answerHistory.value.push({ round: round.value, selected: font.name, correct: current.value.name }) }
function nextRound() { if (round.value >= 10) { score.value = answerHistory.value.filter((answer) => answer.selected === answer.correct).length * 100; gameOver.value = true; return }; round.value += 1; selected.value = null }
function restart() { round.value = 1; score.value = 0; streak.value = 0; selected.value = null; gameOver.value = false; answerHistory.value = [] }
</script>

<template>
  <main>
    <nav aria-label="主要導覽"><a class="brand" href="./"><span class="brand-mark">Aa</span><span>type<span class="brand-dot">.</span>guess</span></a><div class="nav-meta"><span class="live-dot"></span> 小小字體遊戲</div><div class="top-score"><span>{{ gameOver ? '分數' : '最後結算' }}</span><strong>{{ gameOver ? score.toString().padStart(4, '0') : '----' }}</strong></div></nav>
    <section v-if="!gameOver" class="game-shell">
      <header class="game-heading"><div><p class="eyebrow">第 {{ progress }} 回合</p><h1>{{ question }}</h1><p class="question-hint">{{ hint }}</p></div><div class="streak"><span>✦</span> 完成後揭曉</div></header>
      <div class="progress-track" aria-hidden="true"><span :style="{ width: `${round * 10}%` }"></span></div>
      <div class="choices-heading"><span>選出你的答案</span><span>答對得 100 分</span></div>
      <div class="choices" role="group" aria-label="字體選項"><button v-for="font in choices" :key="font.name" class="choice" :class="{ chosen: selected === font.name, correct: selected === font.name && font.name === current.name, wrong: selected === font.name && font.name !== current.name }" @click="choose(font)"><span class="choice-letter">{{ String.fromCharCode(65 + choices.indexOf(font)) }}</span><span class="choice-copy"><strong class="choice-preview" :style="{ fontFamily: `'${font.family}', sans-serif` }">{{ translation }}</strong><small>{{ font.name }}</small></span><span v-if="selected === font.name" class="choice-result">{{ font.name === current.name ? '✓' : '×' }}</span></button></div>
      <div v-if="selected" class="answer-row answer-pending" aria-live="polite"><strong v-if="selected === current.name" class="answer-good">答對了！</strong><strong v-else class="answer-bad">答錯了。</strong><button class="next-button" @click="nextRound">{{ round === 10 ? '查看完整解析' : '下一回合' }} <span>→</span></button></div>
    </section>
    <section v-else class="finish-shell"><p class="eyebrow">遊戲完成 · 共 10 回合</p><h1>你的字體<br /><em>風格報告。</em></h1><div class="final-score"><span>最後得分</span><strong>{{ score.toString().padStart(4, '0') }}</strong><small>10 題答對 {{ score / 100 }} 題</small></div><div class="review-list"><article v-for="answer in answerHistory" :key="answer.round" class="review-card"><p class="review-question">0{{ answer.round }} · {{ rounds[answer.round - 1].question }}</p><div class="font-compare"><div><small>你的選擇</small><strong :style="{ fontFamily: `'${fonts.find((font) => font.name === answer.selected)?.family}', sans-serif` }">{{ answer.selected }}</strong><p>{{ fonts.find((font) => font.name === answer.selected)?.clue }}</p></div><div><small>情境標準答案</small><strong :style="{ fontFamily: `'${currentFont(answer.correct).family}', sans-serif` }">{{ answer.correct }}</strong><p>{{ currentFont(answer.correct).clue }}</p></div></div><p class="review-verdict" :class="answer.selected === answer.correct ? 'is-correct' : 'is-wrong'">{{ answer.selected === answer.correct ? '✓ 這個情境判斷很準。' : `→ ${answer.correct} 更符合這個情境，因為它${currentFont(answer.correct).use}。` }}</p></article></div><button class="restart-button" @click="restart">再玩一次 <span>↗</span></button></section>
    <footer><span>© 2025 type.guess</span><span>獻給喜歡字體的你 <i>♥</i></span><span>10 種字體 · 輕鬆玩一下</span></footer>
  </main>
</template>
