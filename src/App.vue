<template>
  <div id="app">
    <comp/>
  </div>
</template>

<script>
import comp from './components/comp.vue'

export default {
  name: 'app',
  components: {
    comp
  }
}
</script>

<style scoped>
#app {
  margin-bottom: 70px;
}
</style>