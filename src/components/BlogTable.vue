<template>
  <div>
    <div style="display: flex;justify-content: flex-start">
      <el-input placeholder="通过标题搜索该分类下的博客..."
        prefix-icon="el-icon-search"
        v-model="keywords" style="width: 400px" size="mini">
      </el-input>
      <el-button type="primary" icon="el-icon-search" size="mini" style="margin-left: 3px" @click="searchClick">搜索
      </el-button>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      keywords: ''
    }
  },
  name: 'BlogTable',
  methods: {
    searchClick () {
    }
  }
}
</script>

<style scoped>

</style>
