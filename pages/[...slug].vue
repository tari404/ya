<template>
  <ContentDoc>
    <template #default="{ doc }">
      <DocDate :date="doc.createAt" />
      <ContentRenderer v-if="doc.body" :value="doc" />
      <div v-else class="not-found">- 404 -</div>
      <!-- <a
              :href="'https://github.com/tari404/ya/commits/master/content/' + doc._file"
              target="_blank"
            >
              历史编辑记录
            </a> -->
    </template>
    <template #not-found>
      <div class="not-found">- 404 -</div>
    </template>
  </ContentDoc>
</template>

<script lang="ts" setup></script>

<style lang="sass">
.not-found
  display: block
  text-align: center
  font-family: 'Grandiflora One', serif
  font-size: 40px
  color: hsl(var(--color-ink-secondary))
</style>
