<script setup>
import { computed, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import rocketUrl from '../rocket.png'

const astros = [
  {
    title: 'Mês 1',
    subtitle: 'O início',
    message: 'Uma mensagem tímida iniciou a viagem mais incrível da minha vida  ',
    kind: 'planet',
    size: 150,
    decor: 'rings',
    palette: ['#6a7bff', '#ff5ca5', 'rgba(106, 123, 255, 0.26)'],
  },
  {
    title: 'Mês 2',
    subtitle: 'Planeta da beleza',
    message: 'Descobri um mundo onde tua beleza deixa meus dias mais lindos.',
    kind: 'planet',
    size: 135,
    decor: 'hearts',
    palette: ['#ff7a9e', '#8cd2ff', 'rgba(255, 122, 158, 0.22)'],
  },
  {
    title: 'Mês 3',
    subtitle: 'Planeta do carinho',
    message: 'Teu carinho cuida de mim todos os dias <3.',
    kind: 'planet',
    size: 160,
    decor: 'moon',
    palette: ['#7bf0ff', '#826aff', 'rgba(123, 240, 255, 0.22)'],
  },
  {
    title: 'Mês 4',
    subtitle: 'Planeta da alegria',
    message: 'Seu sorriso é meu maior prazer, e a felicidade que tu me traz é inigualavel.',
    kind: 'planet',
    size: 142,
    decor: 'craters',
    palette: ['#ffd46a', '#ff5ca5', 'rgba(255, 212, 106, 0.2)'],
  },
  {
    title: 'Mês 5',
    subtitle: 'Planeta do apoio',
    message: 'Com você, sinto que posso fazer tudo, e alcançar qualquer objetivo não importa o quão distante pareça.',
    kind: 'planet',
    size: 152,
    decor: 'rings',
    palette: ['#40cbff', '#6a7bff', 'rgba(64, 203, 255, 0.22)'],
  },
  {
    title: 'Mês 6',
    subtitle: 'Planeta do beijo',
    message: 'Cada  beijo seu é melhor que o ultimo, e o próximo há de ser ainda melhor.',
    kind: 'planet',
    size: 130,
    decor: 'hearts',
    palette: ['#ff5ca5', '#ffb86a', 'rgba(255, 92, 165, 0.22)'],
  },
  {
    title: 'Mês 7',
    subtitle: 'Planeta da paz',
    message: 'No meio de tanto caos, achei em ti um mundo inteiro feito de paz.',
    kind: 'planet',
    size: 158,
    decor: 'moon',
    palette: ['#8cd2ff', '#b07bff', 'rgba(176, 123, 255, 0.2)'],
  },
  {
    title: 'Mês 8',
    subtitle: 'Planeta do xamego',
    message: 'Quando sinto teu toque na minha pele, sinto como se nada mais importasse no mundo.',
    kind: 'planet',
    size: 145,
    decor: 'craters',
    palette: ['#6affb9', '#40cbff', 'rgba(106, 255, 185, 0.18)'],
  },
  {
    title: 'Mês 9',
    subtitle: 'Planeta da saudade',
    message: 'A saudade aperta meu coração, mas ao mesmo tempo é boa por que sei que é por você, e sei que irei te ver e tudo ficará perfeito.',
    kind: 'planet',
    size: 155,
    decor: 'rings',
    palette: ['#ffb86a', '#826aff', 'rgba(255, 184, 106, 0.2)'],
  },
  {
    title: 'Mês 10',
    subtitle: 'Planeita dos cheirinhos',
    message: 'Quando sinto teu cheiro, meu mundo todo se torna um lugar mais feliz.',
    kind: 'planet',
    size: 138,
    decor: 'hearts',
    palette: ['#ff80a6', '#826aff', 'rgba(255, 128, 166, 0.2)'],
  },
  {
    title: 'Mês 11',
    subtitle: 'Planeta do futuro',
    message: 'É muito bom viver no mundo do futuro, pois sei que você está lá',
    kind: 'planet',
    size: 162,
    decor: 'moon',
    palette: ['#6a7bff', '#40cbff', 'rgba(106, 123, 255, 0.22)'],
  },
  {
    title: '1 ano',
    subtitle: 'A minha estrela guia',
    message: 'Feliz 1 ano de namoro. Eu te amo, obrigado por ser minha estrela guia, por iluminar minha vida e fazer todos os dias mais felizes, espero poder orbitar seu brilho para sempre <3.',
    kind: 'star',
  },
]

const astrosInViewOrder = astros.slice().reverse()

const scrollY = ref(0)
const viewportHeight = ref(1)
let rafId = 0

const lastIndex = astros.length - 1

const clamp = (value, min, max) => Math.min(max, Math.max(min, value))
const lerp = (a, b, t) => a + (b - a) * t

const xForIndex = (index) => {
  if (index >= lastIndex) return 0
  return index % 2 === 0 ? -190 : 190
}

const segmentIndex = computed(() => {
  const vh = viewportHeight.value
  if (!vh) return 0
  return clamp(Math.floor(progressY.value / vh), 0, Math.max(0, lastIndex - 1))
})

const segmentProgress = computed(() => {
  const vh = viewportHeight.value
  if (!vh) return 0
  return clamp((progressY.value - segmentIndex.value * vh) / vh, 0, 1)
})

const activeIndex = computed(() => {
  const vh = viewportHeight.value
  if (!vh) return 0
  return clamp(Math.round(progressY.value / vh), 0, lastIndex)
})

const isReady = ref(false)
const isFinalActive = computed(() => isReady.value && activeIndex.value === lastIndex)
const finalFxKey = ref(0)
watch(isFinalActive, (value) => {
  if (value) finalFxKey.value += 1
})

const rocketX = computed(() => {
  const i = segmentIndex.value
  const t = segmentProgress.value
  return lerp(xForIndex(i), xForIndex(i + 1), t)
})

const maxScroll = computed(() => {
  return Math.max(1, lastIndex * viewportHeight.value)
})

const progressY = computed(() => {
  return clamp(maxScroll.value - scrollY.value, 0, maxScroll.value)
})

const rocketY = computed(() => {
  const vh = viewportHeight.value
  const totalScrollable = Math.max(1, lastIndex * vh)
  const totalProgress = clamp(progressY.value / totalScrollable, 0, 1)
  return -(totalProgress * vh * 0.46) - segmentProgress.value * 40
})

const rocketRotation = computed(() => {
  const i = segmentIndex.value
  const delta = xForIndex(i + 1) - xForIndex(i)
  if (Math.abs(delta) < 1) return 0
  return delta > 0 ? 10 : -10
})

const rocketTransform = computed(() => {
  return `translate3d(-50%, -50%, 0) translate3d(${rocketX.value}px, ${rocketY.value}px, 0) rotate(${rocketRotation.value}deg)`
})

const progressionIndexFromDom = (domIndex) => lastIndex - domIndex

const astroStyle = (astro) => {
  if (astro.kind !== 'planet') return undefined
  const [a, b, glow] = astro.palette || ['#6a7bff', '#ff5ca5', 'rgba(106, 123, 255, 0.26)']
  return {
    '--astro-size': `${astro.size || 150}px`,
    '--planet-a': a,
    '--planet-b': b,
    '--planet-glow': glow,
    '--heart-color': '#ff7aa8',
  }
}

const onResize = () => {
  const previousMaxScroll = maxScroll.value
  const previousProgress = clamp(previousMaxScroll - scrollY.value, 0, previousMaxScroll)
  viewportHeight.value = window.innerHeight || 1
  const nextMaxScroll = maxScroll.value
  window.scrollTo({ top: nextMaxScroll - previousProgress, left: 0, behavior: 'auto' })
}

const onScroll = () => {
  if (rafId) return
  rafId = window.requestAnimationFrame(() => {
    scrollY.value = window.scrollY || window.pageYOffset || 0
    rafId = 0
  })
}

onMounted(() => {
  onResize()
  onScroll()
  window.addEventListener('resize', onResize, { passive: true })
  window.addEventListener('scroll', onScroll, { passive: true })
  window.requestAnimationFrame(() => {
    window.scrollTo({ top: maxScroll.value, left: 0, behavior: 'auto' })
    onScroll()
    window.requestAnimationFrame(() => {
      isReady.value = true
    })
  })
})

onBeforeUnmount(() => {
  window.removeEventListener('resize', onResize)
  window.removeEventListener('scroll', onScroll)
  if (rafId) window.cancelAnimationFrame(rafId)
})
</script>

<template>
  <div class="scene">
    <div class="rocket" :style="{ transform: rocketTransform }" aria-hidden="true">
      <img class="rocket__img" :src="rocketUrl" alt="" />
      <div class="rocket__trail" />
    </div>

    <section
      v-for="(astro, index) in astrosInViewOrder"
      :key="astro.title"
      class="segment"
      :class="{
        'segment--final': astro.kind === 'star',
        'segment--left': progressionIndexFromDom(index) % 2 === 0,
        'segment--right': progressionIndexFromDom(index) % 2 === 1,
      }"
    >
      <div class="segment__content">
        <div
          class="astro"
          :class="{
            'astro--final': astro.kind === 'star',
            'astro--final-active': astro.kind === 'star' && isFinalActive,
          }"
          :style="astroStyle(astro)"
        >
          <div class="astro__body" />
          <div v-if="astro.kind === 'planet' && astro.decor === 'rings'" class="astro__rings" />
          <div v-if="astro.kind === 'planet' && astro.decor === 'craters'" class="astro__craters">
            <div v-for="n in 4" :key="n" class="astro__crater" :style="{ '--i': n }" />
          </div>
          <div v-if="astro.kind === 'planet' && astro.decor === 'hearts'" class="astro__orbit">
            <div v-for="n in 6" :key="n" class="astro__heart" :style="{ '--i': n }" />
          </div>
          <div v-if="astro.kind === 'planet' && astro.decor === 'moon'" class="astro__orbit astro__orbit--moon">
            <div class="astro__moon" />
          </div>
          <div v-if="astro.kind === 'star'" class="astro__glow" />
        </div>

        <div v-if="astro.kind === 'star' && isFinalActive" :key="finalFxKey" class="finalFx" aria-hidden="true">
          <div class="finalFx__flash" />
          <div class="finalFx__ring finalFx__ring--a" />
          <div class="finalFx__ring finalFx__ring--b" />
          <div class="finalFx__sparks">
            <div v-for="n in 18" :key="n" class="finalFx__spark" :style="{ '--a': `${n * 20}deg` }" />
          </div>
        </div>

        <div
          v-if="activeIndex === progressionIndexFromDom(index)"
          class="message"
          :class="{ 'message--final': astro.kind === 'star' }"
        >
          <div class="message__title">{{ astro.title }}</div>
          <div class="message__subtitle">{{ astro.subtitle }}</div>
          <div class="message__text">{{ astro.message }}</div>
        </div>
      </div>
    </section>
  </div>
</template>

<style scoped>
.scene {
  position: relative;
  width: 100%;
  z-index: 0;
  isolation: isolate;
  background:
    radial-gradient(1200px 800px at 50% 10%, rgba(130, 106, 255, 0.22), transparent 60%),
    radial-gradient(900px 700px at 10% 60%, rgba(64, 203, 255, 0.15), transparent 60%),
    radial-gradient(900px 700px at 90% 70%, rgba(255, 98, 175, 0.12), transparent 60%),
    #050610;
}

.scene::before {
  content: '';
  position: fixed;
  inset: 0;
  z-index: 0;
  pointer-events: none;
  background:
    radial-gradient(1px 1px at 15% 25%, rgba(255, 255, 255, 0.9) 60%, transparent 65%),
    radial-gradient(1px 1px at 72% 18%, rgba(255, 255, 255, 0.8) 60%, transparent 65%),
    radial-gradient(1px 1px at 38% 62%, rgba(255, 255, 255, 0.75) 60%, transparent 65%),
    radial-gradient(1px 1px at 86% 77%, rgba(255, 255, 255, 0.7) 60%, transparent 65%),
    radial-gradient(1px 1px at 24% 84%, rgba(255, 255, 255, 0.8) 60%, transparent 65%),
    radial-gradient(1px 1px at 58% 45%, rgba(255, 255, 255, 0.7) 60%, transparent 65%);
  opacity: 0.85;
}

.segment {
  position: relative;
  z-index: 1;
  height: 100vh;
  min-height: 620px;
  display: grid;
  align-items: center;
}

.segment__content {
  position: relative;
  height: 100%;
  width: 100%;
}

.segment--left .astro {
  left: 18%;
}

.segment--right .astro {
  left: 82%;
}

.segment--final {
  height: 100vh;
}

.astro {
  position: absolute;
  top: 45%;
  transform: translate3d(-50%, -50%, 0);
  width: var(--astro-size, 150px);
  height: var(--astro-size, 150px);
  display: grid;
  place-items: center;
}

.astro--final {
  left: 50% !important;
  top: 50%;
  width: 220px;
  height: 220px;
}

.astro__body {
  position: relative;
  z-index: 2;
  width: 100%;
  height: 100%;
  border-radius: 999px;
  background:
    radial-gradient(circle at 30% 30%, rgba(255, 255, 255, 0.35), transparent 50%),
    radial-gradient(circle at 65% 70%, rgba(0, 0, 0, 0.25), transparent 55%),
    linear-gradient(135deg, var(--planet-a, rgba(97, 121, 255, 0.85)), var(--planet-b, rgba(255, 92, 165, 0.7)));
  box-shadow:
    0 20px 60px rgba(0, 0, 0, 0.35),
    0 0 35px var(--planet-glow, rgba(97, 121, 255, 0.22));
}

.astro__rings {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 170%;
  height: 56%;
  transform: translate3d(-50%, -50%, 0) rotate(22deg);
  border-radius: 999px;
  border: 3px solid rgba(255, 255, 255, 0.18);
  box-shadow: 0 0 26px var(--planet-glow, rgba(97, 121, 255, 0.22));
  opacity: 0.95;
  z-index: 1;
}

.astro__rings::after {
  content: '';
  position: absolute;
  inset: 10% 12%;
  border-radius: 999px;
  border: 1px solid rgba(255, 255, 255, 0.18);
  opacity: 0.7;
}

.astro__craters {
  position: absolute;
  inset: 0;
  z-index: 3;
  pointer-events: none;
}

.astro__crater {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 18px;
  height: 18px;
  border-radius: 999px;
  background: rgba(0, 0, 0, 0.18);
  box-shadow: inset 0 2px 10px rgba(255, 255, 255, 0.08);
  transform: translate3d(-50%, -50%, 0);
  opacity: 0.7;
}

.astro__crater:nth-child(1) {
  transform: translate3d(-50%, -50%, 0) translate3d(-22px, -18px, 0) scale(0.85);
}

.astro__crater:nth-child(2) {
  transform: translate3d(-50%, -50%, 0) translate3d(26px, -6px, 0) scale(1.05);
}

.astro__crater:nth-child(3) {
  transform: translate3d(-50%, -50%, 0) translate3d(-6px, 28px, 0) scale(0.9);
}

.astro__crater:nth-child(4) {
  transform: translate3d(-50%, -50%, 0) translate3d(20px, 22px, 0) scale(0.75);
}

.astro__orbit {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 190%;
  height: 190%;
  transform: translate3d(-50%, -50%, 0);
  z-index: 4;
  animation: orbit 9s linear infinite;
  pointer-events: none;
}

.astro__orbit--moon {
  width: 220%;
  height: 220%;
  animation-duration: 11s;
}

.astro__heart {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 14px;
  height: 14px;
  background: var(--heart-color, #ff7aa8);
  transform: rotate(calc(var(--i) * 60deg)) translateX(calc(var(--astro-size, 150px) * 0.76)) rotate(-45deg);
  border-radius: 3px;
  filter: drop-shadow(0 6px 10px rgba(0, 0, 0, 0.35));
  opacity: 0.95;
}

.astro__heart::before,
.astro__heart::after {
  content: '';
  position: absolute;
  width: 14px;
  height: 14px;
  background: var(--heart-color, #ff7aa8);
  border-radius: 999px;
}

.astro__heart::before {
  left: 0;
  top: -7px;
}

.astro__heart::after {
  left: 7px;
  top: 0;
}

.astro__moon {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 22px;
  height: 22px;
  border-radius: 999px;
  background:
    radial-gradient(circle at 30% 35%, rgba(255, 255, 255, 0.55), transparent 45%),
    linear-gradient(135deg, rgba(255, 255, 255, 0.32), rgba(0, 0, 0, 0.18));
  transform: rotate(20deg) translateX(calc(var(--astro-size, 150px) * 0.92));
  box-shadow: 0 10px 26px rgba(0, 0, 0, 0.28);
}

@keyframes orbit {
  to {
    transform: translate3d(-50%, -50%, 0) rotate(360deg);
  }
}

.astro--final .astro__body {
  background:
    radial-gradient(circle at 50% 50%, rgba(255, 255, 255, 0.95), rgba(255, 235, 160, 0.95) 35%, rgba(255, 137, 68, 0.85) 70%, rgba(255, 80, 28, 0.6));
  box-shadow:
    0 0 50px rgba(255, 235, 160, 0.55),
    0 0 120px rgba(255, 160, 80, 0.35),
    0 0 220px rgba(255, 120, 50, 0.22);
}

.astro--final-active .astro__body {
  animation: starPulse 1.25s ease-in-out infinite;
}

.astro__glow {
  position: absolute;
  inset: -40px;
  border-radius: 999px;
  background: radial-gradient(circle at 50% 50%, rgba(255, 255, 255, 0.35), transparent 65%);
  filter: blur(6px);
}

.finalFx {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate3d(-50%, -50%, 0);
  width: 560px;
  height: 560px;
  pointer-events: none;
  z-index: 6;
}

.finalFx__flash {
  position: absolute;
  inset: -80px;
  border-radius: 999px;
  background: radial-gradient(circle at 50% 50%, rgba(255, 255, 255, 0.55), transparent 60%);
  filter: blur(4px);
  animation: finalFlash 900ms ease-out both;
}

.finalFx__ring {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 40px;
  height: 40px;
  border-radius: 999px;
  border: 2px solid rgba(255, 235, 160, 0.7);
  transform: translate3d(-50%, -50%, 0);
  box-shadow:
    0 0 25px rgba(255, 235, 160, 0.35),
    0 0 70px rgba(255, 160, 80, 0.18);
  animation: ringBurst 1200ms ease-out both;
}

.finalFx__ring--b {
  border-color: rgba(255, 255, 255, 0.55);
  animation-duration: 1500ms;
  animation-delay: 120ms;
}

.finalFx__sparks {
  position: absolute;
  inset: 0;
}

.finalFx__spark {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 4px;
  height: 18px;
  border-radius: 999px;
  background: linear-gradient(to bottom, rgba(255, 255, 255, 0.9), rgba(255, 235, 160, 0.7), transparent);
  transform: translate3d(-50%, -50%, 0) rotate(var(--a, 0deg));
  transform-origin: 50% 100%;
  animation: sparkFly 1100ms cubic-bezier(0.2, 0.75, 0.15, 1) both;
  opacity: 0.95;
}

.finalFx__spark:nth-child(2n) {
  height: 22px;
  animation-duration: 1250ms;
}

.finalFx__spark:nth-child(3n) {
  height: 14px;
  animation-duration: 980ms;
}

@keyframes finalFlash {
  0% {
    opacity: 0;
    transform: scale(0.6);
  }
  18% {
    opacity: 1;
    transform: scale(1);
  }
  100% {
    opacity: 0;
    transform: scale(1.35);
  }
}

@keyframes ringBurst {
  0% {
    opacity: 0;
    transform: translate3d(-50%, -50%, 0) scale(0.35);
  }
  20% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    transform: translate3d(-50%, -50%, 0) scale(12);
  }
}

@keyframes sparkFly {
  0% {
    opacity: 0;
    transform: translate3d(-50%, -50%, 0) rotate(var(--a, 0deg)) translateY(0);
  }
  18% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    transform: translate3d(-50%, -50%, 0) rotate(var(--a, 0deg)) translateY(-260px);
  }
}

@keyframes starPulse {
  0%,
  100% {
    transform: scale(1);
    filter: brightness(1);
  }
  50% {
    transform: scale(1.03);
    filter: brightness(1.18);
  }
}

.message {
  position: absolute;
  top: 62%;
  left: 50%;
  transform: translate3d(-50%, -50%, 0);
  width: min(520px, calc(100vw - 48px));
  padding: 16px 18px;
  border-radius: 16px;
  background: rgba(10, 12, 28, 0.62);
  border: 1px solid rgba(255, 255, 255, 0.12);
  backdrop-filter: blur(12px);
  box-shadow: 0 18px 60px rgba(0, 0, 0, 0.35);
  text-align: center;
}

.message--final {
  top: 70%;
  background: rgba(10, 12, 28, 0.52);
  border-color: rgba(255, 235, 160, 0.25);
}

.message__title {
  font-size: 18px;
  font-weight: 700;
  letter-spacing: 0.02em;
}

.message__subtitle {
  margin-top: 2px;
  opacity: 0.85;
}

.message__text {
  margin-top: 10px;
  font-size: 18px;
  line-height: 1.4;
}

.rocket {
  position: fixed;
  left: 50%;
  top: 70%;
  z-index: 10;
  width: 120px;
  height: 120px;
  will-change: transform;
  pointer-events: none;
}

.rocket__img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  filter: drop-shadow(0 10px 22px rgba(0, 0, 0, 0.45));
}

.rocket__trail {
  position: absolute;
  left: 50%;
  top: 78%;
  width: 20px;
  height: 90px;
  transform: translateX(-50%);
  background: linear-gradient(to bottom, rgba(140, 210, 255, 0.65), rgba(255, 115, 172, 0.3), transparent 90%);
  filter: blur(0.2px);
  opacity: 0.9;
  border-radius: 999px;
}

@media (max-width: 520px) {
  .segment--left .astro {
    left: 22%;
  }

  .segment--right .astro {
    left: 78%;
  }

  .astro {
    width: 120px;
    height: 120px;
  }

  .astro--final {
    width: 190px;
    height: 190px;
  }

  .rocket {
    width: 96px;
    height: 96px;
  }

  .message__text {
    font-size: 16px;
  }
}
</style>
