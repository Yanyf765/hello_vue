<template>
  <div id = 'tree'>
    <el-tree :data="data" :props="defaultProps" @click="hello"></el-tree>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Tree',
  data () {
    return {
      data: [{
        label: '一级 1',
        children: [{
          label: '二级 1-1',
          children: [{
            label: '三级 1-1-1'
          }]
        }]
      }, {
        label: '一级 2',
        children: [{
          label: '二级 2-1',
          children: [{
            label: '三级 2-1-1'
          }]
        }, {
          label: '二级 2-2',
          children: [{
            label: '三级 2-2-1'
          }]
        }]
      }, {
        label: '一级 3',
        children: [{
          label: '二级 3-1',
          children: [{
            label: '三级 3-1-1'
          }]
        }, {
          label: '二级 3-2',
          children: [{
            label: '三级 3-2-1'
          }]
        }]
      }],
      defaultProps: {
        children: 'children',
        label: 'label'
      }
    }
  },
  methods: {
    handleNodeClick (data) {
      console.log(data)
    },
    hello () {
      console.log('点击')
      console.log(axios.get('http://localhost:9001/hello'))
    }
  }
}
</script>

<style scoped>
#tree {
  width: 400px;
  background: #42b983;
  font-family: Consolas;
}
</style>
