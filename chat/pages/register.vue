<template>
  <el-main>
    <el-card>
      <div class="register_title">
        <p>アカウント登録</p>
      </div>
      <div @click="selectImage">
        <el-avatar v-if="form.imageUrl.val" :class="{ 'dander': form.imageUrl.errorMessage }" class="photo" shape="square" :size="200" :src="form.imageUrl.val"></el-avatar>
        <el-avatar v-else class="photo" shape="square" :size="200" :src="squareUrl"></el-avatar>
        <span v-show="form.imageUrl.errorMessage" class="image_error">{{
        form.imageUrl.errorMessage
        }}</span>
      </div>
      <el-input :class="{'danger': form.name.errorMessage}" placeholder="お名前" v-model="form.name.val" clearable size="medium"></el-input>
      <span v-show="form.name.errorMessage" class="name_error">{{
      form.name.errorMessage
      }}</span>
      <input ref="image" type="file" accept="image/*" @change="onSelectFile" />
      <el-button type="info" class="register_button" @click="onSubmit()">登録</el-button>
    </el-card>
  </el-main>
</template>

<script>
import { mapMutations } from 'vuex'

  export default {
    data () {
      return {
        squareUrl: "https://cube.elemecdn.com/9/c2/f0ee8a3c7c9638a54940382568c9dpng.png",
        sizeList: ["large", "medium", "small"],
        form: {
          name: {
            label: 'お名前',
            val: null,
            errorMessage: null
          },
          imageUrl: {
            label: 'アイコン画像',
            val: null,
            errorMessage: null
          }
        }
      }
    },
    computed: {
      isValidateError() {
        return this.form.name.errorMessage || this.form.imageUrl.errorMessage
      }
    },
    methods: {
      ...mapMutations('alert', ['setMessage']),

      selectImage() {
        this.$refs.image.click()
      },
      onSelectFile(e) {
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

        // // ストレージオブジェクト作成
        const storageRef = this.$fireStorage.ref()

        // ファイルのパスを設定
        const imageRef = storageRef.child(
          `images/${user.uid}/${localImageFile.name}`
        )

        // ファイルを適用してファイルアップロード開始
        const snapShot = await imageRef.put(localImageFile)
        this.form.imageUrl.val = await snapShot.ref.getDownloadURL()

        this.validateImageUrl()
      },
      validateName() {
        const { name } = this.form
        const maxLength = 8

        if (!name.val) {
          name.errorMessage = `↑${name.label}は必須入力項目です`
          return
        }

        if (name.val.length > maxLength) {
          name.errorMessage = `↑${name.label}は${maxLength}文字以内です。`
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
        const user = await this.$auth()

        // 未ログインの場合
        if (!user) this.$router.push('/login')

        this.validateName()
        this.validateImageUrl()

        if (this.isValidateError) return
        try {
          await this.$firestore
            .collection("users")
            .doc(user.uid)
            .set({
              name: this.form.name.val,
              iconImageUrl: this.form.imageUrl.val
            })
          this.$router.push('/')
        } catch (e) {
          this.setMessage({ message: '登録に失敗しました。' })
        }
      }
    }
  }
</script>

<style scoped>
.register_title {
  width: 100%;
  height: 10%;
}

p {
  border-bottom: solid 1px #000;
  text-align: center;
  font-size: 20px;
}

.photo {
  display: block;
  margin: 0 auto;
  margin-bottom: 30px;
  cursor: pointer;
}

.image_error {
  display: block;
  text-align: center;
  color: red;
  font-size: 14px;
}

input {
  display: none;
}

.name_error {
  display: block;
  text-align: center;
  color: red;
  font-size: 14px;
  margin-bottom: 20px;
}

.el-main {
  height: calc(100vh - 80px);
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0;
}

.el-card {
  width: 50%;
  height: 60%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.el-input {
  margin-bottom: 30px;
  margin-top: 20px;
}

.register_button {
  display: block;
  margin: 0 auto;
}
</style>
