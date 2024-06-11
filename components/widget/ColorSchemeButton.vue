<script setup lang="ts">
const isDarkMode = ref(false)

onMounted(() => {
  const storaged = localStorage.getItem('isDarkTheme')
  if (storaged) {
    isDarkMode.value = storaged === 'true'
  } else {
    isDarkMode.value =
      window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches
  }
})

const toggle = () => {
  isDarkMode.value = !isDarkMode.value
}

watch(isDarkMode, (v) => {
  localStorage.setItem('isDarkTheme', v.toString())
  const html = document.documentElement
  html.classList.remove(v ? 'light' : 'dark')
  html.classList.add(v ? 'dark' : 'light')
})
</script>

<template>
  <button class="color-scheme-button">
    <ClientOnly>
      <svg
        xmlns="http://www.w3.org/2000/svg"
        height="24px"
        viewBox="0 0 24 24"
        width="24px"
        fill="none"
        @click="toggle"
      >
        <defs>
          <!-- r: 3.4 -> 7 -->
          <circle
            id="p1"
            cx="12"
            cy="12"
            :style="`transition: r ${isDarkMode ? '.4s' : '.3s .1s'}; r: ${isDarkMode ? 7 : 3.4}px`"
          />
          <!-- r: 0 -> 6.8 -->
          <circle
            id="p2"
            cx="16.3"
            cy="7.7"
            :style="`transition: r ${isDarkMode ? '.2s .2s' : '.3s'}; r: ${isDarkMode ? 6.8 : 0}px`"
          />
          <!-- width: 3.4 -> 0 -->
          <rect
            id="p3"
            x="1.8"
            y="12"
            height="0.0001"
            :style="`transition: width ${isDarkMode ? '.3s' : '.2s .2s'}; width: ${
              isDarkMode ? 0 : 3.2
            }px`"
          />
        </defs>

        <mask id="c1">
          <rect width="24" height="24" fill="white" />
          <use href="#p2" fill="black" stroke="white" />
        </mask>

        <clipPath id="c2">
          <use href="#p1" />
        </clipPath>

        <use mask="url(#c1)" href="#p1" />
        <use clip-path="url(#c2)" href="#p2" />

        <use href="#p3" transform="rotate(0, 12, 12)" />
        <use href="#p3" transform="rotate(45, 12, 12)" />
        <use href="#p3" transform="rotate(90, 12, 12)" />
        <use href="#p3" transform="rotate(135, 12, 12)" />
        <use href="#p3" transform="rotate(180, 12, 12)" />
        <use href="#p3" transform="rotate(-135, 12, 12)" />
        <use href="#p3" transform="rotate(-90, 12, 12)" />
        <use href="#p3" transform="rotate(-45, 12, 12)" />
      </svg>
    </ClientOnly>
  </button>
</template>

<style lang="sass" scoped>
.color-scheme-button
  width: 36px
  height: 36px
  padding: 5px
  border-radius: 9999px
  display: block
  stroke: oklch(var(--color-ink))
  border: 1px solid oklch(var(--color-ink-tertiary) / 0)
  cursor: pointer
  transition: border-color .14s

@media (any-hover: hover)
  .color-scheme-button:hover
    border-color: oklch(var(--color-ink) / 0.75)
    transition: border-color .4s
</style>
