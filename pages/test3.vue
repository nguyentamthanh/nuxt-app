/* eslint-disable camelcase */
<template>
  <div class="w-full h-full border py-10 shadow-md flex-row relative">
    <canvas
      :id="'canvas'"
      class="
        w-full
        h-full
        absolute
        top-0
        z-40
        border border-0
        rounded
        border-red-200
        pointer-events-none
        bg-blue-300
      "
    ></canvas>
    <div v-for="item in test" :key="item.id">
      //tiêu đề câu hỏi
      <!-- <div class="text-center border-2 border-yellow-500 mx-8 relative">
        Câu {{ index + 1 }} :
        <! {{ item.question.content }} -->
      <!-- </div> -->

      <div class="w-full h-full flex">
        <div class="w-1/2">
          <div
            v-for="(i, index) in item.list1"
            :key="i.id"
            class="m-2 border relative text-center min-w-100px h-7"
          >
            <div
              :id="'pointer_' + i.id"
              class="!z-50 bg-red-200 absolute w-7 h-7 right-1/2 top-0"
              @click="MouseClick(i, index)"
            >
              {{ i.content }}
            </div>
          </div>
        </div>
        <br />
        <div class="w-1/2">
          <div
            v-for="(i, index) in item.list2"
            :key="i.id"
            class="m-2 border relative text-center min-w-100px h-7"
          >
            <div
              :id="'pointer_' + i.id"
              class="!z-50 bg-red-200 absolute w-7 h-7 right-1/2 top-0"
              @click="MouseClick(i, index)"
            >
              {{ i.content }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'IndexPage',
  data() {
    return {
      list1: [],
      list2: [],
      test: {},
      start_pointer: '',
      end_pointer: '',
      conected_list: [],
      colors: [
        '#000000',
        '#FF0000',
        '#FF95C5',
        '#42d7f5',
        '#48f56c',
        '#c74fff',
        '#5bd548',
        '#ab4d50',
        '#756eb0',
        '#f6f462',
      ],
      dialogSuggest: false,
      suggest: '',
    }
  },
  created() {
    this.getdata()
  },
  destroyed() {
    if (process.client) {
      window.removeEventListener('resize', this.handleResize)
    }
  },

  methods: {
    getdata(id) {
      axios
        .get('https://6214967f89fad53b1f17fd73.mockapi.io/api/noitu/q')
        .then((res) => {
          this.test = res.data.map((i) => {
            i.list1 = i.answer.filter((x) => x.group === 1)
            i.list2 = i.answer.filter((x) => x.group === 2)
            return i
          })
        })
    },
    openDialogSuggest() {
      if (this.q_data.question.suggest.content !== '') {
        this.suggest = this.q_data.question.suggest.content
      }
      this.dialogSuggest = true
    },
    saveDialogSuggest() {
      this.q_data.question.suggest.content = ''
      this.q_data.question.suggest.content = this.suggest
      this.dialogSuggest = false
    },
    initDraw(drawData) {
      let select_list = drawData
      if (select_list) {
        for (const property in select_list) {
          s
          let p1 = document.getElementById(property)
          let p2 = document.getElementById(select_list[property])
          let item1 = this.list0.filter((x) => x.id == property)
          let item2 = this.list1.filter((x) => x.id == select_list[property])
          item1 = item1.length > 0 ? item1[0] : null
          item2 = item2.length > 0 ? item2[0] : null
          if (item1 && item2) {
            this.MouseClick(item1) // todo check lai
            this.MouseClick(item2) // todo nghia check
          }
        }
      }
    },
    MouseClick(item, index) {
      const target = document.getElementById('pointer_' + item.id)
      const box = target.getBoundingClientRect()
      if (item.group === 1) {
        this.start_pointer = {
          item,
          top: box.top,
          right: box.right,
          bottom: box.bottom,
          left: box.left,
          width: box.right - box.left,
          height: box.bottom - box.top,
          index,
        }
      } else if (this.start_pointer && item.group === 2) {
        this.end_pointer = {
          item,
          top: box.top,
          right: box.right,
          bottom: box.bottom,
          left: box.left,
          width: box.right - box.left,
          height: box.bottom - box.top,
          index,
        }
        //remove other
        this.conected_list = this.conected_list.filter(
          (x) => x.p2.item.id != item.id
        )
        this.conected_list = this.conected_list.filter(
          (x) => x.p1.item.id != this.start_pointer.item.id
        )
        //add new
        this.test.push({
          p1: this.start_pointer,
          p2: this.end_pointer,
        })
      }
      if (this.test.length > 0) {
        this.test.forEach((x) => {
          if (x.p1 && x.p2) {
            const canvas = document.getElementById('canvas')
            const ctx = canvas.getContext('2d')
            ctx.clearRect(0, 0, canvas.width, canvas.height)
            const container = canvas.parentNode
            // eslint-disable-next-line camelcase
            const parent_pos = container.getBoundingClientRect()
            // eslint-disable-next-line camelcase
            canvas.width = parent_pos.right - parent_pos.left
            // eslint-disable-next-line camelcase
            canvas.height = parent_pos.bottom - parent_pos.top
            const point1 = {
              // eslint-disable-next-line camelcase
              x:
                this.start_pointer.left -
                parent_pos.left +
                this.start_pointer.width,
              // eslint-disable-next-line camelcase
              y:
                this.start_pointer.top -
                parent_pos.top +
                this.start_pointer.height / 2,
            }
            const point2 = {
              // eslint-disable-next-line camelcase
              x: this.end_pointer.left - parent_pos.left,
              // eslint-disable-next-line camelcase
              y:
                this.end_pointer.top -
                parent_pos.top +
                this.end_pointer.height / 2,
            }
            ctx.moveTo(point1.x, point1.y)
            ctx.lineTo(point2.x, point2.y)
            ctx.stroke()
          }
        })
      }
    },
    handleResize() {
      const canvas = document.getElementById('canvas_connect_' + this.id)
      const ctx = canvas.getContext('2d')
      ctx.clearRect(0, 0, canvas.width, canvas.height)
      const container = canvas.parentNode
      let parent_pos = container.getBoundingClientRect()
      canvas.width = parent_pos.right - parent_pos.left
      canvas.height = parent_pos.bottom - parent_pos.top
      for (let index = 0; index < this.test.length; index++) {
        const value = this.conected_list[index]
        if (value) {
          let target = document.getElementById('pointer_' + value.p1.item.id)
          let box = target.getBoundingClientRect()
          let start_pointer = {
            top: box.top,
            right: box.right,
            bottom: box.bottom,
            left: box.left,
            width: box.right - box.left,
            height: box.bottom - box.top,
          }
          target = document.getElementById('pointer_' + value.p2.item.id)
          box = target.getBoundingClientRect()
          let end_pointer = {
            top: box.top,
            right: box.right,
            bottom: box.bottom,
            left: box.left,
            width: box.right - box.left,
            height: box.bottom - box.top,
          }

          let point1 = {
            x: start_pointer.left - parent_pos.left + start_pointer.width,
            y: start_pointer.top - parent_pos.top + start_pointer.height / 2,
          }
          let point2 = {
            x: end_pointer.left - parent_pos.left,
            y: end_pointer.top - parent_pos.top + end_pointer.height / 2,
          }
          ctx.imageSmoothingEnabled = true
          let start = { x: point1.x, y: point1.y }
          let cp1 = { x: (point2.x + point1.x) / 2, y: point1.y }
          let cp2 = { x: (point2.x + point1.x) / 2, y: point2.y }
          let end = { x: point2.x, y: point2.y }
          // Cubic Bézier curve
          ctx.lineWidth = 5
          // console.log(value.p1.index)
          ctx.strokeStyle = this.colors[value.p1.index]
          ctx.beginPath()
          ctx.moveTo(start.x, start.y)
          ctx.bezierCurveTo(cp1.x, cp1.y, cp2.x, cp2.y, end.x, end.y)
          ctx.stroke()
        }
      }
    },
  },
}
Array.prototype.remove = function (key, value) {
  const index = this.findIndex((obj) => obj[key] === value)
  return index >= 0 ? [...this.slice(0, index), ...this.slice(index + 1)] : this
}
</script>

