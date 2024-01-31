<template>
  <div class="editor">
    <!-- <div class="score-item module">
      <span class="demonstration">评分：</span>
      <el-rate class="score" v-model="score" :colors="colors">
      </el-rate>
    </div> -->
    <div class="type-item module">
      <span class="demonstration">方式：</span>
      <el-radio v-for="(item, index) in AIProduceStrategy" :key="item.strategy" v-model="radio" :label="index">{{ item.name }}</el-radio>
      <el-button class="add-strategy" size="small" icon="el-icon-plus" @click="linkToAddStrategy" />
    </div>

    <el-input v-model="originalText" class="module" type="textarea" :autosize="{ minRows: 2, maxRows: 4}" maxlength="60000" show-word-limit placeholder="请输入原文内容" />
    <div class="btn">
      <el-button class="submit module" type="primary" :loading="isLoading" @click="AiInput"> {{ isSuccessInAI ? (isLoading ? '重新生成中...' : '重新生成') : (isLoading ? '生成中...' : '生成') }}</el-button>
    </div>
    <!-- <AIOutputEditor :res="resData"></AIOutputEditor> -->
    <AIOutputEditor v-if="isSuccessInAI" :res="resData" />
  </div>
</template>

<script>
import { EventBus } from '@/utils/event-bus'
import AIOutputEditor from '@/components/Audit/AIOutputEditor.vue'
const Strategy_List = 'http://82.157.175.33:4000/api/strategy/list'
const API_URL = 'http://82.157.175.33:4000/api/openai'

export default {
  name: 'AIEditor',
  components: {
    AIOutputEditor
  },
  props: {
    index: Number,
    articleInfo: Object
  },
  data() {
    return {
      score: 0,
      colors: ['#99A9BF', '#F7BA2A', '#FF9900'],
      AIProduceStrategy: [],
      radio: '',
      originalText: this.articleInfo?.Content || '',
      isSuccessInAI: false,
      isLoading: false,
      resData: {}
    }
  },
  mounted() {
    this.initEvent()
    this.getStrategyList()
  },
  methods: {
    initEvent() {
      const _this = this
      EventBus.$on('ArticleRegenerateSummary', function() {
        _this.AiInput()
      })
    },
    getStrategyList() {
      fetch(Strategy_List, {
        method: 'GET'
      }).then(response => response.json()).then(result => {
        if (result.code === 0) {
          this.AIProduceStrategy = result.data?.AIProduceStrategy || []
          return
        }
        this.$message.error('获取文章生成策略失败，请重试')
      }).catch(() => {
        this.$message.error('catch 获取文章生成策略失败，请重试')
      })
    },
    AiInput() {
      if (this.radio === '' || !this.AIProduceStrategy[this.radio].strategy) {
        this.$message.error('请选择生成方式')
        return
      }
      if (!this.originalText) {
        this.$message.error('请输入原文内容')
        return
      }
      const requestOptions = {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          strategy: this.AIProduceStrategy[this.radio].strategy,
          content: this.originalText
        })
      }
      this.isLoading = true
      fetch(API_URL, requestOptions)
        .then(response => response.json())
        .then(result => {
          this.isSuccessInAI = true
          this.isLoading = false
          const { code, data, msg } = result
          if (code === 0) {
            this.resData = data
            this.$message.success('AI生成成功')
          } else {
            console.log('msg:', msg)
            this.resData = null
            this.$message.error('AI生成失败，请重试')
          }
        }).catch(() => {
          this.isLoading = false
          this.$message.error('AI生成失败，请重试')
        })
    },
    linkToAddStrategy() {
      this.$router.push('/strategy')
    }
  }
}
</script>

<style lang="scss" scoped>
.editor {
  padding: 10px 50px;
  box-sizing: border-box;
}
.score {
  display: inline-block;
}
.module {
  margin-bottom: 15px;
}
.submit {
  float: right;
}
.btn {
  overflow: hidden;
  margin-bottom: 30px;
}
.add-strategy {
  padding: 6px 15px;
}
</style>
