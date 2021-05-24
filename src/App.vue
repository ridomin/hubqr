<template>
  <div id="app">
    <qrcode-vue :value="value"></qrcode-vue>
    <img alt="Vue logo" src="https://vuejs.org/images/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import QrcodeVue from 'qrcode.vue'

export default {
  name: 'App',
  data() {
    return {
      value : 'asdfasdfasdfasdfsdfa'
    }
  },
  components: {
    HelloWorld,
    QrcodeVue
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
