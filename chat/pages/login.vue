<template>
  <el-main>
    <el-card>
      <div class="login_title">
        <p>ログイン</p>
      </div>
        <el-button type="danger" @click="onSubmit()" class="login_button">
          Google でログイン
        </el-button>
    </el-card>
  </el-main>
</template>

<script>
export default {
  middleware: ['checkLogin'],
  methods: {
    onSubmit() {
      const provider = new this.$firebase.auth.GoogleAuthProvider()
      this.$fireAuth.signInWithRedirect(provider)
    }
  }
}
</script>

<style scoped>
.el-main {
  padding: 0;
}

.login_title {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 80px;
  border-bottom: double 1px black;
}

p  {
  font-size: 20px;
}

.login_button {
  color: #FFF;
}

.el-main {
  height: calc(100vh - 80px);
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
}

.el-card {
  width: 50%;
  height: 60%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.el-button {
  width: 350px;
  height: 60px;
}
</style>
