<template>
  <div class="strategy">
    <el-collapse v-model="activeNames">
      <el-collapse-item v-for="item in AIProduceStrategy" :key="item.strategy" :title="item.name" :name="item.strategy">
        <el-input v-model="item.name" class="mb20" placeholder="策略名称" clearable />
        <el-input v-model="item.strategy" class="mb20" placeholder="策略key" :disabled="true" />
        <el-input v-model="item.description" class="mb20" type="textarea" autosize placeholder="策略描述" clearable />
        <el-input v-model="item.JSON" class="mb20" type="textarea" autosize placeholder="策略输出" clearable />
        <el-button type="primary" @click="modifyStrategy(item)">修改</el-button>
        <el-button type="danger" @click="deleteStrategy(item)">删除</el-button>
      </el-collapse-item>
      <el-collapse-item name="add">
        <template slot="title">
          <span class="add">
            新增策略 &nbsp;<i class="header-icon el-icon-circle-plus-outline" />
          </span>
        </template>
        <el-input v-model="name" class="mb20" placeholder="策略名称" clearable />
        <el-input v-model="strategy" class="mb20" placeholder="策略key" clearable />
        <el-input v-model="description" class="mb20" type="textarea" autosize placeholder="策略描述" clearable />
        <el-input v-model="JSONData" class="mb20" type="textarea" autosize placeholder="策略输出" clearable />
        <el-button type="primary" :loading="isLoading" @click="addStrategy">新增</el-button>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script>
const Strategy_Add = 'http://82.157.175.33:4000/api/strategy/add'
const Strategy_Modify = 'http://82.157.175.33:4000/api/strategy/modify'
const Strategy_Delete = 'http://82.157.175.33:4000/api/strategy/delete'
const Strategy_List = 'http://82.157.175.33:4000/api/strategy/list'

export default {
  name: 'StrategyView',
  data() {
    return {
      activeNames: 'add',
      AIProduceStrategy: [],
      name: '',
      strategy: '',
      description: '',
      JSONData: '',
      isLoading: false
    }
  },
  mounted() {
    this.getStrategyList()
  },
  methods: {
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
    modifyStrategy(item) {
      fetch(Strategy_Modify, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(item)
      }).then(response => response.json()).then(result => {
        const { code, data, msg } = result
        if (code === 0 && data) {
          this.$message.success('策略修改成功')
        } else {
          console.error('msg:', msg)
          this.$message.error('策略修改失败，请重试')
        }
      }).catch(() => {
        this.$message.error('catch 策略修改失败，请重试')
      })
    },
    addStrategy() {
      if (!this.name || !this.strategy || !this.description || !this.JSONData) {
        this.$message.error('请填写完整')
        return
      }
      const body = {
        name: this.name,
        strategy: this.strategy,
        description: this.description,
        JSON: this.JSONData
      }
      this.dialogVisible = false
      this.isLoading = true
      fetch(Strategy_Add, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(body)
      }).then(response => response.json()).then(result => {
        this.isLoading = false
        const { code, data, msg } = result
        if (code === 0 && data) {
          this.$message.success('策略录入成功')
          setTimeout(() => {
            this.getStrategyList()
          }, 1000)
        } else {
          console.error('msg:', msg)
          this.$message.error(msg || '策略录入失败，请重试')
        }
      }).catch(() => {
        this.isLoading = false
        this.$message.error('catch 策略录入失败，请重试')
      })
    },
    deleteStrategy(item) {
      fetch(Strategy_Delete, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(item)
      }).then(response => response.json()).then(result => {
        const { code, data, msg } = result
        if (code === 0 && data) {
          this.$message.success('策略删除成功')
          setTimeout(() => {
            this.getStrategyList()
          }, 1000)
        } else {
          console.error('msg:', msg)
          this.$message.error('策略删除失败，请重试')
        }
      }).catch(() => {
        this.$message.error('catch 策略删除失败，请重试')
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.strategy {
  width: 1200px;
  margin: 0 auto;
  // height: 100%;
  background-color: #fff;
  padding: 20px 0;
  box-sizing: border-box;
  .add-strategy {
    margin-top: 20px;
  }
  .add {
    color: rgb(37, 151, 238);
    font-size: 14px;
    .header-icon {
      font-size: 16px;
    }
  }
}
.mb20 {
  margin-bottom: 20px;
}
</style>
