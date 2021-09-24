<template>
  <div class="page">
    <div class="page-main">
      <div id="districtManagement"></div>
    </div>

    <div class="page-bottom">
      <div>
        <el-select v-model="area" placeholder="请选择" size="mini">
          <el-option v-for="item in regionList" :label="item.label" :key="item.value" :value="item.value"></el-option>
        </el-select>
        ||
        <el-button size="mini" @click="editArea">编辑区域</el-button>
        <el-button size="mini" @click="addArea">新增区域</el-button>
        <el-button size="mini" @click="moveArea">移动区域</el-button>
      </div>
      <div>
        <el-button type="primary" size="mini">导出报告</el-button>
        &nbsp;
        <el-button type="danger" size="mini">审批</el-button>
      </div>
      <div>
        <el-button type="primary" size="mini" @click="restoreArea">恢复删除项</el-button>
      </div>
    </div>
  </div>
</template>

<script>

import * as echarts from 'echarts'  //全局引入echarts并使用
// Vue.prototype.$echarts = echarts;

import { districtMockRes } from '@/assets/mockResponse/districtManagement.js'

export default {
  name: 'districtManagement',
  data() {
    return {
      list: districtMockRes,
      myChart: null,
      area: '0',
      regionList: [
        {
          value: '0',
          status: null,
          label: 'DIA中国',
        },
        {
          value: '1',
          status: 0,
          label: '华东2',
        },
        {
          value: '2',
          status: 1,
          label: '华中',
        },
        {
          value: '3',
          status: 2,
          label: '华西',
        },
        {
          value: '4',
          status: 0,
          label: '华北',
        },
        {
          value: '5',
          status: 1,
          label: '华南',
        },
        {
          value: '6',
          status: 2,
          label: '华东',
        },
      ],
    }
  },
  watch: {},
  created() {
    // 地区列表的label添加status的状态
    this.regionList.map((item) => {
      let nstatus =
        item.status == 0 ? '（未提交）' : item.status == 1 ? '（待审批）' : item.status == 2 ? '（已通过）' : ''
      item.label += nstatus
      return item
    })
  },
  mounted() {
    var chartDom = document.getElementById('districtManagement')
    // this.myChart = this.$echarts.init(chartDom)
    this.myChart = echarts.init(chartDom)
    this.renderChart(this.list)
  },
  methods: {
    // 渲染框架图
    renderChart(data) {
      let option = {
        tooltip: {
          trigger: 'item',
          triggerOn: 'mousemove',
        },
        series: [
          {
            type: 'tree',
            id: 0,
            name: 'tree1',
            data: [data],
            left: '2%',
            right: '2%',
            top: '8%',
            bottom: '20%',
            // symbol: function(value, params, callback) {
            //   // let div
            //   // div = document.createElement('div')
            //   // div.className = 'tree-symbol'
            //   // return div
            //   if(params.data.children){
            //     return 'circle'
            //   } else {
            //     return 'rect'
            //   }
            // },
            edgeShape: 'polyline', //curve  polyline
            orient: 'vertical',
            expandAndCollapse: true,
            label: {
              position: 'inside', //inside  bottom
              rotate: 0,
              verticalAlign: 'middle',
              align: 'left',
              fontSize: 10,
              // formatter: function(value) {
              //   let div
              //   div = document.createElement('div')
              //   div.className = 'tree-symbol'
              //   return div
              // },
              formatter: ['{a|这用样式a}', '{b|这用样式b}这样式{x|用样式x}'].join('\n'),

                rich: {
                  a: {
                    color: 'red',
                    lineHeight: 10,
                    width: '100',
                    backgroundColor: 'yellow',
                    height: '20'
                  },
                  b: {
                    backgroundColor: {
                      image: 'xxx/xxx.jpg',
                    },
                    height: 40,
                  },
                  x: {
                    fontSize: 18,
                    fontFamily: 'Microsoft YaHei',
                    borderColor: '#449933',
                    borderRadius: 4,
                  },
                },
            },
            leaves: {
              label: {
                position: 'bottom',
                rotate: 0,
                verticalAlign: 'middle',
                align: 'left',
                // formatter: function(value) {
                //   let div
                //   div = document.createElement('div')
                //   div.className = 'tree-symbol'
                //   return div
                // },
                formatter: ['{a|这段文本采用样式a}', '{b|这段文本采用样式b}这段用默认样式{x|这段用样式x}'].join('\n'),

                rich: {
                  a: {
                    color: 'red',
                    lineHeight: 10,
                  },
                  b: {
                    backgroundColor: {
                      image: 'xxx/xxx.jpg',
                    },
                    height: 40,
                  },
                  x: {
                    fontSize: 18,
                    fontFamily: 'Microsoft YaHei',
                    borderColor: '#449933',
                    borderRadius: 4,
                  },
                },
              },
            },
            animationDurationUpdate: 750,
            roam: true,
          },
        ],
      }
      this.myChart.setOption(option, true)
    },
    // 编辑区域信息
    editArea() {
      console.log('show area edit')
    },
    // 新增区域信息
    addArea() {
      console.log('show area add')
    },
    // 新增区域信息
    moveArea() {
      console.log('show area move')
    },
    // 新增区域信息
    restoreArea() {
      console.log('show area restore')
    },
  },
}
</script>

<style lang="scss" scoped>
@import '~@/styles/variables.scss';
$bottomHeight: 40px;

.page {
  position: relative;
  .page-main {
    position: relative;
    bottom: $bottomHeight;
    #districtManagement {
      width: 100%;
      height: 600px;
      background: rgb(177, 176, 175);
    }
  }
  .page-bottom {
    position: fixed;
    bottom: 0;
    padding: 0 10px;
    height: $bottomHeight;
    width: 100vw;
    background: $abc-background;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
}

.tree-symbol {
  width: 100px;
  height: 50px;
  background: palegreen;
}
</style>
