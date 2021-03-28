<template>
  <div id="app">
    <upload-videos></upload-videos>
  </div>
</template>

<script>
import UploadVideos from './components/UploadVideos.vue'

export default {
  name: 'App',
  components: {
    UploadVideos
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
