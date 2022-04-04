<template>
  <div class="h-full w-full bg-white">
    <div class="w-full flex flex-row relative">
      <client-only>
        <canvas
          class="w-full h-full absolute top-0 border border-3 border-black pointer-events-none"
          id="canvas"
        ></canvas>
      </client-only>
      <div class="w-full h-full flex">
        <div class="w-1/2">
          <div
            class="p-3 bg-white mx-12 h-20 my-5 cursor-pointer relative border rounded-lg text-center hover:(border-blue-300)"
            v-for="(item, index) in list0"
            :key="index"
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
            v-for="(item, index) in list1"
            :key="index"
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
</template>

<script>
export default {
  data() {
    return {
      list0: [
        {
          id: 11,
          content: 'Dog',
          group: 0,
        },
        {
          id: 12,
          content: 'Cat',
          group: 0,
        },
        {
          id: 13,
          content: 'Monkey',
          group: 0,
        },
      ],
      list1: [
        {
          id: 14,
          content: 'X',
          group: 1,
        },
        {
          id: 15,
          content: 'Y',
          group: 1,
        },
        {
          id: 16,
          content: 'Z',
          group: 1,
        },
      ],
      start_pointer: '',
      end_pointer: '',
      debug_string: 'nothing',
      connected_list: [],
      colors: ['#f56548', '#48f56c', '#48f56c', '#e748f5'],
    }
  },
  created() {
    if (process.client) {
      window.addEventListener('resize', this.handleResize)
    }
  },
  destroyed() {
    if (process.client) {
      window.removeEventListener('resize', this.handleResize)
    }
  },
  methods: {
    MouseClick(item, event) {
      let target = event.target
      var canvas = document.getElementById('canvas'),
        ctx = canvas.getContext('2d')
      if (item.group === 0) {
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
        console.log(item.id)
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
        //remove
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

        console.log(this.connected_list)
      }
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
        // ctx.fillRect(
        //   this.start_pointer.x - 20 / 2,
        //   this.start_pointer.y - 20 / 2,
        //   20,
        //   20
        // )
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
        // var random = Math.floor(Math.random() * this.colors.length)

        ctx.lineWidth = 5
        ctx.beginPath()
        // console.log(this.colors)
        this.colors.forEach(function (element) {
          ctx.strokeStyle = element
        })
        ctx.moveTo(start.x, start.y)
        ctx.bezierCurveTo(cp1.x, cp1.y, cp2.x, cp2.y, end.x, end.y)
        ctx.stroke()
      }
    },
  },
}
</script>
