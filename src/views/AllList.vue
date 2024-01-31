<template>
  <ul v-infinite-scroll="loadMore" infinite-scroll-disabled="loading" infinite-scroll-distance="10">
    <template v-if="list.length">
      <li v-for="item in list" :key="item.ID" class="list-card-container">
        <ListCard :card-info="item" />
      </li>
    </template>
    <template v-else>
      暂无数据~
    </template>
  </ul>
</template>

<script>
import ListCard from '../components/Touch/ListCard.vue'
const GetArticleSummaryList = 'http://82.157.175.33:4000/api/GetArticleSummaryList'

export default {
  components: {
    ListCard
  },
  data() {
    return {
      list: [],
      total: 100,
      index: 0
    }
  },
  methods: {
    loadMore() {
      this.loading = true
      if (this.list.length < this.total) {
        this.getArticleSummaryList(10, this.index)
        this.index += 1
      }
    },
    getArticleSummaryList(Limit, Offset) {
      fetch(`${GetArticleSummaryList}?${new URLSearchParams({ Limit, Offset })}`, {
        method: 'GET'
      }).then(response => response.json()).then(result => {
        this.loading = false
        if (result.code === 0 && result.data) {
          this.list = this.list.concat(result.data)
          this.total = result.total
          return
        }
        this.$message.error('获取文章列表失败，请重试')
      }).catch(() => {
        this.loading = false
        this.$message.error('catch 获取文章列表失败，请重试')
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.list-card-container {
  margin-bottom: 10px;
}
</style>
