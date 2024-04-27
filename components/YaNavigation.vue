<template>
  <div id="ya-nav" @wheel="onwheel">
    <div class="main-nav" :style="{ transform: `translateY(${scrollOffset}px)` }">
      <span
        v-for="(group, i) in navigationGroup"
        :key="i"
        :style="{ transform: mainNavTagOffset(i) }"
        @click="toggle(i)"
      >
        {{ group.title }}
      </span>
    </div>
    <div class="sub-nav-wrapper">
      <span>-</span>
      <div
        :style="{
          transform: `translateX(${-104 * focused}px)`,
          height: subNavDisplayHeight ? subNavDisplayHeight + 'em' : 'auto',
        }"
      >
        <ul v-for="(group, i) in navigationGroup" :key="i" class="sub-nav">
          <li v-for="link in group.children" :key="link._path">
            <NuxtLink :to="link._path">{{ link.title }}</NuxtLink>
          </li>
        </ul>
      </div>
      <span>-</span>
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

const route = useRoute()
const path = route.path.replace(/\/$/, '')
let focusedNum = navigationGroup.value.findIndex((group) => {
  return group.children.find((nav) => nav._path === path)
})
if (focusedNum < 0) {
  focusedNum = 0
}

const subNavDisplayHeight = ref(
  (navigationGroup.value[focusedNum] || { children: [] }).children.length * 1.8
)

const focused = ref(focusedNum)
const toggle = (idx: number) => {
  focused.value = idx
}
watch(focused, (idx) => {
  subNavDisplayHeight.value = (navigationGroup.value[idx] || { children: [] }).children.length * 1.8
})

const pageWidth = ref(0)
const resize = () => {
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
const scrollOffset = ref('0')
const onwheel = (e: WheelEvent) => {
  scrollOffsetNum -= e.deltaY * 0.6
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
let raf = 0
const update = () => {
  scrollOffsetNum *= 0.94
  scrollOffset.value = scrollOffsetNum.toFixed(2)
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
  --nav-width: 64px
  position: relative

.main-nav
  width: var(--nav-width)
  position: absolute
  top: 50%
  left: 0
  color: gray
  span
    position: absolute
    top: -0.9em
    left: 0
    width: 100%
    text-align: center
    cursor: pointer
    transition: transform .5s

.sub-nav-wrapper
  background-color: white
  margin: -16px -20px
  padding: 16px 20px
  width: var(--nav-width)
  box-sizing: content-box
  overflow: hidden
  position: relative
  display: flex
  flex-direction: column
  gap: 12px
  &::before, &::after
    content: ''
    position: absolute
    left: 0
    width: 100%
    height: 16px
  &::before
    top: 0
    background-image: linear-gradient(to bottom, white, rgba(white, 0))
  &::after
    bottom: 0
    background-image: linear-gradient(to top, white, rgba(white, 0))
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
    white-space: nowrap

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

@media screen and (max-width: 960px)
  .sub-nav-wrapper
    > span
      display: none
</style>
