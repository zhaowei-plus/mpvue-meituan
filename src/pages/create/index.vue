<template>
  <div class="create">
    <van-tabs active="a">
      <van-tab title="标签 1" name="a">内容 1</van-tab>
      <van-tab title="标签 2" name="b">内容 2</van-tab>
      <van-tab title="标签 3" name="c">内容 3</van-tab>
    </van-tabs>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        active: 1
      }
    },
    methods: {
      onChange(event) {
        wx.showToast({
          title: `切换到标签 ${event.detail.name}`,
          icon: 'none'
        });
      }
    }
  }
</script>

<style scoped>
  page {
    width: 100%;
    height: 100%;
    border: 1px solid red;
  }
  .create {
    border: 1px solid red;
  }

</style>
