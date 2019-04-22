<template>
  <div id="app">
    <keyboard-license-plate v-model="platenumber"></keyboard-license-plate>
    <!-- <img src="./assets/WechatIMG2.jpeg" alt=""> -->
  </div>
</template>

<script>
import KeyboardLicensePlate from '../component/index.js'

export default {
  name: 'app',
  components: {
    KeyboardLicensePlate
  },
  data () {
    return {
      platenumber: ''
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
