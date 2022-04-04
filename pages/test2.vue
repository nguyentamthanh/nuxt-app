<template>
  <div class="w-full" v-if="q_data && q_data.question">
    <div class="select-none py-1" v-if="q_data.question.data.type === 'image'">
      <img :src="q_data.question.data.url" alt="Question" />
    </div>
    <div class="select-none py-1 flex" v-else>
      <Tinymce
        v-if="
          is_edit === $ENUM.QUIZ_ACTIONS.CREATE ||
          is_edit === $ENUM.QUIZ_ACTIONS.UPDATE
        "
        class="w-full min-h-15 border hover:(border-blue-300)"
        v-model="q_data.question.data.content"
        :inline="true"
      >
      </Tinymce>
      <!--      <el-input class="mx-3" type="textarea" v-if="is_edit === $ENUM.QUIZ_ACTIONS.CREATE || is_edit === $ENUM.QUIZ_ACTIONS.UPDATE" :placeholder="q_data.question.data.placeholder" v-model="q_data.question.data.content" size="mini"></el-input>-->
      <span
        v-else
        class="w-full break-words overflow-ellipsis"
        v-html="q_data.question.data.content"
      />
    </div>
    <el-row
      :gutter="20"
      v-if="
        is_edit === $ENUM.QUIZ_ACTIONS.CREATE ||
        is_edit === this.$ENUM.QUIZ_ACTIONS.UPDATE
      "
    >
      <el-col :span="24" class="flex justify-end">
        <div class="flex h-8">
          <el-button
            v-if="
              is_edit === $ENUM.QUIZ_ACTIONS.CREATE ||
              is_edit === $ENUM.QUIZ_ACTIONS.UPDATE
            "
            class="cursor-pointer"
            size="mini"
            @click="addAnswer"
            >Thêm Đáp Án</el-button
          >
          <el-button
            :type="q_data.question.suggest.content !== '' ? 'primary' : ''"
            class="cursor-pointer"
            size="mini"
            @click="openDialogSuggest"
            >Gợi Ý</el-button
          >
        </div>
      </el-col>
    </el-row>
    <div class="w-full flex flex-row relative">
      <canvas
        class="
          w-full
          h-full
          absolute
          top-0
          z-40
          border border-0
          rounded
          border-gray-200
          pointer-events-none
        "
        :id="'canvas_connect_' + id"
      ></canvas>
      <div class="w-full h-full flex">
        <div class="w-1/2">
          <div
            class="
              min-w-100px
              py-3
              bg-white
              mx-10
              my-5
              cursor-pointer
              relative
              border
              rounded-lg
              text-center
            "
            v-for="(item, index) in list0"
            :id="item.id"
            @click="
              is_edit === $ENUM.QUIZ_ACTIONS.VIEW ? MouseClick(item, index) : ''
            "
            :key="index"
            :class="
              start_pointer && start_pointer.item.id == item.id
                ? 'border-blue-300'
                : '' || is_edit === $ENUM.QUIZ_ACTIONS.VIEW
                ? 'hover:(border-blue-500)'
                : ''
            "
          >
            <div
              v-if="
                (index > 1 && is_edit === $ENUM.QUIZ_ACTIONS.CREATE) ||
                is_edit === $ENUM.QUIZ_ACTIONS.UPDATE
              "
              class="
                absolute
                right-0
                top-0
                transform
                -translate-y-1/2
                translate-x-1/2
              "
            >
              <el-button
                class="cursor-pointer"
                size="mini"
                @click="removeAns(0, item, index)"
                icon="el-icon-close"
                circle
              />
            </div>
            <div
              v-if="
                is_edit === $ENUM.QUIZ_ACTIONS.CREATE ||
                is_edit === $ENUM.QUIZ_ACTIONS.UPDATE
              "
            >
              <!--              <el-input type="textarea" :placeholder="item.placeholder" v-model="item.data.content"></el-input>-->
              <Tinymce
                class="w-full min-h-15 border hover:(border-blue-300)"
                :id="item.id + 'a'"
                :idx="item.id + 'a'"
                v-model="item.data.content"
                :inline="true"
              >
              </Tinymce>
            </div>
            <div
              v-else
              class="w-full select-none break-words overflow-ellipsis"
              v-html="item.data.content"
            />
            <div
              :id="'pointer_' + item.id"
              class="
                absolute
                w-7
                h-7
                right-0
                top-1/2
                bg-white
                border border-2
                shadow-md
                rounded-full
                border-gray-300
                transform
                translate-x-1/2
                -translate-y-1/2
                z-50
                cursor-pointer
                hover:(border-blue-500)
              "
              @click="MouseClick(item, index)"
            >
              <span class="flex justify-center items-center">{{
                item.label
              }}</span>
            </div>
          </div>
        </div>
        <div class="w-1/2">
          <div
            class="
              min-w-100px
              py-3
              bg-white
              mx-10
              my-5
              cursor-pointer
              relative
              border
              rounded-lg
              text-center
            "
            v-for="(item, index) in list1"
            :key="index"
            :id="item.id"
            @click="
              is_edit === $ENUM.QUIZ_ACTIONS.VIEW ? MouseClick(item, index) : ''
            "
            :class="
              is_edit === $ENUM.QUIZ_ACTIONS.VIEW
                ? 'hover:(border-blue-500)'
                : ''
            "
          >
            <div
              v-if="
                (index > 1 && is_edit === $ENUM.QUIZ_ACTIONS.CREATE) ||
                is_edit === $ENUM.QUIZ_ACTIONS.UPDATE
              "
              class="
                absolute
                right-0
                top-0
                transform
                -translate-y-1/2
                translate-x-1/2
              "
            >
              <el-button
                class="cursor-pointer"
                size="mini"
                @click="removeAns(1, item, index)"
                icon="el-icon-close"
                circle
              />
            </div>
            <div
              v-if="
                is_edit === $ENUM.QUIZ_ACTIONS.CREATE ||
                is_edit === $ENUM.QUIZ_ACTIONS.UPDATE
              "
            >
              <!--              {{item}}-->
              <!--              <el-input type="textarea" :placeholder="item.placeholder" v-model="item.data.content"></el-input>-->
              <Tinymce
                class="w-full min-h-15 border hover:(border-blue-300)"
                v-model="item.data.content"
                :inline="true"
              >
              </Tinymce>
            </div>
            <div
              v-else
              class="w-full break-words overflow-ellipsis select-none"
              v-html="item.data.content"
            />
            <div
              :id="'pointer_' + item.id"
              class="
                absolute
                w-7
                h-7
                left-0
                top-1/2
                bg-white
                border border-2
                shadow-md
                rounded-full
                border-gray-300
                transform
                -translate-x-1/2 -translate-y-1/2
                z-50
                cursor-pointer
                text-center
                hover:(border-blue-500)
              "
              @click="MouseClick(item, index)"
            >
              <span class="flex justify-center items-center">{{
                item.label
              }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <el-dialog title="Gợi Ý" :visible.sync="dialogSuggest">
      <Tinymce class="p-3 w-full" v-model="suggest"> </Tinymce>
      <div slot="footer" class="dialog-footer">
        <el-button
          @click="
            dialogSuggest = false
            suggest = ''
          "
        >
          {{ $t('admin.cancel') }}
        </el-button>
        <el-button type="primary" @click="saveDialogSuggest">
          {{ $t('admin.save') }}
        </el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'CONNECT',
  props: {
    data: {
      type: Object,
    },
    index: {
      type: Number,
      default: 0,
    },
    is_edit: {
      type: String,
      default: () => this.$ENUM.QUIZ_ACTIONS.VIEW,
    },
  },
  data() {
    return {
      log: {},
      q_data: '',
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
      start_pointer: '',
      end_pointer: '',
      list0: [],
      list1: [],
      conected_list: [],
      id: this.gen_id(),
      dialogSuggest: false,
      suggest: '',
    }
  },
  watch: {
    q_data: {
      handler: function () {
        this.list0 = this.q_data.answers
          .filter((item) => item.group === 0)
          .map((x, index) => {
            x.label = index + 1
            return x
          })
        this.list1 = this.q_data.answers
          .filter((item) => item.group === 1)
          .map((x, index) => {
            x.label = String.fromCharCode(65 + index)
            return x
          })
        this.$emit('update:data', this.q_data)
      },
      deep: true,
    },
    data: function () {
      if (this.data) {
        this.q_data = this.data
      }
    },
    // 'data.select': function () {
    //   if (this.data) {
    //     this.q_data.select = this.data.select
    //   }
    // },
  },
  mounted() {
    this.$nextTick(() => {
      if (this.q_data) {
        this.initDraw(this.q_data.select)
      }
    })
    if (process.client) {
      window.addEventListener('resize', this.handleResize)
    }
  },
  created() {
    if (this.is_edit === this.$ENUM.QUIZ_ACTIONS.CREATE) {
      let xdata = {
        select: '',
        type: this.$ENUM.QUIZ_TYPE.CONNECT.type,
        index: 0,
        point: 0,
        question: {
          category: '',
          data: {
            note: '<p>Note</p>',
            type: 'text',
            placeholder: 'Nhập nội dung câu hỏi',
            content: '',
          },
          suggest: {
            content: '',
            type: 'text',
          },
        },
        answers: [
          {
            id: this.gen_id() - 3,
            group: 0,
            data: {
              type: 'text',
              title: 'A',
              content: '',
              placeholder: 'Nhập nội dung câu trả lời',
            },
          },
          {
            id: this.gen_id() - 2,
            group: 1,
            data: {
              type: 'text',
              title: 'B',
              content: '',
              placeholder: 'Nhập nội dung câu trả lời',
            },
          },
          {
            id: this.gen_id() - 1,
            group: 0,
            data: {
              type: 'text',
              title: 'C',
              content: '',
              placeholder: 'Nhập nội dung câu trả lời',
            },
          },
          {
            id: this.gen_id(),
            group: 1,
            data: {
              type: 'text',
              title: 'D',
              content: '',
              placeholder: 'Nhập nội dung câu trả lời',
            },
          },
        ],
        correct: '',
        text: '',
      }
      this.q_data = xdata
    } else if (this.is_edit === this.$ENUM.QUIZ_ACTIONS.UPDATE) {
      this.q_data = JSON.parse(JSON.stringify(this.data))
      let test = this.q_data.answers
        .filter((item) => item.group === undefined)
        .map((x, index) => {
          x.label = index + 1
          return x
        })
      if (test.length > 0) {
        this.q_data.answers = [
          {
            id: this.gen_id() - 3,
            group: 0,
            data: {
              type: 'text',
              title: 'A',
              content: '',
              placeholder: 'Nhập nội dung câu trả lời',
            },
          },
          {
            id: this.gen_id() - 2,
            group: 1,
            data: {
              type: 'text',
              title: 'B',
              content: '',
              placeholder: 'Nhập nội dung câu trả lời',
            },
          },
          {
            id: this.gen_id() - 1,
            group: 0,
            data: {
              type: 'text',
              title: 'C',
              content: '',
              placeholder: 'Nhập nội dung câu trả lời',
            },
          },
          {
            id: this.gen_id(),
            group: 1,
            data: {
              type: 'text',
              title: 'D',
              content: '',
              placeholder: 'Nhập nội dung câu trả lời',
            },
          },
        ]
      }
      this.q_data.select = this.q_data.correct
    } else {
      this.q_data = JSON.parse(JSON.stringify(this.data))
    }
    this.list0 = this.q_data.answers
      .filter((item) => item.group === 0)
      .map((x, index) => {
        x.label = index + 1
        return x
      })
    this.list1 = this.q_data.answers
      .filter((item) => item.group === 1)
      .map((x, index) => {
        x.label = String.fromCharCode(65 + index)
        return x
      })
    this.conected_list = []
  },
  computed: {
    q_index: function () {
      return this.index || 0
    },
  },

  destroyed() {
    if (process.client) {
      window.removeEventListener('resize', this.handleResize)
    }
  },
  methods: {
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
    removeAns(group, item, index) {
      this.q_data.answers.splice(
        this.q_data.answers.findIndex((el) => el.id === item.id),
        1
      )
      if (group == 0) {
        this.conected_list = this.conected_list.filter(
          (x) => x.p1.item.id != item.id
        )
        //this.list0.splice(index, 1)
      } else if (group == 1) {
        this.conected_list = this.conected_list.filter(
          (x) => x.p2.item.id != item.id
        )
        //this.list1.splice(index, 1)
      } else {
        console.log('invalid group, do nothing')
      }
      this.$nextTick(() => {
        this.handleResize()
      })
    },
    getLabel() {
      let xlabel = []
      for (let index = 0; index < this.conected_list.length; index++) {
        const value = this.conected_list[index]
        xlabel.push(`${value.p1.item.label}-${value.p2.item.label}`)
      }
      return xlabel
    },
    // getAns(){
    //   let xAns = {}
    //   for (let index = 0; index < this.conected_list.length; index++) {
    //     const value = this.conected_list[index]
    //     console.log(value)
    //     xAns[`${value.p1.item.id}`] = value.p2.item.id
    //   }
    //   return xAns
    // },
    initDraw(drawData) {
      let select_list = drawData
      if (select_list) {
        for (const property in select_list) {
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
      let target = document.getElementById('pointer_' + item.id)
      let box = target.getBoundingClientRect()
      if (item.group === 0) {
        this.start_pointer = {
          item: item,
          top: box.top,
          right: box.right,
          bottom: box.bottom,
          left: box.left,
          width: box.right - box.left,
          height: box.bottom - box.top,
          index: index,
        }
      } else if (this.start_pointer && item.group == 1) {
        this.end_pointer = {
          item: item,
          top: box.top,
          right: box.right,
          bottom: box.bottom,
          left: box.left,
          width: box.right - box.left,
          height: box.bottom - box.top,
          index: index,
        }
        // remove other
        this.conected_list = this.conected_list.filter(
          (x) => x.p2.item.id != item.id
        )
        this.conected_list = this.conected_list.filter(
          (x) => x.p1.item.id != this.start_pointer.item.id
        )
        // add new
        this.conected_list.push({
          p1: this.start_pointer,
          p2: this.end_pointer,
        })
        this.log[`${this.start_pointer.item.id}`] = this.end_pointer.item.id
        this.q_data.select = this.log
        if (
          this.is_edit === this.$ENUM.QUIZ_ACTIONS.CREATE ||
          this.is_edit === this.$ENUM.QUIZ_ACTIONS.UPDATE
        ) {
          this.q_data.correct = this.log
        }
        this.q_data.text = this.getLabel()
        this.end_pointer = ''
        this.handleResize()
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
      for (let index = 0; index < this.conected_list.length; index++) {
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
    gen_id() {
      return Date.now()
    },
    addAnswer() {
      let list0 = {
        id: this.gen_id() - 1,
        group: 0,
        data: {
          placeholder: 'Nhập câu trả lời',
          content: '',
          type: 'text',
          title: 'A.',
          url: 'https://youtube.com/asdfasdf',
        },
        suggest: {
          content: '',
          title: 'Hint',
          type: 'text',
          url: 'https://youtube.com/asdfasdf',
        },
      }
      let list1 = {
        id: this.gen_id(),
        group: 1,
        data: {
          placeholder: 'Nhập câu trả lời',
          content: '',
          type: 'text',
          title: 'A.',
          url: 'https://youtube.com/asdfasdf',
        },
        suggest: {
          content: '',
          title: 'Hint',
          type: 'text',
          url: 'https://youtube.com/asdfasdf',
        },
      }
      this.q_data.answers.push(list0)
      this.q_data.answers.push(list1)
      this.$nextTick(() => {
        this.handleResize()
      })
    },
  },
}
Array.prototype.remove = function (key, value) {
  const index = this.findIndex((obj) => obj[key] === value)
  return index >= 0 ? [...this.slice(0, index), ...this.slice(index + 1)] : this
}
</script>
