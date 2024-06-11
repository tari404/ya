<template>
  <div
    id="ya-nav"
    @wheel.prevent="onwheel"
    @touchstart.prevent="ontouchstart"
    @touchmove.prevent="ontouchmove"
    @touchend="ontouchend"
  >
    <div class="sub-nav-wrapper">
      <span>-</span>
      <div
        :style="{
          transform: `translateX(${-112 * focused}px)`,
          height: subNavDisplayHeight ? subNavDisplayHeight + 'em' : 'auto',
        }"
      >
        <ul
          v-for="(group, i) in navigationGroup"
          :key="i"
          :aria-hidden="focused !== i || undefined"
          class="sub-nav"
        >
          <li v-for="link in group.children" :key="link._path">
            <NuxtLink
              :to="link._path"
              :tabindex="focused !== i ? -1 : undefined"
              @touchstart.stop
              >{{ link.title }}</NuxtLink
            >
          </li>
        </ul>
      </div>
      <span>-</span>
    </div>
    <div
      class="main-nav"
      :style="{
        '--translate': (touchOffset + scrollOffset).toFixed(4) + 'px',
      }"
    >
      <button
        v-for="(group, i) in navigationGroup"
        :key="i"
        :style="{ transform: mainNavTagOffset(i) }"
        :class="{ 'router-link-exact-active': focusedNavGroupIdx === i }"
        :tabindex="focused === i ? -1 : undefined"
        @click="toggle(i)"
        @touchstart.stop
      >
        {{ group.title }}
      </button>
    </div>
  </div>
  <!-- <Log :data="navigation" /> -->
</template>

<script lang="ts" setup>
// 当前假设不存在三级嵌套路由。如果增加三级嵌套路由，则 UI 和规则均需要做出更改
interface ISimpleNavigation {
  title: string
  _path: string
}
interface INavigation extends ISimpleNavigation {
  children?: ISimpleNavigation[]
}

const props = defineProps<{ navigation: INavigation[] }>()

const navigationGroup = computed(() => {
  const homeNav = {
    title: '主页',
    children: [] as ISimpleNavigation[],
  }
  const group = [homeNav]

  for (const nav of props.navigation) {
    if (nav.children) {
      group.push({
        title: nav.title,
        children: nav.children,
      })
    } else {
      homeNav.children.push(nav)
    }
  }

  return group
})

// const route = useRoute()
// const path = route.path.replace(/\/$/, '')
// let focusedNum = navigationGroup.value.findIndex((group) => {
//   return group.children.find((nav) => nav._path === path)
// })
// if (focusedNum < 0) {
//   focusedNum = 0
// }

const focusedNavGroupIdx = computed(() => {
  const path = useRoute().path.replace(/\/$/, '')
  const idx = navigationGroup.value.findIndex((group) =>
    group.children.find((nav) => nav._path === path)
  )
  return idx < 0 ? 0 : idx
})

const subNavDisplayHeight = ref(
  (navigationGroup.value[focusedNavGroupIdx.value] || { children: [] }).children.length * 1.8
)

const focused = ref(focusedNavGroupIdx.value)
const toggle = (idx: number) => {
  focused.value = idx
}
watch(focused, (idx) => {
  subNavDisplayHeight.value = (navigationGroup.value[idx] || { children: [] }).children.length * 1.8
})

const pageWidth = ref(process.client ? window.innerWidth : 0)
const resize = () => {
  pageWidth.value = 0 // 强制触发 mainNavTagOffset 变化
  pageWidth.value = window.innerWidth
}

const mainNavTagOffset = (idx: number) => {
  const w = pageWidth.value
  if (w === 0) {
    return 'translateX(0)'
  }

  const dir = w > 960 ? 'Y' : 'X'
  const distance = w > 960 ? 7.6 : 7

  return `translate${dir}(${(idx - focused.value) * distance}em)`
}

let scrollOffsetNum = 0
const scrollOffset = ref(0)
const onwheel = (e: WheelEvent) => {
  e.preventDefault()

  scrollOffsetNum -= e.deltaY * 1.2
  if (focused.value === 0) {
    scrollOffsetNum = Math.min(scrollOffsetNum, 50)
  } else if (focused.value === navigationGroup.value.length - 1) {
    scrollOffsetNum = Math.max(scrollOffsetNum, -50)
  }

  if (scrollOffsetNum > 60) {
    focused.value -= 1
    scrollOffsetNum = 0
  } else if (scrollOffsetNum < -60) {
    focused.value += 1
    scrollOffsetNum = 0
  }
}

let touchOffsetNum = 0
let touchX = 0
const touchOffset = ref(0)
const ontouchstart = (e: TouchEvent) => {
  e.preventDefault()
  touchX = e.touches[0].pageX
}
const ontouchmove = (e: TouchEvent) => {
  e.preventDefault()
  touchOffsetNum += e.touches[0].pageX - touchX
  touchX = e.touches[0].pageX

  if (focused.value === 0) {
    touchOffsetNum = Math.min(touchOffsetNum, 50)
  } else if (focused.value === navigationGroup.value.length - 1) {
    touchOffsetNum = Math.max(touchOffsetNum, -50)
  }

  if (touchOffsetNum > 60) {
    focused.value -= 1
    touchOffsetNum = 0
  } else if (touchOffsetNum < -60) {
    focused.value += 1
    touchOffsetNum = 0
  }
}
const ontouchend = () => {
  touchOffsetNum = 0
}

let raf = 0
const update = () => {
  scrollOffsetNum *= 0.98
  scrollOffset.value = Math.max(scrollOffset.value * 0.98, scrollOffsetNum)

  touchOffset.value = Math.max(touchOffset.value * 0.98, touchOffsetNum)
  raf = requestAnimationFrame(update)
}

onMounted(() => {
  resize()
  window.addEventListener('resize', resize)
  raf = requestAnimationFrame(update)
})
onBeforeUnmount(() => {
  window.removeEventListener('resize', resize)
  cancelAnimationFrame(raf)
})
</script>

<style lang="sass">
#ya-nav
  flex: 0 0 auto
  --nav-width: 72px
  position: relative
  &::before
    content: ''
    width: 100%
    height: 100dvh
    padding: 24px 48px
    position: absolute
    top: 50%
    left: 50%
    transform: translate3d(-50%, -50%, 0)

.main-nav
  width: var(--nav-width)
  transform: translateY(var(--translate))
  position: absolute
  top: 50%
  left: 0
  color: oklch(var(--color-ink-secondary))
  button
    position: absolute
    top: -0.9em
    left: 0
    width: 100%
    text-align: center
    cursor: pointer
    transition: transform .5s

.sub-nav-wrapper
  background-color: oklch(var(--color-bg))
  margin: -12px -20px
  padding: 12px 20px
  width: var(--nav-width)
  box-sizing: content-box
  overflow: hidden
  position: relative
  display: flex
  flex-direction: column
  gap: 8px
  z-index: 1
  &::before, &::after
    content: ''
    position: absolute
    left: 0
    width: 100%
    height: 16px
    z-index: 1
  &::before
    top: 0
    background-image: linear-gradient(to bottom, oklch(var(--color-bg)), oklch(var(--color-bg) / 0))
  &::after
    bottom: 0
    background-image: linear-gradient(to top, oklch(var(--color-bg)), oklch(var(--color-bg) / 0))
  > span
    display: block
    text-align: center
  > div
    margin: 0 -20px
    width: fit-content
    display: flex
    align-items: center
    transition: transform .6s, height .3s .4s
.sub-nav
  padding: 0 20px
  flex: 0 0 var(--nav-width)
  width: var(--nav-width)
  box-sizing: content-box
  display: flex
  flex-direction: column
  align-items: center
  a
    padding: 2px 12px
    white-space: nowrap

.router-link-exact-active
  &::before, &::after
    content: ''
    width: 4px
    height: 4px
    border-top: 1px solid oklch(var(--color-ink-secondary))
    position: absolute
    top: 50%
  &::before
    border-right: 1px solid oklch(var(--color-ink-secondary))
    left: 0px
    transform: translateY(-50%) rotate(45deg)
  &::after
    border-left: 1px solid oklch(var(--color-ink-secondary))
    right: 0px
    transform: translateY(-50%) rotate(-45deg)

@media screen and (max-width: 960px)
  #ya-nav
    &::before
      width: 100vw
      height: 100%
  .main-nav
    transform: translateX(var(--translate))
  .sub-nav-wrapper
    > span
      display: none
</style>
