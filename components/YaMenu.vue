<template>
  <div id="ya-menu">
    <WidgetColorSchemeButton />
  </div>
</template>

<style lang="sass">
#ya-menu
  width: 36px

  display: flex
  flex-direction: column
  align-items: center

  position: absolute
  left: 50%
  top: 100%
  transform: translate3d(-50%, 12px, 0)

@media screen and (max-width: 960px)
  #ya-menu
    position: absolute
    left: calc(50% + 50vw)
    top: 50%
    transform: translate3d(-60px, -50%, 0)
</style>
