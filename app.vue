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
@import url('https://fonts.googleapis.com/css2?family=Grandiflora+One&family=Noto+Music&family=Noto+Serif&family=Noto+Serif+SC&display=swap')
@import '~/css/reset.sass'
</style>
