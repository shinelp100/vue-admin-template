<template>
  <a class="list-card" :href="CardInfo?.Article?.Url">
    <h3 class="title">{{ CardInfo.Title }}<span>{{ CardInfo.Status === 1 ? '看多': '看空' }}</span></h3>
    <div class="tags"><span v-for="item in CardInfo.tags" :key="item">#{{ item }}</span></div>
    <p class="avatar"><img class="img" :src="CardInfo?.Article?.Author?.Image" alt="avatar">作者: {{ CardInfo?.Article?.Author?.AuthorName }}<span class="time">{{ CardInfo?.Article?.PublishAt | global_filter("YYYY-MM-DD HH:mm") }}</span></p>
    <p class="introduce">{{ CardInfo.Summary }}</p>
    <p class="detail">{{ CardInfo.Outline }}</p>
  </a>
</template>

<script>
export default {
  name: 'ListCard',
  props: {
    CardInfo: Object
  }
}
</script>

<style lang="scss" scoped>
.list-card {
  display: block;
  color: #999;
  padding: 10px 15px;
  text-align: justify;
  text-decoration: none;
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
  .tags {
    margin: 5px 0;
    span {
      padding: 5px;
      color: #4e4d38;
    }
  }
  .avatar {
    margin: 5px 0;
    overflow: hidden;
    .time {
      float: right;
      margin-right: 5px;
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
</style>
