<template>
  <div>
    <button
     v-if="!action.getWarnings().length"
     @click.stop.prevent="onSubmit"
     >Next</button>
    <button
     @click.stop.prevent="onCancel"
     >Exit</button>
  </div>
</template>

<script lang="ts">
export default {
  props: ['action','onSubmit','onCancel']
}
</script>
