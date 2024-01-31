<template>
  <div class="filter-select">
    <label class="mr25">
      <span>内容源：</span>
      <el-select v-model="contentSourceValue" filterable placeholder="请选择">
        <el-option v-for="item in contentSourceArr" :key="item.value" :label="item.label" :value="item.value" />
      </el-select>
    </label>

    <label class="mr25">
      <span>股票代码：</span>
      <el-select v-model="stockCodeValue" filterable placeholder="请选择">
        <el-option v-for="item in stockCodeArr" :key="item.value" :label="item.label" :value="item.value" />
      </el-select>
    </label>

    <label class="mr25">
      <span>新闻/消息：</span>
      <el-select v-model="contentTypeValue" filterable placeholder="请选择">
        <el-option v-for="item in contentTypeArr" :key="item.value" :label="item.label" :value="item.value" />
      </el-select>
    </label>

    <el-button type="primary" icon="el-icon-search" :loading="isSearch" @click="search">搜索</el-button>
  </div>
</template>

<script>
import { EventBus } from '@/utils/event-bus'
const API_URL = 'http://82.157.175.33:4000/api/GetSiteInfo'

export default {
  name: 'HeaderOptions',
  data() {
    return {
      contentSourceArr: [
        {
          label: '全部',
          value: ''
        }
      ],
      contentSourceValue: '',
      stockCodeArr: [
        {
          label: '全部',
          value: ''
        }
      ],
      stockCodeValue: '',
      contentTypeArr: [
        {
          label: '全部',
          value: ''
        },
        {
          label: '文章',
          value: 'article'
        },
        {
          label: '消息',
          value: 'new'
        }
      ],
      contentTypeValue: '',
      isSearch: false
    }
  },
  mounted() {
    this.initData()
    this.initEvent()
  },
  methods: {
    initData() {
      fetch(API_URL)
        .then(response => response.json())
        .then(result => {
          if (result.code === 0 && result.data) {
            this.contentSourceArr = this.contentSourceArr.concat(result.data.source)
            this.stockCodeArr = this.stockCodeArr.concat(result.data.stockCode)
            return
          }
          this.$message.error('获取站点信息失败，请重试')
        }).catch(() => {
          this.$message.error('catch 获取站点信息失败，请重试')
        })
    },
    initEvent() {
      EventBus.$on('searchEnd', () => {
        this.isSearch = false
      })
    },
    search() {
      this.isSearch = true
      EventBus.$emit('search', {
        ArticleType: this.contentTypeValue,
        SiteIdList: [...new Set([this.contentSourceValue, this.stockCodeValue].filter(item => item))]
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.filter-select {
  display: flex;
  align-content: center;
  padding: 0 10px 10px;
  justify-content: space-around;
}

.mr25 {
  margin-right: 25px;
}
</style>
