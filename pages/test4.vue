<template>
  <div class="h-full w-full bg-white">
    <div class="w-full flex-row relative">
      <client-only>
        <canvas
          class="w-full h-full absolute top-0 border border-3 border-black pointer-events-none"
          id="canvas"
        ></canvas>
      </client-only>
      <el-input
        class="px-10 pt-4"
        :rows="2"
        :column="2"
        type="textarea"
        placeholder="Nhập câu hỏi"
        v-model="questions.question.content"
      ></el-input>
      <div class="w-full flex mt-6 space-x-20">
        <div class="w-1/2">
          <div
            v-for="item in list1"
            :key="item.id"
            @click="MouseClick(item, $event)"
            class="p-3 bg-white mx-12 h-20 my-5 cursor-pointer relative border rounded-lg text-center hover:(border-blue-300)"
          >
            <el-input
              class="h-full w-full"
              :rows="2"
              v-model="item.content"
              type="textarea"
              placeholder="Nhập đáp án"
            ></el-input>
            <div
              v-on:click.prevent
              class="absolute w-5 h-5 right-0 top-1/2 bg-white border border-2 shadow-md rounded-full border-gray-300 transform translate-x-1/2 -translate-y-1/2 z-50 pointer-events-none"
            ></div>
          </div>
        </div>

        <div class="w-1/2">
          <div
            v-for="item in list2"
            :key="item.id"
            @click="MouseClick(item, $event)"
            class="p-3 bg-white mx-12 h-20 my-5 cursor-pointer relative border rounded-lg text-center hover:(border-blue-300)"
          >
            <el-input
              class="h-full w-full"
              :rows="2"
              v-model="item.content"
              type="textarea"
              placeholder="Nhập đáp án"
            ></el-input>
            <div
              class="absolute w-5 h-5 left-0 top-1/2 bg-white border border-2 shadow-md rounded-full border-gray-300 transform -translate-x-1/2 -translate-y-1/2 z-50 pointer-events-none"
            ></div>
          </div>
        </div>
      </div>
      <div class="px-10">
        <el-button class="" type="danger" plain @click="putData(id)"
          >Cập nhật
        </el-button>
      </div>
    </div>
  </div>
</template>
<script>
import axios from 'axios'
import { nanoid } from 'nanoid'
export default {
  data() {
    return {
      Diem: [],
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
            id: nanoid(),
            group: 1,
            title: 'A',
            content: '',
          },
          {
            id: nanoid(),
            group: 1,
            title: 'B',
            content: '',
          },
          {
            id: nanoid(),
            group: 2,
            title: 'C',
            content: '',
          },
          {
            id: nanoid(),
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
    this.getdata(this.$route.params.id)
  },
  methods: {
    getdata(id) {
      axios
        .get('https://6214967f89fad53b1f17fd73.mockapi.io/api/noitu/q/' + id)
        .then((res) => {
          this.test = res?.data?.question
          this.list1 = res?.data.answer.filter((x) => x.group === 1)
          this.list2 = res?.data.answer.filter((x) => x.group === 2)
        })
        .catch((err) => console.log(err))
    },
    putData(id) {
      axios({
        method: 'put',
        url: 'https://6214967f89fad53b1f17fd73.mockapi.io/api/noitu/q/' + id,
        data: {
          question: this.test,
          answer: this.list1.concat(this.list2),
        },
      }).then((res) => {
        this.$router.push('/data')
      })
    },
  },
}
</script>
