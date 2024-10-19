<template>
  <main id="ya-body" ref="body">
    <!-- <div class="ya-header"></div> -->

    <div style="position: relative">
      <YaLogo />

      <YaMenu />
    </div>

    <hr />

    <ContentNavigation v-slot="{ navigation }">
      <YaNavigation :navigation="navigation" />
    </ContentNavigation>

    <hr />

    <div class="ya-doc">
      <div ref="docWrapper">
        <NuxtPage />
      </div>
    </div>

    <hr class="xxl" />

    <div class="ya-end xxl">
      <button :class="docScrolled ? '' : 'disabled'" @click="jump">𝄇</button>
    </div>
  </main>
</template>

<script lang="ts" setup>
useHead({
  meta: [
    { hid: 'twitter:card', name: 'twitter:card', content: 'player' },
    { hid: 'og:image', property: 'og:image', content: '/A.png' },
  ],
})

const docWrapper = ref(null) as Ref<HTMLElement | null>
const body = ref(null) as Ref<HTMLElement | null>

const scrollA = ref(false)
const scrollB = ref(false)

const docScrolled = computed(() => {
  return scrollA.value || scrollB.value
})

const jump = () => {
  if (!docScrolled.value) {
    return
  }
  window.scrollTo({ top: 0, behavior: 'smooth' })
  if (docWrapper.value) {
    docWrapper.value.scrollTo({ top: 0, behavior: 'smooth' })
  }
}

onMounted(() => {
  if (!docWrapper.value || !body.value) {
    return
  }
  const docWrapperEl = docWrapper.value
  // const bodyEl = body.value

  window.onscroll = () => {
    scrollA.value = !!window.scrollY
    // if (window.scrollY > 32) {
    //   const navHeight =
    //     docWrapperEl.getBoundingClientRect().top - bodyEl.getBoundingClientRect().top
    //   bodyEl.style.marginTop = 32 - navHeight + 'px'
    // }
  }
  docWrapperEl.onscroll = () => {
    scrollB.value = !!docWrapperEl.scrollTop
  }
})
</script>

<style lang="sass">
@import url('https://fonts.googleapis.com/css2?family=Grandiflora+One&family=Noto+Music&family=Noto+Serif&family=Noto+Serif+SC&display=swap')
@import '~/css/reset.sass'
@import '~/css/style.sass'

#ya-body
  display: flex
  align-items: center
  padding: 32px
  // background-color: hsl(var(--color-bg))
  gap: 4em
  height: 100dvh
  overflow: hidden
  transition: gap .4s, margin-top .6s
  > hr
    flex: 0 0 1px
    margin: 0
    height: 2em
    width: 1px
    border: none
    background-color: hsl(var(--color-ink-secondary))

.ya-header
  position: fixed
  top: 0
  left: 0
  width: 100%
  height: 48px
  background-color: hsl(var(--color-bg))
  border-bottom: 1px solid lighthsl(var(--color-ink-secondary))
  box-shadow: 0 2px 4px rgba(black, .05)
  z-index: 10

.ya-doc
  flex: 10 0 25em
  margin: 0 2em
  max-width: 36em
  max-height: 100%
  display: flex
  position: relative
  &::before, &::after
    content: ''
    position: absolute
    left: 0
    right: 0
    height: 2em
    pointer-events: none
    z-index: 1
  &::before
    top: 0
    background-image: linear-gradient(to bottom, hsl(var(--color-bg)), hsl(var(--color-bg) / 0))
  &::after
    bottom: 0
    background-image: linear-gradient(to top, hsl(var(--color-bg)), hsl(var(--color-bg) / 0))
  > div
    margin: 0 -2em
    padding: 4em 2em
    width: 100%
    max-height: 100%
    overflow: scroll
    &::-webkit-scrollbar
      display: none

  ul
    list-style: circle
  ol
    list-style: decimal
  li
    margin: 0.5em 0
  img
    width: 100%
  hr
    margin: 2em 0
    border: none
    height: 1px
    background-color: hsl(var(--color-ink-tertiary))
  h1, h2, h3, h4, h5, h6
    margin: 1.5em 0 1em
    > a
      text-decoration: none
      color: hsl(var(--color-ink))
  em
    font-style: normal
    text-decoration: underline dotted
    // opacity: .3
    // transition: opacity .3s
  // em:hover
  //   opacity: .8
  blockquote
    border-left: 3px solid hsl(var(--color-ink-tertiary))
    padding-left: 15px

.ya-end
  padding-right: 32px
  flex: 1 0 48px
  font-family: 'Noto Music', sans-serif
  display: flex
  justify-content: center
  align-items: center
  > button
    width: 48px
    height: 48px
    display: flex
    justify-content: center
    align-items: center
    cursor: pointer
    transition: opacity .2s
  .disabled
    opacity: .6
    cursor: not-allowed

@media screen and (max-width: 1460px) and (min-width: 961px)
  .xxl
    display: none !important

@media screen and (max-width: 960px)
  body
    overflow: auto
    &::-webkit-scrollbar
      display: none
  #ya-body
    padding: 32px 24px
    height: auto
    flex-direction: column
    gap: 24px
    > hr
      height: 1px
      width: 2em
  .ya-doc
    margin: 0
    flex: 0 0 auto
    max-height: unset
    > div
      margin: 0
      padding: 2em 0
      overflow: visible
    img
      margin: 0 -32px
      width: calc(100% + 64px)
  .ya-end
    padding: 0
    flex: 1 0 64px
</style>
