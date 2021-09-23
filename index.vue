<template>
  <div id="tree">
    <svg></svg>
  </div>
</template>

<script>
import * as d3 from 'd3' //引入d3

import { districtMockRes, districtMockRes3, districtMockRes4 } from '@/assets/mockResponse/districtManagement.js'

export default {
  name: 'customerManagement',
  data() {
    return {
      textA: 'A bad excuse is better than none.',
      list: districtMockRes,
      textB: districtMockRes3,
    }
  },
  components: {},
  computed: {},
  mounted() {
    // this.drawTree()
    this.drawTree2()
  },
  methods: {
    // tree1
    drawTree() {
      let width = 500

      let svg = d3.select('#tree').select('svg')
      svg.style('background', 'yellow')

      let dx = 10
      let dy = 114.5
      let margin = {
        top: 10,
        right: 120,
        bottom: 10,
        left: 40,
      }

      const root = d3.hierarchy(this.list)

      root.x0 = dy / 2
      root.y0 = 0
      root.descendants().forEach((d, i) => {
        d.id = i
        d._children = d.children
        if (d.depth && d.data.name.length !== 7) d.children = null
      })

      svg
        .attr('viewBox', [-margin.left, -margin.top, width, dx])
        .style('font', '10px sans-serif')
        .style('user-select', 'none')

      const gLink = svg
        .append('g')
        .attr('fill', 'none')
        .attr('stroke', '#555')
        .attr('stroke-opacity', 0.4)
        .attr('stroke-width', 1.5)

      const gNode = svg
        .append('g')
        .attr('cursor', 'pointer')
        .attr('pointer-events', 'all')

      const tree = d3.tree().nodeSize([dx, dy])
      const diagonal = d3
        .linkHorizontal()
        .x((d) => d.y)
        .y((d) => d.x)

      function update(source) {
        const duration = d3.event && d3.event.altKey ? 2500 : 250
        const nodes = root.descendants().reverse()
        const links = root.links()

        // Compute the new tree layout.
        tree(root)

        let left = root
        let right = root
        root.eachBefore((node) => {
          if (node.x < left.x) left = node
          if (node.x > right.x) right = node
        })

        const height = right.x - left.x + margin.top + margin.bottom

        const transition = svg
          .transition()
          .duration(duration)
          .attr('viewBox', [-margin.left, left.x - margin.top, width, height])
          .tween('resize', window.ResizeObserver ? null : () => () => svg.dispatch('toggle'))

        // Update the nodes…
        const node = gNode.selectAll('g').data(nodes, (d) => d.id)

        // Enter any new nodes at the parent's previous position.
        const nodeEnter = node
          .enter()
          .append('g')
          .attr('transform', (d) => `translate(${source.y0},${source.x0})`)
          .attr('fill-opacity', 0)
          .attr('stroke-opacity', 0)
          .on('click', (event, d) => {
            d.children = d.children ? null : d._children
            update(d)
          })

        nodeEnter
          .append('circle')
          .attr('r', 2.5)
          .attr('fill', (d) => (d._children ? '#555' : '#999'))
          .attr('stroke-width', 10)

        nodeEnter
          .append('text')
          .attr('dy', '0.31em')
          .attr('x', (d) => (d._children ? -6 : 6))
          .attr('text-anchor', (d) => (d._children ? 'end' : 'start'))
          .text((d) => d.data.name)
          .clone(true)
          .lower()
          .attr('stroke-linejoin', 'round')
          .attr('stroke-width', 3)
          .attr('stroke', 'white')

        // Transition nodes to their new position.
        const nodeUpdate = node
          .merge(nodeEnter)
          .transition(transition)
          .attr('transform', (d) => `translate(${d.y},${d.x})`)
          .attr('fill-opacity', 1)
          .attr('stroke-opacity', 1)

        // Transition exiting nodes to the parent's new position.
        const nodeExit = node
          .exit()
          .transition(transition)
          .remove()
          .attr('transform', (d) => `translate(${source.y},${source.x})`)
          .attr('fill-opacity', 0)
          .attr('stroke-opacity', 0)

        // Update the links…
        const link = gLink.selectAll('path').data(links, (d) => d.target.id)

        // Enter any new links at the parent's previous position.
        const linkEnter = link
          .enter()
          .append('path')
          .attr('d', (d) => {
            const o = { x: source.x0, y: source.y0 }
            return diagonal({ source: o, target: o })
          })

        // Transition links to their new position.
        link
          .merge(linkEnter)
          .transition(transition)
          .attr('d', diagonal)

        // Transition exiting nodes to the parent's new position.
        link
          .exit()
          .transition(transition)
          .remove()
          .attr('d', (d) => {
            const o = { x: source.x, y: source.y }
            return diagonal({ source: o, target: o })
          })

        // Stash the old positions for transition.
        root.eachBefore((d) => {
          d.x0 = d.x
          d.y0 = d.y
        })
      }

      update(root)

      return svg.node()
    },

    // tree2
    drawTree2() {
      let width = 954
      let margin = { top: 48, right: 0, bottom: 48, left: 0 }
      let dx = 12
      let dy = 120

      const tree = (data) => {
        const root = d3.hierarchy(data)
        root.dx = 10
        root.dy = width / (root.height + 1)
        return d3.tree().nodeSize([root.dx, root.dy])(root)
      }

      // const tree = d3.tree().nodeSize([dx, dy])

      const treeLink = d3
        .linkHorizontal()
        .x((d) => d.y)
        .y((d) => d.x)

      function data(data) {
        function populate(tree, words) {
          if (words.length === 0) {
            // condition for ending recursion
            return
          }

          let word = words[0] // words = ["A", "bad", "excuse", "is", "better", "than", "none."]
          let node = tree['children'].find((t) => t.text === word)

          if (!node) {
            // if we haven't seen this word before
            node = {
              text: word,
              children: [],
            }
            tree['children'].push(node)
          }

          populate(node, words.slice(1)) // keep populating without the first, already-processed word
        }

        let root = {
          text: '💬',
          children: [],
        }

        let i = 0
        const len = data.length
        for (i; i < len; i++) {
          const phrase = data[i] // "A bad excuse is better than none."
          const words = phrase.split(' ') // ["A", "bad", "excuse", "is", "better", "than", "none."]
          populate(root, words)
        }

        return root
      }

      let aaa = data(this.textB)

      let parsed = d3.hierarchy(this.textA, (id) => {
        for (const split of [/[^\w\s]/, /\s/]) {
          const children = id && id.split(split).filter((id) => id.length > 0)
          if (children.length > 1) return children
        }
      })

      function graph(root, { label = (d) => d.data.id, highlight = () => false, marginLeft = 40 } = {}) {
        root = tree(root)

        let x0 = Infinity
        let x1 = -x0
        root.each((d) => {
          if (d.x > x1) x1 = d.x
          if (d.x < x0) x0 = d.x
        })

        let svg = d3.select('#tree').select('svg')
        svg
          .style('background', 'yellow')
          .attr('viewBox', [0, 0, width, x1 - x0 + dx * 2])
          .style('overflow', 'visible')

        const g = svg
          .append('g')
          .attr('font-family', 'sans-serif')
          .attr('font-size', 10)
          .attr('transform', `translate(${marginLeft},${dx - x0})`)

        const link = g
          .append('g')
          .attr('fill', 'none')
          .attr('stroke', '#555')
          .attr('stroke-opacity', 0.4)
          .attr('stroke-width', 1.5)
          .selectAll('path')
          .data(root.links())
          .join('path')
          .attr('stroke', (d) => (highlight(d.source) && highlight(d.target) ? 'red' : null))
          .attr('stroke-opacity', (d) => (highlight(d.source) && highlight(d.target) ? 1 : null))
          .attr('d', treeLink)

        const node = g
          .append('g')
          .attr('stroke-linejoin', 'round')
          .attr('stroke-width', 3)
          .selectAll('g')
          .data(root.descendants())
          .join('g')
          .attr('transform', (d) => `translate(${d.y},${d.x})`)

        // node
        //   .append('circle')
        //   .attr('fill', (d) => (highlight(d) ? 'red' : d.children ? '#555' : '#999'))
        //   .attr('r', 2.5)

        node.append('rect').attr('fill', (d) => (highlight(d) ? 'red' : d.children ? '#555' : '#999'))
        // .attr('r', 2.5)

        node
          .append('text')
          .attr('fill', (d) => (highlight(d) ? 'red' : null))
          .attr('dy', '0.31em')
          .attr('x', (d) => (d.children ? -6 : 6))
          .attr('text-anchor', (d) => (d.children ? 'end' : 'start'))
          .text(label)
          .clone(true)
          .lower()
          .attr('stroke', 'white')

        return svg.node()
      }

      // graph(parsed, { marginLeft: 200, label: (d) => d.data })
      // graph(this.list, {
      //   marginLeft: 200,
      //   label: (d, i) => {
      //     const { data } = d
      //     return data.name
      //   },
      // })
      graph(aaa, {
        marginLeft: 200,
        label: (d, i) => {
          const { data } = d
          return data.text
        },
      })
    },
  },
}
</script>

<style lang="scss" scoped></style>
