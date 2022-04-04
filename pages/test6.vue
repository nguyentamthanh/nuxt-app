<template>
  <div class="w-full h-full border py-10 shadow-md">
    <el-input
      class="px-10"
      v-model="test.content"
      type="textarea"
      :rows="2"
      :column="2"
      placeholder="Điền câu hỏi"
      >{{ test.id }}</el-input
    >
    <div class="w-full flex mt-6 space-x-20">
      <div class="w-1/2 text-center">
        <div v-for="a in list1" :key="a.id" class="mt-4">
          <el-input
            class="p-7 px-10"
            v-model="a.content"
            type="textarea"
            :rows="2"
            >{{ test.answer }}</el-input
          >
        </div>
      </div>
      <div class="w-1/2 text-center">
        <div v-for="a in list2" :key="a.id" class="mt-4">
          <el-input
            class="p-7 px-10"
            v-model="a.content"
            type="textarea"
            :rows="2"
          ></el-input>
        </div>
      </div>
    </div>
    <div class="px-10">
      <el-button class="" type="danger" plain @click="putData(id)"
        >Cập nhật
      </el-button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      list1: [],
      list2: [],
      test: {},
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
