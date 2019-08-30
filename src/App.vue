<template>
<!--  <div id="app">-->
<!--    <img src="./assets/logo.png">-->
<!--    <router-view/>-->
<!--    <el-button type="success">1</el-button>-->
<!--    <h1>site : {{site}}</h1>-->
<!--    <tree></tree>-->
<!--  </div>-->
  <div id="app" style="background-color: rgba(235, 235, 235, 0.08)">
    <router-view/>
  </div>
</template>

<script>
// import tree from './components/tree/tree.vue'
// export default {
//   name: 'App',
//   data: function () {
//     return {
//       site: '菜鸟教程'
//     }
//   },
//   components: {tree}
// }
export default {
  name: 'app'
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
