<template>
  <p v-if="str">{{ str }}</p>
</template>

<script lang="ts">
export default {
  name: 'DocDate',
}
</script>

<script lang="ts" setup>
const props = defineProps<{ date?: number }>()

const str = props.date ? new Date(props.date).toLocaleDateString() : ''
</script>
