<script setup lang="ts">
import { computed, ref } from 'vue'

type Font = { name: string; family: string; mood: string }
const fonts: Font[] = [
  { name: 'Bungee', family: 'Bungee', mood: 'LOUD & PLAYFUL' }, { name: 'Cormorant Garamond', family: 'Cormorant Garamond', mood: 'ELEGANT & LITERARY' },
  { name: 'DM Mono', family: 'DM Mono', mood: 'PRECISE & TECHNICAL' }, { name: 'Fraunces', family: 'Fraunces', mood: 'WARM & EXPRESSIVE' },
  { name: 'IBM Plex Serif', family: 'IBM Plex Serif', mood: 'SMART & TRUSTED' }, { name: 'Manrope', family: 'Manrope', mood: 'CLEAN & FRIENDLY' },
  { name: 'Pacifico', family: 'Pacifico', mood: 'CASUAL & SUNNY' }, { name: 'Playfair Display', family: 'Playfair Display', mood: 'CLASSIC & REFINED' },
  { name: 'Space Grotesk', family: 'Space Grotesk', mood: 'MODERN & CURIOUS' }, { name: 'Unbounded', family: 'Unbounded', mood: 'FUTURISTIC & BOLD' },
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
    <nav aria-label="Main navigation"><a class="brand" href="./"><span class="brand-mark">Aa</span><span>type<span class="brand-dot">.</span>guess</span></a><div class="nav-meta"><span class="live-dot"></span> A tiny type game</div><div class="top-score"><span>Score</span><strong>{{ score.toString().padStart(4, '0') }}</strong></div></nav>
    <section v-if="!gameOver" class="game-shell">
      <header class="game-heading"><div><p class="eyebrow">Round {{ progress }}</p><h1>What’s the <em>type?</em></h1></div><div class="streak" :class="{ active: streak > 1 }"><span>✦</span> {{ streak }} streak</div></header>
      <div class="progress-track" aria-hidden="true"><span :style="{ width: `${round * 10}%` }"></span></div>
      <article class="sample-card"><div class="card-label"><span>Can you recognize this one?</span><span class="card-number">0{{ round }}</span></div><p class="font-sample" :style="{ fontFamily: `'${current.family}', sans-serif` }">{{ sample }}</p><div class="sample-footer"><span>Every font has a voice.</span><span>Choose wisely <b>↓</b></span></div></article>
      <div class="choices-heading"><span>Your best guess</span><span>+100 points for a hit</span></div>
      <div class="choices" role="group" aria-label="Font choices"><button v-for="font in choices" :key="font.name" class="choice" :class="{ correct: selected && font.name === current.name, wrong: selected === font.name && font.name !== current.name }" @click="choose(font)"><span class="choice-letter">{{ String.fromCharCode(65 + choices.indexOf(font)) }}</span><span class="choice-copy"><strong>{{ font.name }}</strong><small>{{ font.mood }}</small></span><span v-if="selected && font.name === current.name" class="choice-result">✓</span><span v-else-if="selected === font.name" class="choice-result">×</span><span v-else class="choice-arrow">↗</span></button></div>
      <div v-if="selected" class="answer-row" aria-live="polite"><span v-if="selected === current.name" class="answer-good">Nice eye! That’s {{ current.name }}.</span><span v-else class="answer-bad">It was {{ current.name }} — keep looking.</span><button class="next-button" @click="nextRound">{{ round === 10 ? 'See results' : 'Next round' }} <span>→</span></button></div>
    </section>
    <section v-else class="finish-shell"><p class="eyebrow">Game complete · 10 rounds</p><h1>You have a<br /><em>type.</em></h1><div class="final-score"><span>Final score</span><strong>{{ score.toString().padStart(4, '0') }}</strong><small>{{ score / 100 }} out of 10 correct</small></div><button class="restart-button" @click="restart">Play again <span>↗</span></button></section>
    <footer><span>© 2025 type.guess</span><span>Made for the font-curious <i>♥</i></span><span>10 fonts · 1 good time</span></footer>
  </main>
</template>
