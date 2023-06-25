<script lang="ts">
export default {
  name: 'DevLog',
}
</script>

<script lang="ts" setup>
const props = defineProps<{ data: any }>()

// eslint-disable-next-line no-console
console.log(props.data)
</script>
