<template>
  <div id="app">
    <MainComponent msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
  import MainComponent from './components/MainComponent.vue'

  export default {
    name: 'App',
    components: {
      MainComponent
    }
  }
</script>

<style>
</style>
