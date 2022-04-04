<template>
  <div class="h-full w-full bg-white">
    <div class="w-full flex-row relative">
      <client-only>
        <canvas
          class="w-full h-full absolute top-0 border border-3 pointer-events-none"
          id="canvas"
        ></canvas>
      </client-only>

      <div v-for="i in test" :key="i.id" class="">
        <div class="bg-green-500 text-center border rounded-lg bg mx-auto">
          <div class="text-center">Câu {{ i.id }}</div>
        </div>
        <div class="w-full h-full flex">
          <div class="w-1/2">
            <div
              class="p-3 bg-white mx-12 h-20 my-5 cursor-pointer relative border rounded-lg text-center hover:(border-blue-300)"
              v-for="item in i.list1"
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
              v-for="item in i.list2"
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
      </div>
    </div>
    <el-footer>
      <div class="py-4 flex justify-center">
        <el-button type="danger" plain class="" @click="Check()"
          >Nộp bài</el-button
        >
      </div>
    </el-footer>
  </div>
</template>

<script>
import axios from 'axios'
import _ from 'lodash'
import { nanoid } from 'nanoid'
export default {
  data() {
    return {
      test2: '',
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
    }
  },
  created() {
    if (process.client) {
      window.addEventListener('resize', this.draw)
    }
    this.getdata()
  },
  destroyed() {
    if (process.client) {
      window.removeEventListener('resize', this.draw)
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
      // console.log('connection list', this.connected_list)
      this.connected_list.forEach((item) => {
        x.push(item.p1.id + item.p2.id)
      })
      console.log(x)
      console.log('day la x', x)
      let y = []
      this.test.forEach((item) => {
        y.push(item.Diem)
      })
      console.log('day la y', y)
      let c = []
      y.forEach((item) => {
        c.push(item)
      })
      console.log(c.length)
      let a = ''
      for (var i = 0; i < c.length; i++) {
        for (var n = 0; n < c[i].length; n++) {
          // console.log("ssss",c[i][n])

          Object.values(c[i][n]).forEach((x) => {
            a += x
          })

          //this.test2[`${nanoid}`] = Object.values(c[i][n])
          // var a = Object.values(c[i][n])
          // console.log(a);
        }
      }
      let z = []
      for (let index = 0; index < a.length / 42; index++) {
        let start = index * 42
        let end = start + 42
        z.push(a.substring(start, end))
      }
      console.log(z)
      if (_.isEqual(x.sort(), z.sort())) {
        this.$message({
          message: 'Đáp án đúng',
          type: 'success',
        })
      } else {
        this.$message({
          message: 'Đáp án sai ',
          type: 'error',
        })

        // for (var i = 0; i < y.length; i++) {
        //   for (var n = 0; n < y[i].length; n++) {
        //     const c = y[i][n]
        //     var g = [].push(c)
        //     console.log(c)

        //     // console.log('cccccc', g)
        //     for (var b = 0; b < g.length; b++) {
        //       var z = g[b]
        //       var j = []
        //       // console.log(z)
        //       var f = Array.from(z.split(21))
        //       f.forEach((item) => {
        //         j.push(item)
        //         // console.log(typeof item );
        //       })
        //       // console.log(j);
        //     }
        //   }
        // }

        // var a = Object.values(c)
        // console.log('aaaaa', a)
        // console.log(Object.keys(a))
        // console.log(Object.values(a))
        // for (var b = 0; b < a.length; b++) {
        //   // console.log(b)
        //   var z = a[b]
        //   // console.log(z)
        //   for (var v = 0; v < z.length; v++) {}
        // }
        // console.log('value',Object.keys(y))
        // if (_.isEqual(x.sort(), y[0])) {
        //   this.$message({
        //     message: 'Đáp án đúng',
        //     type: 'success',
        //   })
        // } else {
        //   this.$message({
        //     message: 'Đáp án sai ',
        //     type: 'warning',
        //   })
        //   // }
        // }
        // if (_.isEqual(x, y[i])) {
        //   console.log('dap an dung')
        //   this.$message({
        //     message: 'Đáp án đúng',
        //     type: 'success',
        //   })
        // } else {
        //   this.$message({
        //     message: 'Đáp án sai ',
        //     type: 'warning',
        //   })
        //   console.log('dap an sai')
        // }
        // this.test.forEach(x=>{
        //   UserAns=x.Diem
        //   console.log(x.Diem);
        // })
        // let x = []
        // // console.log("fsdfsdf",this.connected_list)
        // this.connected_list.forEach((item) => {
        //   // console.log('x', x)
        //   x.push(item.p1.id + item.p2.id)
        // })
        // let y = []
        // this.test.forEach((item) => {
        //   y.push(item.Diem)
        //   console.log('y', item.Diem)
        //   // a=Object.keys()
        //   //  Object.keys(item.Diem).forEach(x=>{
        //   //     item.Diem[x]
        //   // console.log(x)
        //   //  })
        // })
        // console.log(_.isEqual(x, y))
        // // console.log(typeof y)
        // // console.log(_.isEqual(x, y))
        // // if () {
        // //     console.log('dap an dung')
        // //   } else {
        // //     console.log('dap an sai')
        // //   }
        // // console.log('bien x',x)
        // // console.log('data',this.test)
        // // console.log('đáp án', this.test[0].Diem)
      }
    },
  },
}
</script>
