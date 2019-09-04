<template>
  <el-container>
    <el-header class="cate_mana_header">
      <el-input
        placeholder="请输入栏目名称"
        v-model="cateName" style="width: 200px;">
      </el-input>
      <el-button type="primary" size="medium" style="margin-left: 10px" @click="addNewCate">新增栏目</el-button>
    </el-header>
    <el-main class="cate_mana_main">
      <el-table
        ref="multipleTable"
        :data="categories"
        tooltip-effect="dark"
        style="width: 100%"
        @selection-change="handleSelectionChange" v-loading="loading">
        <el-table-column
          type="selection"
          width="55" align="center">
        </el-table-column>
        <el-table-column
          label="编号"
          prop="id"
          width="120" align="center">
        </el-table-column>
        <el-table-column
          label="栏目名称"
          prop="cateName"
          width="120" align="center">
        </el-table-column>
        <el-table-column
          prop="date"
          label="启用时间" align="center">
          <template slot-scope="scope">{{ scope.row.date | formatDate}}</template>
        </el-table-column>
        <el-table-column label="操作" align="center">
          <template slot-scope="scope">
            <el-button
              size="mini"
              @click="handleEdit(scope.$index, scope.row)">编辑
            </el-button>
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)">删除
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-button type="danger" :disabled="this.selItems.length==0" style="margin-top: 10px;width: 100px;"
                 @click="deleteAll" v-if="this.categories.length>0">批量删除
      </el-button>
      <MyPagination :cur="currentPage" :all="totalCount" :callback="getTableData"></MyPagination>
    </el-main>
  </el-container>
</template>

<script>
import {postRequest, deleteRequest, getRequest} from '../utils/api'
import MyPagination from '@/components/MyPagination'
export default {
  name: 'CateMana',
  components: {
    MyPagination: MyPagination
  },
  methods: {
    addNewCate () {
      this.loading = true
      var _this = this
      postRequest('/admin/category/add', {cateName: this.cateName}).then(resp => {
        if (resp.status === 200) {
          var json = resp.data
          _this.$message({type: json.status, message: json.msg})
          _this.cateName = ''
          _this.refresh(1)
        }
        _this.loading = false
      }, resp => {
        if (resp.response.status === 403) {
          _this.$message({
            type: 'error',
            message: resp.response.data
          })
        }
        _this.loading = false
      })
    },
    refresh (page) {
      let _this = this
      getRequest('/admin/category/page?page=' + page).then(resp => {
        _this.categories = resp.data.data.list
        _this.loading = false
        _this.totalCount = Math.ceil(resp.data.data.totalCount / 10)
      }, resp => {
        if (resp.response.status === 403) {
          _this.$message({
            type: 'error',
            message: resp.response.data
          })
        }
        _this.loading = false
      })
    },
    handleSelectionChange (val) {
      this.selItems = val
    },
    handleEdit (index, row) {},
    handleDelete (index, row) {
      let _this = this
      this.$confirm('确认删除 ' + row.cateName + ' ?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        _this.deleteCate(row.id)
      }).catch(() => {
        // 取消
        _this.loading = false
      })
    },
    deleteAll () {
    },
    deleteCate (ids) {
      var _this = this
      this.loading = true
      // 删除
      deleteRequest('/admin/category/' + ids).then(resp => {
        var json = resp.data
        _this.$message({
          type: json.status,
          message: json.msg
        })
        _this.refresh(1)
      }, resp => {
        _this.loading = false
        if (resp.response.status === 403) {
          _this.$message({
            type: 'error',
            message: resp.response.data
          })
        } else if (resp.response.status === 500) {
          _this.$message({
            type: 'error',
            message: '该栏目下尚有文章，删除失败!'
          })
        }
      })
    },
    getTableData (data) {
      this.currentPage = data
      this.refresh(data)
    }
  },
  data () {
    return {
      cateName: '',
      selItems: [],
      categories: [],
      loading: false,
      totalCount: 55,
      currentPage: 1,
      limit: 10
    }
  },
  mounted: function () {
    this.loading = true
    this.refresh(1)
  }
}
</script>

<style>
  .cate_mana_header {
    background-color: white;
    margin-top: 20px;
    padding-left: 5px;
    display: flex;
    justify-content: flex-start;
  }

  .cate_mana_main {
    /*justify-content: flex-start;*/
    display: flex;
    flex-direction: column;
    padding-left: 5px;
    background-color: white;
    margin-top: 20px;
    padding-top: 10px;
  }
</style>
