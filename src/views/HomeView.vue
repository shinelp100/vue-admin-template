<template>
  <div class="home">
    <nav>
      <router-link to="/editor">Editor</router-link> |
      <router-link to="/list">List</router-link> |
      <router-link to="/strategy">strategy</router-link>
    </nav>
    <el-button type="primary" :loading="loading" @click="generateTask">{{ loading ? '生成任务中...' : '生成任务' }}</el-button>
  </div>
</template>

<script>
const API_URL = 'http://82.157.175.33:4000/api/echo'

export default {
  name: 'HomeView',
  components: {
  },
  data() {
    return {
      loading: false
    }
  },
  methods: {
    generateTask() {
      this.loading = true
      fetch(API_URL, {
        method: 'GET'
      }).then(response => response.json()).then(result => {
        this.loading = false
        if (result.code === 0) {
          this.$message.success('生成任务成功')
          return
        }
        this.$message.error('生成任务失败，请重试')
      }).catch(() => {
        this.loading = false
        this.$message.error('catch 生成任务失败，请重试')
      })
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
