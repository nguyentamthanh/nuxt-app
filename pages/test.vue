
<template>
  <div class="w-full flex flex-row relative rounded-md shadow-md">
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
    <div class="w-full h-full flex">

      <div class="w-1/2">
        <div
          v-for="(i, index) in list0"
          :key="i.id"
          class="m-2 border relative text-center min-w-100px h-7"
        >
          <div
            :id="'pointer_' + i.id"
            class="!z-50 bg-red-200 absolute w-7 h-7 right-1/2 top-0"
            @click="MouseClick(i, index)"
          >
            {{ i.title }}
          </div>
        </div>
      </div>

      <div class="w-1/2">
        <div
          v-for="(i, index) in list1"
          :key="i.id"
          class="m-2 border relative text-center min-w-100px h-7"
        >
          <div
            :id="'pointer_' + i.id"
            class="!z-50 bg-red-200 absolute w-7 h-7 right-1/2 top-0"
            @click="MouseClick(i, index)"
          >
            {{ i.title }}
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexDraw',

  data() {
    return {
      qs: {
        id: 1,
        content: '',
        answer: [
          { id: '0', title: 'A0', group: 0 },
          { id: '1', title: 'B0', group: 0 },
          { id: '3', title: 'A1', group: 1 },
          { id: '4', title: 'B1', group: 1 },
        ],
      },
      list0: [],
      list1: [],
      test: [],
      start_pointer: '',
      end_pointer: '',
    }
  },
  // created: {
  //   // list0: () => {
  //   //   return this.qs.answer.filter(x => x.group === 0)
  //   // },
  //   // list1: () => {
  //   //   return this.qs.answer.filter(x => x.group === 1)
  //   // },
  //   this.list0 =this.qs.answer.filter(x => x.group === 0)
  // },
  created() {
    this.list0 = this.qs.answer.filter((x) => x.group === 0)
    this.list1 = this.qs.answer.filter((x) => x.group === 1)
  },
  methods: {
    MouseClick(item, index) {
      const target = document.getElementById('pointer_' + item.id)
      const box = target.getBoundingClientRect()
      if (item.group === 0) {
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
      } else if (this.start_pointer && item.group === 1) {
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
        this.test.push({
          p1: this.start_pointer,
          p2: this.end_pointer,
        })
        // this.test = this.test.filter(x => x.p2.item.id != item.id)
        // this.test = this.test.filter(x => x.p1.item.id != this.start_pointer.item.id)
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
  },
}
</script>


