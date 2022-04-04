<template>
  <div class="w-full h-full">
    <div class="boder rounded-sm shadow-md w-auto h-auto m-3rem">
      <div class="p-4">
        <nuxt-link to="/Do">
          <el-button type="info" plain>Làm Bài</el-button>
        </nuxt-link>
        <nuxt-link to="/CreateNew" class="ml-7 mt-4">
          <el-button type="warning" plain>Tạo mới</el-button>
        </nuxt-link>
      </div>
      <el-table :data="tableData" class="w-full">
        <el-table-column label="STT" class="" width="300px">
          <template slot-scope="{ row }" class="">
            {{ row.id }}
          </template>
        </el-table-column>

        <el-table-column label="Nội dung câu hỏi" class="">
          <template slot-scope="{ row }" class="">
            {{ row.question.content }}
          </template>
        </el-table-column>

        <el-table-column label="" class="" width="120px">
          <template slot-scope="{ row }">
            <div class="">
              <el-button type="success" @click="Do(row.id)">Do</el-button>
            </div>
          </template>
        </el-table-column>

        <el-table-column label="" class="" width="120px">
          <template slot-scope="{ row }">
            <div class="">
              <el-button type="primary" @click="update(row.id)">Edit</el-button>
            </div>
          </template>
        </el-table-column>

        <el-table-column class="" width="120px">
          <template slot-scope="{ row }" class="">
            <el-button type="danger" @click="deleteData(row.id)"
              >Delete</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'IndexPage',
  data() {
    return {
      tableData: [],
    }
  },
  created() {
    this.getdata()
  },
  methods: {
    getdata() {
      this.tableData = []
      axios
        .get('https://6214967f89fad53b1f17fd73.mockapi.io/api/noitu/q')
        .then((res) => {
          this.tableData = res.data
        })
    },
    update(id) {
      this.$router.push('/update/' + id)
    },
    deleteData(id) {
      axios
        .delete('https://6214967f89fad53b1f17fd73.mockapi.io/api/noitu/q/' + id)
        .then((res) => {
          this.getdata()
          this.$message({
            message: 'Đã xóa thành công ',
            type: 'success',
          })
        })
    },
    Do(id) {
      this.$router.push('/lambai/' + id)
    },
  },
}
</script>
