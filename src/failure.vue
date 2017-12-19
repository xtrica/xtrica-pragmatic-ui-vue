<template>
  <div>
    <h2>A connection error occurred.</h2>
    <button
     @click.stop.prevent="onRetry"
     ><span>Retry</span></button>
    <button
     @click.stop.prevent="onAbort"
     >Exit</button>
  </div>
</template>

<script>
export default {
  props: ['action','onRetry','onAbort']
}
</script>
