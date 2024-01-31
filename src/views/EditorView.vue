<template>
  <div class="list-container">
    <HeaderOptions />
    <el-table :data="tableData" style="width: 100%">
      <el-table-column type="expand">
        <template slot-scope="scope">
          <AIInputEditor :index="scope.$index" :article-info="scope.row" />
        </template>
      </el-table-column>
      <el-table-column label="序号" type="index" width="50" />
      <el-table-column label="日期" width="180">
        <template slot-scope="scope">
          <i class="el-icon-time" />
          <span style="margin-left: 10px">{{ scope.row.PublishAt | global_filter("YYYY-MM-DD HH:mm") }}</span>
        </template>
      </el-table-column>
      <el-table-column label="标题">
        <template slot-scope="scope">
          <a :href="scope.row.Url" target="_blank">{{ scope.row.Title }}</a>
        </template>
      </el-table-column>
      <el-table-column label="公司" width="180" align="center">
        <template slot-scope="scope">
          <el-tag size="small" style="margin-right:5px; margin-top: 5px;">{{ scope.row.Site.SiteMeta && scope.row.Site.SiteMeta.MetaValue || '-' }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="分类" width="100">
        <template slot-scope="{ row }">
          {{ row.Type || '-' }}
        </template>
      </el-table-column>
      <el-table-column label="作者" width="150" prop="Author.AuthorName" />
      <el-table-column label="内容源" width="120" prop="Site.Tag"></el-table-column>
    </el-table>
    <el-pagination class="pagination" :current-page.sync="currentPage" background layout="prev, pager, next" :total="total" @current-change="handleCurrentChange" />
  </div>
</template>

<script>
import { EventBus } from '../utils/event-bus'
import AIInputEditor from '@/components/Audit/AIInputEditor.vue'
import HeaderOptions from '@/components/Audit/HeaderOptions.vue'
const API_ArticleList_URL = 'http://82.157.175.33:4000/api/ArticleList'
const API_RejectArticle_URL = 'http://82.157.175.33:4000/api/RejectArticle'

export default {
  components: {
    AIInputEditor,
    HeaderOptions
  },
  data() {
    return {
      tableData: [],
      total: 0,
      currentPage: 1
    }
  },
  mounted() {
    // 获取页面初始数据
    this.getArticleList(10, 0)
    this.initEvent()
  },
  methods: {
    // handleEdit(index, row) {
    //   console.log(index, row);
    // },
    getArticleList(Limit, Offset, data) {
      fetch(`${API_ArticleList_URL}?${new URLSearchParams({ Limit, Offset, ...data })}`, {
        method: 'GET'
      }).then(response => response.json()).then(result => {
        EventBus.$emit('searchEnd', result.data)
        if (result.code === 0) {
          this.tableData = result.data
          this.total = result.total
          return
        }
        this.$message.error('获取文章列表失败，请重试')
      }).catch(() => {
        EventBus.$emit('searchEnd', {})
        this.$message.error('catch 获取文章列表失败，请重试')
      })
    },
    initEvent() {
      EventBus.$on('ArticleItemDelete', row => {
        const index = this.tableData.indexOf(row)
        fetch(API_RejectArticle_URL, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            ArticleID: row.ID
          })
        }).then(response => response.json()).then(result => {
          if (result.code === 0 && result.data === true) {
            this.tableData.splice(index, 1)
            return
          }
          this.$message.error('删除失败，请重试')
        }).catch(() => {
          this.$message.error('catch 删除失败，请重试')
        })
      })

      // 搜索
      EventBus.$on('search', data => {
        this.getArticleList(10, 1, data)
        this.filterData = data
        this.currentPage = 1
      })
    },
    handleCurrentChange(val) {
      this.getArticleList(10, val, this.filterData)
    }
  }
}
</script>
<style lang="scss" scoped>
a {
  // text-decoration: none;
  color: #409eff;
}
.list-container {
  // max-width: 1200px;
  margin: 0 auto;
  padding: 10px;
  box-sizing: border-box;

}
.pagination {
  margin-top: 20px;
  text-align: center;
}
</style>
