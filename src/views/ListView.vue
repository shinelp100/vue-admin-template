<template>
  <div id="app">
    <!-- <div class="nav">
      <mt-button :class="selectedIndex === 1 ? 'selected': ''" @click.native.prevent="toggle(1)">全部</mt-button>
      <mt-button :class="selectedIndex === 2 ? 'selected': ''" @click.native.prevent="toggle(2)">特斯拉</mt-button>
      <mt-button :class="selectedIndex === 3 ? 'selected': ''" @click.native.prevent="toggle(3)">微软</mt-button>
    </div>
    <div class="conditions">
      <mt-button size="small">全部</mt-button>
      <mt-button size="small">分析</mt-button>
      <mt-button size="small">消息</mt-button>
    </div>
    <div class="conditions">
      <mt-button size="small">全部</mt-button>
      <mt-button size="small">Cybertruck</mt-button>
      <mt-button size="small">市场竞争</mt-button>
      <mt-button size="small">销量</mt-button>
    </div> -->
    <mt-tab-container v-model="active" swipeable>
      <mt-tab-container-item id="tab-container1">
        <div class="item">
          <h3 class="title">特斯拉:Cybertruck 改变一切 <span>看多</span></h3>
          <p class="avatar"><img class="img" src="" alt="avatar">作者: Victor Dergunov<span class="time">今天</span></p>
          <p class="introduce">Tesla的Cybertruck是一场游戏改变者，因为它为特斯拉在整个汽车行业中获取更多市场份额打开了另个巨大市场。</p>
          <p class="detail">特斯拉的电动皮卡Cybertruck可能成为公司创造巨大收入和利润的关键。其独特设计和先进功能使其在皮卡市场脱颖而出，具有潜在的竞争力和市场吸引力。Cybertruck以具有竞争性的价格和卓越性能脱颖而出，有望占据皮卡市场的重要份额，为特斯拉每年带来200-300亿美元的销售额</p>
        </div>
      </mt-tab-container-item>
      <mt-tab-container-item id="tab-container2">
        <mt-cell v-for="n in 5" :key="n" title="tab-container 2" />
      </mt-tab-container-item>
      <mt-tab-container-item id="tab-container3">
        <mt-cell v-for="n in 7" :key="n" title="tab-container 3" />
      </mt-tab-container-item>
    </mt-tab-container>
  </div>
</template>

<script>
// const API_URL = "http://localhost:4000/api/index";

export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      messages: 'shinelp100',
      selectedIndex: 1,
      active: 'tab-container1'
    }
  },
  mounted() {
    // fetch(API_URL)
    //   .then(response => response.json())
    //   .then(result => {
    //     this.messages = result.messages;
    //   });
  },
  methods: {
    toggle(index) {
      if (this.selectedIndex === index) return
      this.selectedIndex = index
      this.active = 'tab-container' + index
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  .selected {
    background-color: rgb(15, 124, 233);
    color: white;
  }
  .item {
    color: #999;
    padding: 10px 15px;
    text-align: left;
    .title {
      color: #3b69f6;
      font-size: 18px;
      line-height: 24px;
      position: relative;
      span {
        // position: absolute;
        // right: 0;
        // top: 5px;
        display: inline-block;
        background: #719f2f;
        line-height: 20px;
        font-size: 14px;
        width: 40px;
        text-align: center;
        border-radius: 2px;
        color: white;
        box-sizing: border-box;
        margin-left: 5px;
      }
    }
    .avatar {
      margin: 5px 0;
      overflow: hidden;
      .time {
        float: right;
      }
    }
    .introduce {
      color: #666;
      font-size: 14px;
      line-height: 18px;
      margin: 5px 0;
    }
    .detail {
      color: #333;
      font-size: 18px;
      line-height: 24px;
    }
  }
}
</style>
