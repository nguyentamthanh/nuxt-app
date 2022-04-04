<template>
  <div class="h-full w-full bg-white m-auto">
    <div class="w-full flex-row relative">
      <client-only>
        <canvas
          class="w-full h-full absolute top-0 border border-3 pointer-events-none"
          id="canvas"
        ></canvas>
      </client-only>

      <div class="bg-green-500 text-center border rounded-lg bg mx-auto">
        <div class="text-center">Câu {{ test.id }}</div>
      </div>
      <div class="w-full h-full flex">
        <div class="w-1/2">
          <div
            class="p-3 bg-white mx-12 h-20 my-5 cursor-pointer relative border rounded-lg text-center hover:(border-blue-300)"
            v-for="item in list1"
            :key="item.id"
            @click="MouseClick(item, $event)"
          >
            {{ item.content }}
            <div
              v-on:click.prevent
              class="absolute w-5 h-5 right-0 top-1/2 bg-white border border-2 shadow-md rounded-full border-gray-300 transform translate-x-1/2 -translate-y-1/2 z-50 pointer-events-none"
            ></div>
          </div>
        </div>

        <div class="w-1/2">
          <div
            class="p-3 mx-12 my-10 cursor-pointer relative border rounded-lg text-center"
            v-for="item in list2"
            :key="item.id"
            @click="MouseClick(item, $event)"
          >
            {{ item.content }}
            <div
              class="absolute w-5 h-5 left-0 top-1/2 bg-white border border-2 shadow-md rounded-full border-gray-300 transform -translate-x-1/2 -translate-y-1/2 z-50 pointer-events-none"
            ></div>
          </div>
        </div>
      </div>
      <div class="py-4 flex justify-center ">
        <el-button type="primary" plain class="" @click="Check()"
          >Check</el-button
        >
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'
export default {
  data() {
    return {
      test: {},
      list1: '',
      list2: '',
      questions: {
        Diem: [],
        question: {
          content: '',
        },
        answer: [
          {
            id: 1,
            group: 1,
            title: 'A',
            content: '',
          },
          {
            id: 2,
            group: 1,
            title: 'B',
            content: '',
          },
          {
            id: 3,
            group: 2,
            title: 'C',
            content: '',
          },
          {
            id: 4,
            group: 2,
            title: 'D',
            content: '',
          },
        ],
      },
      start_pointer: '',
      end_pointer: '',
      debug_string: 'nothing',
      connected_list: [],
      id: this.$route.params.id,
    }
  },
  created() {
    if (process.client) {
      window.addEventListener('resize', this.draw)
    }
    this.getdata(this.$route.params.id)
  },
  destroyed() {
    if (process.client) {
      window.removeEventListener('resize', this.draw)
    }
  },
  methods: {
    getdata(id) {
      axios
        .get('https://6214967f89fad53b1f17fd73.mockapi.io/api/noitu/q/' + id)
        .then((res) => {
          this.test = res.data
          this.list1 = this.test.answer.filter((x) => x.group === 1)
          this.list2 = this.test.answer.filter((x) => x.group === 2)
        })
    },
    MouseClick(item, event) {
      let target = event.target
      var canvas = document.getElementById('canvas'),
        ctx = canvas.getContext('2d')
      if (item.group === 1) {
        this.start_pointer = {
          id: item.id,
          x:
            target.getBoundingClientRect().x -
            canvas.getBoundingClientRect().x +
            target.getBoundingClientRect().width,
          y:
            target.getBoundingClientRect().y -
            canvas.getBoundingClientRect().y +
            target.getBoundingClientRect().height / 2,
        }
        // console.log(item.id)
      } else {
        this.end_pointer = {
          id: item.id,
          x:
            target.getBoundingClientRect().x - canvas.getBoundingClientRect().x,
          y:
            target.getBoundingClientRect().y -
            canvas.getBoundingClientRect().y +
            target.getBoundingClientRect().height / 2,
        }
        //remove ans
        this.connected_list = this.connected_list.filter(
          (x) => x.p1.id != this.start_pointer.id
        )
        this.connected_list = this.connected_list.filter(
          (x) => x.p2.id != item.id
        )

        this.connected_list.push({
          p1: this.start_pointer,
          p2: this.end_pointer,
        })

        this.start_pointer = ''
        this.end_pointer = ''

        // console.log(this.connected_list)
      }
      // this.connected_list = this.connected_list.filter(
      //   (x) => x.p1.id != this.start_pointer.id
      // )
      // this.connected_list = this.connected_list.filter((x) => x.p2.id != id)
      this.draw()
    },
    draw() {
      var canvas = document.getElementById('canvas'),
        ctx = canvas.getContext('2d')
      const container = canvas.parentNode //Thay doi kich thuoc phan tu canvas phu hop voi bien cha
      let parent_pos = container.getBoundingClientRect() // cung cap thong tin kic thuoc phan tu trong 1 khung hinh
      canvas.width = parent_pos.right - parent_pos.left // tinh chieu dai va chieu cao cua canvas
      canvas.height = parent_pos.bottom - parent_pos.top
      ctx.clearRect(0, 0, canvas.width, canvas.height) //dat kich thuoc hinh chu nhat mau den trong suot
      for (let index = 0; index < this.connected_list.length; index++) {
        const x = this.connected_list[index]
        let p1 = x.p1
        let p2 = x.p2
        // console.log(this.connected_list)
        ctx.imageSmoothingEnabled = true //lam dep do ro diem anh
        let start = { x: p1.x, y: p1.y }
        let cp1 = {
          x: (p2.x + p1.x) / 2,
          y: p1.y,
        }
        let cp2 = {
          x: (p1.x + p2.x) / 2,
          y: p2.y,
        }
        let end = { x: p2.x, y: p2.y }
        // ctx.fillStyle = 'red'
        // ctx.fill()
        ctx.lineWidth = 5
        ctx.beginPath()
        ctx.moveTo(start.x, start.y)
        ctx.bezierCurveTo(cp1.x, cp1.y, cp2.x, cp2.y, end.x, end.y)
        ctx.stroke()
      }
    },
    Check() {
      let x = []
      this.connected_list.forEach((item) => {
        x.push(item.p1.id + item.p2.id)
      })
      console.log('day la x', x)

      let y = []
      this.test.Diem.forEach((item) => {
        y.push(item.p1 + item.p2)
      })
      console.log('day la y', y)
      if (_.isEqual(x.sort(), y.sort())) {
        this.$message({
          message: 'Đáp án đúng',
          type: 'success',
        })
      } else {
        this.$message({
          message: 'Đáp án sai ',
          type: 'error',
        })
      }
    },
  },
}
</script>
