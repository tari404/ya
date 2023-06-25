<template>
  <div id="ya-nav">
    <div class="main-nav">
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

const { path } = useRoute()
const focusedNum = navigationGroup.value.findIndex((group) => {
  return group.children.find((nav) => nav._path === path)
})

const subNavDisplayHeight = ref(
  (navigationGroup.value[focusedNum] || { children: [] }).children.length * 1.8
)

const focused = ref(focusedNum)
const toggle = (idx: number) => {
  focused.value = idx
  subNavDisplayHeight.value = (navigationGroup.value[idx] || { children: [] }).children.length * 1.8
}

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

onMounted(() => {
  resize()
  window.addEventListener('resize', resize)
})
onBeforeUnmount(() => {
  window.removeEventListener('resize', resize)
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
  margin: 0 -20px
  padding: 0 20px
  width: var(--nav-width)
  box-sizing: content-box
  overflow: hidden
  position: relative
  display: flex
  flex-direction: column
  gap: 12px
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
  .main-nav
    top: 0
    span
      top: 0
</style>
