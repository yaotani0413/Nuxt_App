<template>
  <div>
    <Header />
    <Alert />
    <Nuxt />
  </div>
</template>

<script>
import Header from '~/components/Header.vue'
import Alert from '~/components/Alert.vue'

export default {
  components: {
    Header,
    Alert
  }
}
</script>

<style>
html {
  font-family: "San Francisco UI",Helvetica,"PingFang SC","Hiragino Sans GB","Microsoft YaHei","微软雅黑",Arial,sans-serif;
}
</style>
