<template>
  <div>
    <h1>Exporting...</h1>
  </div>
</template>

<script>
export default {
  mounted() {
    if (window.parent) {
      console.log('POST')
      window.parent.postMessage(localStorage.periods, '*')
    }
  }
}
</script>
