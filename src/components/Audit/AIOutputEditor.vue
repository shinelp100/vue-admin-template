<template>
  <div class="ai-output">
    <strong class="bold">标题：</strong>
    <el-input v-model="title" class="module" placeholder="ai输出标题" clearable />
    <br>
    <strong class="bold">摘要：</strong>
    <el-input v-model="content" class="module" rows="4" type="textarea" autosize placeholder="ai输出内容" clearable />
    <template v-if="outlineStr">
      <br>
      <strong class="bold">提纲：</strong>
      <el-input v-model="outlineStr" class="module" rows="4" type="textarea" autosize placeholder="ai输出摘要" clearable />
    </template>
    <br>
    <strong class="bold">一段话原文：</strong>
    <el-input v-model="original" class="module" placeholder="ai输出一句话原文" clearable />
    <template v-if="translation">
      <br>
      <strong class="bold">一段话译文：</strong>
      <el-input v-model="translation" class="module" placeholder="ai输出一句话译文" clearable />
    </template>
    <div class="tags">
      <strong class="bold">标签：</strong>
      <el-input v-for="(item, index) in tags" :key="item" v-model="tags[index]" class="tag-input module" placeholder="ai输出标签">
        <i slot="suffix" class="el-icon-close el-input__icon" @click="deleteTag('tags' ,item)" />
      </el-input>
      <el-button icon="el-icon-plus" @click="addTag('tags')" />
    </div>
    <div v-if="!$parent.articleInfo.Site.SiteMeta.MetaValue" class="tags">
      <strong class="bold">股票：</strong>
      <el-input v-for="(item, index) in stock" :key="item" v-model="stock[index]" class="tag-input module" placeholder="ai输出股票">
        <i slot="suffix" class="el-icon-close el-input__icon" @click="deleteTag('stock', item)" />
      </el-input>
      <el-button icon="el-icon-plus" @click="addTag('stock')" />
    </div>
    <div class="type-item module mt25">
      <span class="demonstration">买卖建议：</span>
      <el-radio v-for="(item, index) in TradingProposalList" :key="item.id" v-model="tradingProposal" :label="index">{{ item.label }}</el-radio>
    </div>
    <div class="btn">
      <el-button class="mr10" type="primary" :loading="isLoading" @click="AiInput"> {{ isLoading ? '提交中...' : '提交' }}</el-button>
      <el-button class="mr10" type="primary" :loading="$parent.isLoading" @click="Regenerate"> {{ $parent.isLoading ? '重新生成中...' : '重新生成' }}</el-button>
      <el-popconfirm :title="$parent.articleInfo.Title" confirm-button-text="好的" cancel-button-text="不用了" icon="el-icon-info" @confirm="handleDelete">
        <el-button slot="reference" type="danger">删除</el-button>
      </el-popconfirm>
    </div>
  </div>
</template>

<script>
import { EventBus } from '@/utils/event-bus'
const API_URL = 'http://82.157.175.33:4000/api/SaveArticleSummary'

export default {
  name: 'AIOutputEditor',
  props: {
    res: Object
  },
  data() {
    return {
      TradingProposalList: [
        { id: 1, label: '强烈卖出' },
        { id: 2, label: '卖出' },
        { id: 3, label: '中性' },
        { id: 5, label: '买入' },
        { id: 4, label: '强烈买入' },
        { id: 0, label: '无' }
      ],
      tradingProposal: 5,
      title: this.res?.title || '',
      content: this.res?.content_summary || '',
      outlineStr: this.dealWithOutline(this.res?.outline) || '',
      original: this.res.key_sentence?.original || '',
      translation: this.res.key_sentence?.translation || '',
      tags: this.res?.tags || [],
      stock: this.res?.stock || [],
      isLoading: false,
      isGenerate: false
    }
  },
  watch: {
    res: {
      handler: function(val) {
        if (val) {
          this.title = val.title
          this.content = val.content_summary
          this.outlineStr = this.dealWithOutline(this.res?.outline)
          this.original = val.key_sentence?.original
          this.translation = val.key_sentence?.translation
          this.tags = val.tags
          this.stock = val.stock
        } else {
          this.title = ''
          this.content = ''
          this.outlineStr = ''
          this.original = ''
          this.translation = ''
          this.tags = []
        }
      },
      deep: true
    }
  },
  methods: {
    deleteTag(type, item) {
      if (type === 'stock') {
        const index = this.stock.indexOf(item)
        this.stock.splice(index, 1)
        return
      }
      const index = this.tags.indexOf(item)
      this.tags.splice(index, 1)
    },
    addTag(type) {
      if (type === 'stock') {
        this.stock.push('')
        return
      }
      this.tags.push('')
    },
    // 修改提纲后替换成统一格式后.replace(/\n{2,99}/gi, '\n\n')进行字符串分割
    dealWithOutline(dataArr) {
      let outlineStr = ''
      if (!dataArr || !dataArr.length) return ''
      for (const data of dataArr) {
        outlineStr += `<H1>${data.heading}</H1>\n<C1>${data.content}</C1>\n\n`
      }
      return outlineStr.slice(0, -2)
    },
    AiInput() {
      if (!this.title || !this.content) {
        this.$message.error('请填写完整信息')
        return
      }
      const body = {
        ArticleID: this.$parent.articleInfo?.ID,
        Content: this.$parent?.originalText,
        Title: this.title,
        Summary: this.content,
        Outline: this.outlineStr,
        ContentSummary: {
          Original: this.original,
          Translation: this.translation
        },
        tags: this.tags.filter(_ => _),
        stock: this.stock.filter(_ => _),
        TradingProposal: this.TradingProposalList?.[this.tradingProposal]?.id
      }
      this.isLoading = true
      fetch(API_URL, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(body)
      }).then(response => response.json()).then(result => {
        this.isSuccessInAI = true
        this.isLoading = false
        const { code, data, msg } = result
        if (code === 0 && data) {
          this.$message.success('数据入库成功')
        } else {
          console.error('msg:', msg)
          this.$message.error('数据入库失败，请重试')
        }
      }).catch(() => {
        this.isLoading = false
        this.$message.error('数据入库失败，请重试')
      })
    },
    Regenerate() {
      EventBus.$emit('ArticleRegenerateSummary', this.$parent.articleInfo)
    },
    handleDelete() {
      EventBus.$emit('ArticleItemDelete', this.$parent.articleInfo)
    }
  }
}
</script>
<style>
.el-popover {
  max-width: 50px;
}
.el-popconfirm__action {
  margin: 5px 0 0;
}
</style>
<style lang="scss" scoped>
.module {
  margin-bottom: 25px;
  margin-top: 10px;
}
.tag {
  margin-right: 10px;
}
.btn {
  overflow: hidden;
  margin-bottom: 20px;
  display: flex;
  justify-content: end;
  align-content: center;
}
.tag-input {
  width: 120px;
  margin-right: 10px;
}
.el-icon-close {
  cursor: pointer;
}
.bold {
  font-weight: bold;
}
.mr10 {
  margin-right: 10px;
}
.mt25 {
  margin-top: 25px;
}
</style>
