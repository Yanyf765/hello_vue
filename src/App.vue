<template>
  <div id="app">
<!--    <img src="./assets/logo.png">-->
<!--    <router-view/>-->
<!--    <el-button type="success">1</el-button>-->
<!--    <h1>site : {{site}}</h1>-->
    <tree></tree>
  </div>
</template>

<script>
import tree from './component/tree/tree.vue'
export default {
  name: 'App',
  data: function () {
    return {
      site: '菜鸟教程'
    }
  },
  components: {tree}
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
