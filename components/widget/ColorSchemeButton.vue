<script setup lang="ts">
import { CSSProperties } from 'vue'

const isDarkMode = ref(false)

const maskRef = ref<HTMLDivElement | null>(null)

onMounted(() => {
  const storaged = localStorage.getItem('isDarkTheme')
  if (storaged) {
    isDarkMode.value = storaged === 'true'
  } else {
    isDarkMode.value =
      window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches
  }

  document.documentElement.classList.add(isDarkMode.value ? 'dark' : 'light')
})

const maskPos = ref<CSSProperties>()
const toggle = () => {
  const mask = maskRef.value
  if (!mask) return

  const rect = mask.getBoundingClientRect()

  maskPos.value = {
    top: rect.top + 18 + 'px',
    left: rect.left + 18 + 'px',
    opacity: 1,
  }
  requestAnimationFrame(() => {
    maskPos.value = {
      top: rect.top + 18 + 'px',
      left: rect.left + 18 + 'px',
      transform: 'translate3d(-50%, -50%, 0) scale(100)',
      opacity: 1,
    }
  })

  const val = !isDarkMode.value

  isDarkMode.value = val

  mask.addEventListener(
    'transitionend',
    () => {
      maskPos.value = undefined
      localStorage.setItem('isDarkTheme', val.toString())
      document.documentElement.classList.remove(val ? 'light' : 'dark')
      document.documentElement.classList.add(val ? 'dark' : 'light')
    },
    {
      once: true,
    }
  )
}
</script>

<template>
  <button class="color-scheme-button" :class="isDarkMode ? 'dark-mode' : ''" @click="toggle">
    <ClientOnly>
      <svg
        xmlns="http://www.w3.org/2000/svg"
        height="24px"
        viewBox="0 0 24 24"
        width="24px"
        fill="none"
      >
        <defs>
          <!-- r: 3.4 -> 7 -->
          <circle id="p1" class="p1" cx="12" cy="12" />
          <!-- r: 0 -> 6.8 -->
          <circle id="p2" class="p2" cx="16.3" cy="7.7" />
          <!-- width: 3.4 -> 0 -->
          <rect id="p3" class="p3" x="1.8" y="12" height="0.0001" />
        </defs>

        <mask id="c1">
          <rect width="24" height="24" fill="white" />
          <circle class="p2" cx="16.3" cy="7.7" fill="black" stroke="white" />
        </mask>

        <clipPath id="c2">
          <circle class="p1" cx="12" cy="12" />
        </clipPath>

        <circle class="p1" cx="12" cy="12" mask="url(#c1)" />
        <circle class="p2" cx="16.3" cy="7.7" clip-path="url(#c2)" />

        <rect class="p3" x="1.8" y="12" height="0.0001" transform="rotate(0, 12, 12)" />
        <rect class="p3" x="1.8" y="12" height="0.0001" transform="rotate(45, 12, 12)" />
        <rect class="p3" x="1.8" y="12" height="0.0001" transform="rotate(90, 12, 12)" />
        <rect class="p3" x="1.8" y="12" height="0.0001" transform="rotate(135, 12, 12)" />
        <rect class="p3" x="1.8" y="12" height="0.0001" transform="rotate(180, 12, 12)" />
        <rect class="p3" x="1.8" y="12" height="0.0001" transform="rotate(-135, 12, 12)" />
        <rect class="p3" x="1.8" y="12" height="0.0001" transform="rotate(-90, 12, 12)" />
        <rect class="p3" x="1.8" y="12" height="0.0001" transform="rotate(-45, 12, 12)" />
      </svg>
    </ClientOnly>

    <Teleport to="body" :disabled="!maskPos">
      <div ref="maskRef" class="color-mask" :style="maskPos"></div>
    </Teleport>
  </button>
</template>

<style lang="sass">
@mixin sun
  .p1
    transition: r .3s .1s
    r: 3.4px
  .p2
    transition: r .3s
    r: 0px
  .p3
    transition: width .2s .2s
    width: 3.2px

@mixin moon
  .p1
    transition: r .4s
    r: 7px
  .p2
    transition: r .2s .2s
    r: 6.8px
  .p3
    transition: width .3s
    width: 0

.color-scheme-button
  @include sun
  width: 36px
  height: 36px
  padding: 6px
  border-radius: 9999px
  display: block
  background: hsl(var(--color-bg))
  stroke: hsl(var(--color-ink))
  cursor: pointer
  position: relative

.color-mask
  position: absolute
  top: 50%
  left: 50%
  transform: translate3d(-50%, -50%, 0)
  mix-blend-mode: difference
  z-index: 100
  width: 36px
  height: 36px
  border-radius: 9999px
  background: #fff
  transition: transform .5s linear
  opacity: 0

.dark-mode
  @include moon

@media (any-hover: hover)
  .color-scheme-button:hover
    @include moon
    .color-mask
      opacity: 1
      transition: opacity .6s
  .dark-mode:hover
    @include sun
</style>
