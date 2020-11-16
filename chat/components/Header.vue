<template>
  <el-container>
    <el-header>
      <div v-if="isChatPage" class="back">
        <nuxt-link to="/">
          <i class="el-icon-back"></i>
        </nuxt-link>
      </div>
      <h1>
      チャット アプリ
      </h1>
    </el-header>
  </el-container>
</template>

<script>
export default {
  computed: {
    isChatPage() {
      return this.$route.path.match(/\/rooms\/[A-Za-z0-9]*/)
    }
  }
}
</script>

<style scoped>
.back {
  width: 20px;
  height: 20px;
}

.el-container {
  background-color: #1E90FF;
  height: 80px;
  width: 100%;
  position: fixed;
  top: 0;
  line-height: 80px;
}

.el-header {
  padding: 0;
  margin: 0;
  text-align: center;
  font-size: 30px;
  color: #FFF;
}
</style>
