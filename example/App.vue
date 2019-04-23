<template>
  <div id="app">
    <div class="cell">
      <div class="label">车牌号码</div>
      <div class="field">
        <keyboard-license-plate v-model="platenumber"></keyboard-license-plate>
      </div>
    </div>
  </div>
</template>

<script>
import KeyboardLicensePlate from '../keyboard-license-plate/index.js'

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

<style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
.cell {
  display: flex;
  padding: 10px 15px;
  .label {
    min-width: 90px;
  }
  .field {
    flex: 1;
  }
}
</style>
