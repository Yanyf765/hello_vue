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
    <el-table
      ref="multipleTable"
      :data="articles"
      tooltip-effect="dark"
      style="width: 100%;overflow-x: hidden;overflow-y: hidden;"
      @selection-change="handleSelectionChange" v-loading="loading">
      <el-table-column
        type="selection"
        width="35" align="center" v-if="showEdit || showDelete">
      </el-table-column>
      <el-table-column
        label="标题"
        width="400"
        align="center">
        <template slot-scope="scope"><span style="color: #409eff;cursor: pointer" @click="itemClick(scope.row)">{{ scope.row.title}}</span></template>
      </el-table-column>
      <el-table-column
        label="最近编辑时间"
        width="140"
        align="center">
        <template slot-scope="scope">{{scope.row.editTime | formatDateTime}}</template>
      </el-table-column>
      <el-table-column
        prop="nickname"
        label="作者"
        width="120"
        align="center">
      </el-table-column>
      <el-table-column
        prop="cateName"
        label="所属分类"
        width="120"
        align="center">
      </el-table-column>
      <el-table-column
        label="操作"
        align="center"
        v-if="showEdit || showDelete">
        <template slot-scope="scope">
          <el-button
            size="mini"
            @click="handleEdit(scope.$index, scope.row)"
            v-if="showEdit">编辑
          </el-button>
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(scope.$index, scope.row)"
            v-if="handleDelete">删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="blog_table_footer">
      <el-button
        type="danger"
        size="mini"
        style="margin: 0px;"
        v-show="this.articles.length>0 && showDelete"
        :disabled="this.selItems.length == 0"
        @click="deleteMany">批量删除
      </el-button>
    </div>
    <MyPagination :cur="currentPage" :all="totalCount" :callback="getTableData"></MyPagination>
  </div>
</template>

<script>
// eslint-disable-next-line import/no-duplicates
import {getRequest, putRequest} from '../utils/api'
// eslint-disable-next-line import/no-duplicates
import {postRequest} from '../utils/api'
// eslint-disable-next-line import/no-duplicates
import {isNotNullORBlank} from '../utils/utils'
// eslint-disable-next-line import/no-duplicates
import {Bus} from '../utils/utils'
import MyPagination from '@/components/MyPagination'
export default {
  data () {
    return {
      articles: [],
      keywords: '',
      loading: false,
      totalCount: 10,
      pageSize: 10,
      selItems: false,
      dustbinData: [],
      currentPage: 1,
      limit: 10
    }
  },
  components: {
    MyPagination: MyPagination
  },
  name: 'BlogTable',
  mounted: function () {
    var _this = this
    this.loading = true
    this.loadBlogs(1, this.pageSize)
    Bus.$on('blogTableReload', function () {
      _this.loading = true
      _this.loadBlogs(_this.currentPage, _this.pageSize)
    })
  },
  methods: {
    searchClick () {
      this.loadBlogs(1, this.pageSize)
    },
    itemClick (row) {
      this.$router.push({path: '/blogDetail', query: {aid: row.id}})
    },
    handleSelectionChange (val) {
      this.selItems = val
    },
    loadBlogs (page, count) {
      var _this = this
      var url = ''
      if (this.state === -2) {
        url = '/admin/article/page' + '?page=' + page + '&keywords=' + this.keywords
      } else {
        url = '/article/page?state=' + this.state + '&page=' + page + '&keywords=' + this.keywords
      }
      getRequest(url).then(resp => {
        _this.loading = false
        if (resp.status === 200) {
          _this.articles = resp.data.data.list
          _this.totalCount = Math.ceil(resp.data.data.totalCount / 10)
        } else {
          _this.$message({type: 'error', message: '数据加载失败!'})
        }
      }, resp => {
        _this.loading = false
        if (resp.response.status === 403) {
          _this.$message({type: 'error', message: resp.response.data})
        } else {
          _this.$message({type: 'error', message: '数据加载失败!'})
        }
      }).catch(resp => {
        // 压根没见到服务器
        _this.loading = false
        _this.$message({type: 'error', message: '数据加载失败!'})
      })
    },
    saveBlog (state) {
      if (!(isNotNullORBlank(this.article.title, this.article.mdContent, this.article.cid))) {
        this.$message({type: 'error', message: '数据不能为空!'})
        return
      }
      var _this = this
      _this.loading = true
      postRequest('/article/', {
        id: _this.article.id,
        title: _this.article.title,
        mdContent: _this.article.mdContent,
        htmlContent: _this.$refs.md.d_render,
        cid: _this.article.cid,
        state: state,
        dynamicTags: _this.article.dynamicTags
      }).then(resp => {
        _this.loading = false
        if (resp.status === 200 && resp.data.status === 'success') {
          _this.article.id = resp.data.msg
          _this.$message({type: 'success', message: state === 0 ? '保存成功!' : '发布成功!'})
          Bus.$emit('blogTableReload')
          if (state === 1) {
            _this.$router.replace({path: '/articleList'})
          }
        }
      }, resp => {
        _this.loading = false
        _this.$message({type: 'error', message: state === 0 ? '保存草稿失败!' : '博客发布失败!'})
      })
    },
    handleEdit (index, row) {
      this.$router.push({path: '/editBlog', query: {from: this.activeName, id: row.id}})
    },
    handleDelete (index, row) {
      this.dustbinData.push(row.id)
      this.deleteToDustBin(row.state)
    },
    deleteToDustBin (state) {
      var _this = this
      this.$confirm(state !== -2 ? '将该文件放入回收站,是否继续' : '永久删除文件,是否继续', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        _this.loading = true
        var url = ''
        if (_this.state === -2) {
          url = '/admin/article/dustbin'
        } else {
          url = '/article/dustbin'
        }
        putRequest(url, {aids: _this.dustbinData, state: state}).then(resp => {
          if (resp.status === 200) {
            var data = resp.data
            _this.$message({type: data.status, message: data.msg})
            if (data.status === 'success') {
              Bus.$emit('blogTableReload')
            }
          } else {
            _this.$message({type: 'error', message: '删除失败!'})
          }
          _this.loading = false
          _this.dustbinData = []
        }, resp => {
          _this.loading = false
          _this.$message({type: 'error', message: '删除失败!'})
          _this.dustbinData = []
        }).catch(() => {
          _this.$message({
            type: 'info',
            message: '已取消删除'
          })
          _this.dustbinData = []
        })
      })
    },
    deleteMany () {},
    getTableData (data) {
      this.currentPage = data
      this.loadBlogs(data)
    }
  },
  props: ['state', 'showEdit', 'showDelete', 'activeName']
}
</script>

<style>
  .blog_table_footer{
    display: flex;
    box-sizing: content-box;
    padding-top: 10px;
    padding-bottom: 0px;
    margin-bottom: 0px;
    justify-content: space-between;
  }
</style>
