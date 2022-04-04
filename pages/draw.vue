<template>
  <div class="w-full h-full border py-10 shadow-md">
    <el-input
      class="px-10"
      :rows="2"
      :column="2"
      type="textarea"
      placeholder="Nhập câu hỏi"
      v-model="questions.question.content"
    ></el-input>
    <div class="w-full flex mt-6 space-x-30">
      <div class="w-1/2">
        <div v-for="a in list1" :key="a" class="mt-4">
          <el-input
            class="p-7 px-10"
            :rows="2"
            v-model="a.content"
            type="textarea"
            placeholder="Nhập đáp án"
          ></el-input>
        </div>
      </div>
      <div class="w-1/2">
        <div v-for="a in list2" :key="a" class="mt-4">
          <el-input
            class="p-7 px-10"
            :rows="2"
            v-model="a.content"
            type="textarea"
            placeholder="Nhập đáp án"
          ></el-input>
        </div>
      </div>
    </div>
    <div class="px-10">
      <el-button type="danger" @click="AddQuestion" class="mx-auto"
        >Add Answer</el-button
      >
      <el-button type="success" plain @click="Save" class="mx-auto"
        >Save</el-button
      >
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'IndexPage',
  data() {
    return {
      list1: '',
      list2: '',
      questions: {
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
    }
  },
  created() {
    this.adddata()
  },
  methods: {
    AddQuestion() {
      this.questions.answer.push(
        {
          id: '',
          group: 1,

          title: 'A',
          content: '',
        },
        {
          id: '',
          group: 2,
          title: 'A',
          content: '',
        }
      )
      this.adddata()
    },
    adddata() {
      this.list1 = this.questions.answer.filter((x) => x.group === 1)
      this.list2 = this.questions.answer.filter((x) => x.group === 2)
    },

    Save() {
      axios({
        method: 'post',
        url: 'https://6214967f89fad53b1f17fd73.mockapi.io/api/noitu/q',
        data: {
          question: this.questions.question,
          answer: this.questions.answer,
        },
      }).then((res) => {
        this.$router.push('/data')
      })
    },
  },
}
</script>
