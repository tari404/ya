<template>
  <div id="ya-logo">
    <span class="ia">[iːa]</span>
    yA
  </div>
</template>

<style lang="sass" scoped>
#ya-logo
  min-width: 128px
  width: 128px
  min-height: 128px
  height: 128px
  font-family: 'Grandiflora One', serif
  font-size: 72px
  line-height: 108px
  text-align: center
  user-select: none
  cursor: none
  background-color: hsl(var(--color-bg))
  position: relative
  &::before, &::after
    content: ''
    width: 16px
    height: 32px
    background-color: hsl(var(--color-ink))
    position: absolute
    z-index: -1
    transition: opacity .14s
    opacity: 0
  &::before
    left: -1px
    top: -1px
  &::after
    right: -1px
    bottom: -1px

  .ia
    position: absolute
    top: 16px
    left: 26px
    font-size: 14px
    line-height: 20px
    font-family: 'Noto Serif', 'Noto Serif SC', serif
    opacity: .3

@media (any-hover: hover)
  #ya-logo:hover
    &::before, &::after
      opacity: 1
      transition: opacity .4s
</style>
