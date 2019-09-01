<template>
  <el-card style="width: 500px" v-loading="loading">
    <div>
      <div style="text-align: left">
        <el-form :model="emailValidateForm" label-position="top" ref="emailValidateForm">

        </el-form>
      </div>
    </div>
  </el-card>
</template>

<script>
// eslint-disable-next-line import/no-duplicates
// import {getRequest} from '../utils/api'
// eslint-disable-next-line import/no-duplicates
// import {putRequest} from '../utils/api'
export default {
  name: 'BlogCfg'
}
</script>

<style scoped>

</style>
