<template lang="html">
  <div class="page-bar">
    <ul>
      <li v-if="cur!=1"><a v-on:click="btnClick(1)">首页</a><a v-on:click="btnClick1(-1)"> 上一页 </a></li>
      <li class="no-click" v-else><a>首页</a><a> 上一页 </a></li>
      <li v-for="index in indexs"  v-bind:class="{ active: cur == index}" :key="index">
        <a v-on:click="btnClick(index)" >{{ index }}</a>
      </li>
      <li v-if="cur!=all"><a v-on:click="btnClick1(1)"> 下一页 </a><a v-on:click="btnClick(all)"> 尾页 </a></li>
      <li class="no-click" v-else><a>下一页</a><a> 尾页 </a></li>
      <li><a>共<i>{{all}}</i>页</a></li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    cur: {
      type: [String, Number],
      required: true
    },
    all: {
      type: [String, Number],
      required: true
    },
    callback: {
      default () {
        return function callback () {
          // todo
        }
      }
    }
  },
  computed: {
    indexs () {
      var left = 1
      var right = this.all
      var ar = []
      if (this.all >= 11) {
        if (this.cur > 5 && this.cur < this.all - 4) {
          left = this.cur - 5
          right = this.cur + 4
        } else {
          if (this.cur <= 5) {
            left = 1
            right = 10
          } else {
            right = this.all
            left = this.all - 9
          }
        }
      }
      while (left <= right) {
        ar.push(left)
        left++
      }
      return ar
    }
  },
  methods: {
    btnClick (page) {
      if (page !== this.cur) {
        this.callback(page)
      }
    },
    btnClick1 (page) {
      this.callback(this.cur + page)
    }
  }
}
</script>

<style lang="css">
  ul,li {
    margin: 0px;
    padding: 0px;
  }

  .page-bar li {
    list-style: none;
    display: inline-block;
  }

  .page-bar li:first-child>a {
    margin-left: 0px
  }

  .page-bar a {
    border: 1px solid #ddd;
    text-decoration: none;
    position: relative;
    float: left;
    padding: 6px 12px;
    margin-left: -1px;
    line-height: 1.42857143;
    color: #20a0ff;
    cursor: pointer
  }
  .no-click a{
    border: 1px solid #ddd;
    text-decoration: none;
    position: relative;
    float: left;
    padding: 6px 12px;
    margin-left: -1px;
    line-height: 1.42857143;
    color: #949292;
    cursor: pointer
  }

  .page-bar a:hover {
    background-color: #eee;
  }

  .page-bar .active a {
    color: #fff;
    cursor: default;
    background-color: #20a0ff;
    border-color: #20a0ff;
  }

  .page-bar i {
    font-style:normal;
    color: black;
    margin: 0px 4px;
    font-size: 14px;
  }
</style>
