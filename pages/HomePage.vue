<template>
  <div class="container mx-auto p-5">
    <div class="flex flex-row justify-between text-center text-sm text-base">
      <div class="flex flex-row justify-center">
        <div
          class="
            bg-gradient-to-r
            from-purple-400
            to-red-400
            w-10
            h-10
            rounded-lg
          "
        ></div>
        <h1 class="text-3xl text-gray-600 ml-2">Logo</h1>
      </div>
      <div class="mt-2">
        <nuxt-link
          class="
            text-white
            hover:text-purple-600
            p-4
            px-3
            sm:px-4
            border
            rounded-lg
            bg-green-400
          "
          to="/data"
          >Dữ liệu Câu Hỏi</nuxt-link
        >
        <nuxt-link
          class="
            text-white
            hover:text-purple-600
            p-4
            px-3
            sm:px-4
            border
            rounded-lg
            bg-red-400
          "
          to="/Do"
          >Làm Bài</nuxt-link
        >
        <nuxt-link
          class="
            text-white
            hover:text-purple-600
            p-4
            px-3
            sm:px-4
            border
            rounded-lg
            bg-blue-400
          "
          to="/CreateNew"
          >Tạo Mới Câu Hỏi</nuxt-link
        >
      </div>
    </div>

  </div>
</template>

<script>
export default {
  
}
</script>

<style>
</style>
