<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <ComboBox :data="cacheData" :width="300" :height="40" />
    <div>Helo</div>
  </div>
</template>

<script>
import ComboBox from './components/ComboBox.vue'

export default {
  components: {
    ComboBox
  },
  data(){
    return{
      cacheData:[
                {text:'Nam', value:0},
                {text:'Nữ', value:1},
                {text:'Gay', value:2},
            ],
    }
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
