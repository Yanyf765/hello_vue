<template>
  <div id = 'tree'>
    <el-tree :data="data" :props="defaultProps" @click="handleNodeClick(data)"></el-tree>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Tree',
  data () {
    return {
      data: [],
      defaultProps: {
        children: 'children',
        label: 'label'
      }
    }
  },
  created () {
    this.getTree() // 本地JSON
  },
  methods: {
    handleNodeClick (data) {
      console.log(data)
    },
    // 本地json获取商品数据
    getTree () {
      // debugger
      axios.get('/api/tree').then((res) => {
        this.data = res.data.data
      })
    }
  }
}
</script>

<style scoped>
#tree {
  width: 200px;
  height: 30px;
  background: #42b983;
  font-family: Consolas;
}
</style>
