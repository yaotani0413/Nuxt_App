<template>
  <el-card>
    <form>
      <div class="icon_container" @click="selectImage">
        <el-avatar
          v-if="form.imageUrl.val"
          :size="200"
          :src="form.imageUrl.val"
          :class="{ 'danger': form.imageUrl.errorMessage }"
          shape="square"
          class="image"
          >
        </el-avatar>
        <el-avatar v-else
          :size="200"
          shape="square"
          class="no_image"
          >
        </el-avatar>
        <input
            ref="image"
            @change="onFileSelect"
            type="file"
            style="display: none"
            accept="image/*"
          />
        <span
          v-show="form.imageUrl.errorMessage"
          class="image_error"
        >
          {{ form.imageUrl.errorMessage }}
        </span>
      </div>
      <div>
        <el-input
          type="text"
          placeholder="ルーム名"
          v-model="form.name.val"
          @blur="validateName"
          :class="{ 'danger': form.name.errorMessage }"
          size="small"
          maxlength="mexLength"
          >
          </el-input>
          <span
          v-show="form.name.errorMessage"
          class="name_error"
          >
          {{ form.name.errorMessage }}
        </span>
      </div>
      <div class="button_container">
        <el-button @click="onSubmit">作成</el-button>
      </div>
    </form>
  </el-card>
</template>

<script>
import { mapMutations } from 'vuex'

export default {
  data() {
    return {
      input: '',
      form: {
        name: {
          label: "名前",
          val: null,
          errorMessage: null
        },
        imageUrl: {
          label: "画像",
          val: null,
          errorMessage: null
        }
      }
    }
  },
  computed: {
    isValidateError() {
      return this.form.name.errorMessage || this.form.imageUrl.errorMessage
    },
    maxLength() {
      return 35
    }
  },
  methods: {
    ...mapMutations('alert', ['setMessage']),

    selectImage() {
      this.$refs.image.click()
    },

    onFileSelect(e) {
      const files = e.target.files
      if (files.length === 0) return

      const reader = new FileReader()
      reader.readAsDataURL(files[0])

      reader.addEventListener('load', () => {
        this.upload({
          localImageFile: files[0]
        })
      })
    },

    async upload({ localImageFile }) {
      const user = await this.$auth()

      // 未ログインの場合
      if (!user) this.$router.push('/login')

      // ストレージオブジェクト作成
      const storageRef = this.$fireStorage.ref()

      // ファイルのパスを設定
      const imageRef = storageRef.child(
        `images/${user.uid}/rooms/${localImageFile.name}`
      )

      // ファイルを適用してファイルアップロード開始
      const snapShot = await imageRef.put(localImageFile)
      this.form.imageUrl.val = await snapShot.ref.getDownloadURL()

      this.validateImageUrl()
    },

    validateName() {
      const { name } = this.form

      if (!name.val) {
        name.errorMessage = `↑${name.label}は必須入力項目です`
        return
      }

      if (name.val.length > this.maxLength) {
        name.errorMessage = `${name.label}は${this.maxLength}文字以内です。`
        return
      }

      name.errorMessage = null
    },

    validateImageUrl() {
      const { imageUrl } = this.form

      if (!imageUrl.val) {
        imageUrl.errorMessage = `↑${imageUrl.label}は必須入力項目です`
        return
      }

      imageUrl.errorMessage = null
    },

    async onSubmit() {
      // 認証チェック
      const user = this.$auth.currentUser
      if (!user) this.$router.push('/login')

      // 入力値チェック
      this.validateName()
      this.validateImageUrl()
      if (this.isValidateError) return

      // 登録データを準備
      const params = {
        name: this.form.name.val,
        topImageUrl: this.form.imageUrl.val,
        createdAt: this.$firebase.firestore.FieldValue.serverTimestamp()
      }

      try {
        await this.$firestore.collection('rooms').add(params)
        this.$emit('closeModal')
      } catch (e) {
        this.setMessage({ message: '登録に失敗しました。' })
      }
    }
  }
}
</script>

<style scoped>
.el-card {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.el-avatar {
  display: block;
  margin: 0 auto;
}

.image {
  margin-bottom: 40px;
}

.no_image {
  margin-bottom: 40px;
}

.image_error {
  display: block;
  text-align: center;
  color: red;
  font-size: 14px;
  margin-bottom: 40px;
}

.name_error {
  display: block;
  text-align: center;
  color: red;
  font-size: 14px;
  margin-bottom: 20px;
}

.el-button {
  display: block;
  margin: 0 auto;
  margin-top: 40px;
  background-color: #1E90FF;
  color: #fff;
}
</style>
