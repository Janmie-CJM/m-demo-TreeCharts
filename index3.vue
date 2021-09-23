<template>
  <div id="salesTargetManagement">
    <!-- <svg width="600" height="500"></svg> -->

    <!-- <p>1111</p> -->

    <!-- <svg>
      <rect></rect>
      <rect></rect>
    </svg> -->

    <!-- <h1 id="target">Hello World</h1>

    <p></p>
    <p></p>
    <p></p>

    <el-button @click="removeText">移除h3</el-button> -->

    <h1>Bar Chart using D3.js</h1>
    <svg class="bar-chart"></svg>
  </div>
</template>

<script>
import * as d3 from 'd3' //引入d3

export default {
  name: 'salesTargetManagement',
  data() {
    return {
      data: [73, 52, 33, 22, 14, 68],
      // classA:{
      //   'width':'100vw',
      //   'height': '100vh',
      //   'background': 'yellow'
      // }
    }
  },
  mounted() {
    // d3.select('#salesTargetManagement').select('p').style('width','100vw').style('height','100vh').style('background','red')
    // d3.select('#salesTargetManagement').select('p').attr('class',this.classA)
    // this.draw()
    // this.changeText()
    // this.setDatum()
    // this.setData()
    // this.appendText()
    // this.insertText()
    // this.drawChart()
    this.drawBar()
  },
  methods: {
    // 柱形图
    drawBar() {
      // 数据集
      let dataset = [80, 100, 56, 120, 180, 30, 40, 120, 160]
      // 定义svg图形宽高，以及柱状图间距
      let svgWidth = 500,
        svgHeight = 300,
        barPadding = 5
      // 通过图形计算每个柱状宽度
      let barWidth = svgWidth / dataset.length

      // 绘制图形
      let svg = d3
        .select('#salesTargetManagement')
        .select('svg')
        .attr('width', svgWidth)
        .attr('height', svgHeight)
        .style('background', 'red')

      let yScale = d3
        .scaleLinear()
        .domain([0, d3.max(dataset)])
        .range([0, svgHeight])

      let barChart = svg
        .selectAll('rect')
        .data(dataset) //绑定数组
        .enter() // 指定选择集的enter部分
        .append('rect') // 添加足够数量的矩形
        // .attr('y', (d) => svgHeight - d) // d为数据集每一项的值, 取y坐标
        // .attr('height', (d) => d) // 设定高度
        .attr('y', (d) => svgHeight - yScale(d)) // d为数据集每一项的值, 取y坐标
        .attr('height', (d) => yScale(d)) // 设定高度
        .attr('width', barWidth - barPadding) // 设定宽度
        // .attr('transform', (d, i) => {
        //   let translate = [barWidth * i, 0]
        //   return 'translate(' + translate + ')'
        // }) // 实际是计算每一项值的x坐标
        .attr('x', (d, i) => barWidth * i)

      // 在图形上方显示数值
      let text = svg
        .selectAll('text')
        .data(dataset)
        .enter()
        .append('text')
        .text((d) => d)
        .attr('y', (d, i) => svgHeight - d - 2)
        .attr('x', (d, i) => barWidth * i)
        .attr('fill', '#A64C38')

      // // Axes：轴
      // // 首先是拿最大值构建x轴坐标
      // let xAxesScale = d3
      //   .scaleLinear()
      //   .domain([0, d3.max(dataset)])
      //   .range([0, svgWidth])

      // // 接下来是反转值，用作y轴坐标。
      // let yAxesScale = d3
      //   .scaleLinear()
      //   .domain([0, d3.max(dataset)])
      //   .range([svgHeight, 0])

      // // 横轴的API使用
      // let x_axis = d3.axisBottom().scale(xAxesScale)

      // // 纵轴的API使用
      // let y_axis = d3.axisLeft().scale(yAxesScale)

      // // 在svg中提供了如g元素这样的将多个元素组织在一起的元素。
      // // 由g元素编组在一起的可以设置相同的颜色，可以进行坐标变换等，类似于Vue中的 <template>

      // svg
      //   .append('g')
      //   .attr('transform', 'translate(50, 10)')
      //   .call(y_axis)

      // let xAxisTranslate = svgHeight - 20

      // svg
      //   .append('g')
      //   .attr('transform', 'translate(50, ' + xAxisTranslate + ')')
      //   .call(x_axis)
    },
    // 简易柱形图
    drawChart() {
      var dataset = [250, 210, 170, 130, 90] //数据（表示矩形的宽度）

      var width = 500 //画布的宽度
      var height = 300 //画布的高度

      var svg = d3
        .select('#salesTargetManagement') //选择文档中的body元素
        .append('svg') //添加一个svg元素
        // .attr('width', width) //设定宽度
        // .attr('height', height) //设定高度
        .style('width', width) //设定宽度
        .style('height', height) //设定高度
        .style('background', 'yellow')

      var rectHeight = 30 //每个矩形所占的像素高度(包括空白)

      // svg
      //   .selectAll('rect')
      //   .data(dataset)
      //   .enter()
      //   .append('rect')
      //   .attr('x', 20)
      //   .attr('y', function(d, i) {
      //     return i * rectHeight
      //   })
      //   .attr('width', function(d) {
      //     return d
      //   })
      //   .attr('height', rectHeight - 2)
      //   .attr('fill', 'steelblue')

      svg
        .selectAll('rect')
        .data(dataset)
        .enter()
        .append('rect')
        .attr('y', function(d, i) {
          return height - d
        })
        .attr('x', function(d, i) {
          return i * rectHeight
        })
        .attr('height', function(d) {
          return d
        })
        .attr('width', rectHeight - 2)
        .attr('fill', 'steelblue')
    },
    // copy的案例
    draw() {
      let margin = 30 // 上下左右边距

      let svg = d3.select('svg')
      let width = svg.attr('width')
      let height = svg.attr('height')

      // 创建矩形分组
      let g = svg.append('g').attr('transform', 'translate(' + margin + ',' + margin + ')') // 图表距离视口的左、上距离

      // 定义 X 轴比例尺
      let scaleX = d3
        .scaleBand()
        .domain(d3.range(this.data.length))
        .rangeRound([0, width - margin * 2])

      // 定义 y 轴比例尺
      let scaleY = d3
        .scaleLinear()
        .domain([0, d3.max(this.data)])
        .range([height - margin * 2, 0])
      // 上边距30；注意：range 后面跟的参数0，放在第二位 因为 y轴正方向向下

      // 绘制 x y 轴
      let axisX = d3.axisBottom(scaleX)
      let axisY = d3.axisLeft(scaleY)
      g.append('g')
        .attr('transform', 'translate(0, ' + (height - margin * 2) + ')')
        .call(axisX)
      g.append('g')
        .attr('transform', 'translate(0,0)')
        .call(axisY)

      // 创建矩形分组
      let gs = g
        .selectAll('rect')
        .data(this.data)
        .enter()
        .append('g')

      // 绘制矩形 + 过渡效果
      let rectP = 40 // 柱状图间距
      gs.append('rect')
        .attr('x', function(d, i) {
          return scaleX(i) + rectP / 2
        })
        .attr('y', function(d, i) {
          var min = scaleY.domain()[0] // [0, 73]
          return scaleY(min)
          // scaleY(0) y轴比例尺映射出来的是最大值；这个效果等同于 return height - 2*margin 的效果
        })
        .attr('width', function(d, i) {
          return scaleX.step() - rectP
        })
        .attr('height', function(d, i) {
          return 0 // 动画初始状态为0
        })
        .attr('fill', 'pink')
        .transition()
        .duration(1500)
        .delay(function(d, i) {
          return i * 200 // 每个柱子逐渐开始的效果
        })
        .attr('y', function(d, i) {
          return scaleY(d)
        })
        .attr('height', function(d, i) {
          return height - margin * 2 - scaleY(d)
        })

      // 添加鼠标划入划出事件
      gs.on('mouseover', function() {
        d3.select(this.firstChild) // 这里的this是包含：rect text 的节点
          .transition()
          .duration(1000)
          .delay(200)
          .attr('fill', '#306ade')
      })

      gs.on('mouseout', function() {
        d3.select(this.firstChild)
          .transition()
          .duration(1000)
          .delay(200)
          .attr('fill', 'pink')
      })

      // 绘文字 + 过渡效果
      gs.append('text')
        .attr('x', function(d, i) {
          return scaleX(i) + rectP
        })
        .attr('y', function(d, i) {
          return height - 2 * margin
        })
        .attr('dx', function(d, i) {
          return -2
        })
        .attr('dy', function(d, i) {
          return 20
        })
        .text(function(d, i) {
          return d
        })
        .attr('fill', 'green')
        .transition()
        .duration(1500)
        .delay(function(d, i) {
          return i * 200
        })
        .attr('y', function(d, i) {
          return scaleY(d)
        })
    },
    // 更改hello world
    changeText() {
      let paragraphs = document.getElementById('target')
      paragraphs.innerHTML = 'SWUSTVIS'
      d3.select('body')
        .select('#target')
        .text('hhhhhhh')
        .style('color', 'red')
        .style('font-size', '30px')
    },
    // 绑定数据 datum
    setDatum() {
      var str = 'happy'
      var a = d3.select('#salesTargetManagement')
      var p = a.selectAll('p')
      p.datum(str)
      p.text(function(d, i) {
        return '第 ' + i + ' 个元素绑定的数据是 ' + d
      })
    },
    // 绑定数据 data
    setData() {
      var dataset = ['sun', 'moon', 'you']
      var a = d3.select('#salesTargetManagement')
      var p = a.selectAll('p')
      p.data(dataset).text(function(d, i) {
        return 'i love ' + d
      })
    },
    // append
    appendText() {
      d3.select('#salesTargetManagement')
        .select('#target')
        .append('h2')
        .text('append 了 一个 h2')
        .style('color', 'blue')
    },
    // insert
    insertText() {
      d3.select('#salesTargetManagement')
        .insert('h3', '#target')
        .text('在 #target 前 insert 了 一个 h3')
        .style('color', 'purple')
    },
    // remove
    removeText() {
      d3.select('h3').remove()
    },
  },
}
</script>

<style lang="scss" scoped></style>{ }
