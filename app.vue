<template>
  <div>
    <NuxtPage />
  </div>
</template>

<script lang="ts" setup>
useHead({
  meta: [
    { hid: 'twitter:card', name: 'twitter:card', content: 'player' },
    { hid: 'og:image', property: 'og:image', content: '/A.png' },
  ],
})
</script>

<style lang="sass">
@import url('https://fonts.googleapis.com/css2?family=Grandiflora+One&family=Noto+Music&family=Noto+Serif&family=Noto+Serif+SC&display=block')
@import '~/css/reset.sass'

.router-link-exact-active
  &::before, &::after
    content: ''
    width: 4px
    height: 4px
    border-top: 1px solid gray
    position: absolute
    top: 50%
  &::before
    border-right: 1px solid gray
    left: -14px
    transform: translateY(-1px) rotate(45deg)
  &::after
    border-left: 1px solid gray
    right: -14px
    transform: translateY(-1px) rotate(-45deg)
</style>
