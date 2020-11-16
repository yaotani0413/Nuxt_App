<template>
  <div class="chat_main">
    <div class="chat_section">
    </div>
    <div class="form">
      <el-input
      type="textarea"
      placeholder="発言しよう"
      v-model="form.message.val"
      name="form.body"
      >
      </el-input>
      <el-button
      :disable="isValidateError"
      @click="onSubmit"
      >
      </el-button>
    </div>
  </div>
</template>

<script>
export default {
  middleware: ['checkAuth'],
  data() {
    return {
      form: {
        message: {
          val: null
        }
      }
    }
  },

  computed: {
    isValidateError() {
      return !this.form.message.val
    }
  },

  methods: {
    async onSubmit() {
      if (this.isValidateError) return

      const user = await this.$user()

      // 未ログインの場合
      if (!user) this.$router.push('/login')
      const roomId = this.$route.params.id

      // 登録データを準備
      const chat = {
        userId: user.uid,
        name: user.name,
        iconImageUrl: user.iconImageUrl,
        body: this.form.message.val,
        createdAt: this.$firebase.firestore.FieldValue.serverTimestamp()
      }

      try {
        await this.$firestore
          .collection('rooms')
          .doc(roomId)
          .collection('chats')
          .add(chat)
        this.resetForm()
        this.scrollBottom()
      } catch (e) {
        this.setMessage({ message: '登録に失敗しました。' })
      }
    },

    scrollBottom() {
      const element = document.documentElement
      const bottom = element.scrollHeight - element.clientHeight
      window.scroll(0, bottom)
    },

    resetForm() {
      this.form.message.val = null
    }
  }
}
</script>

<style scoped>
.chat_section {
  height: calc(100vh - 80px);
  width: 100%;
  background-color: blueviolet;
}

.form {
  display: flex;
  position: fixed;
  bottom: 10px;
  align-items: center;
  justify-content: center;
}
</style>
