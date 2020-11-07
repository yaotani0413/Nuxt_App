<template>
  <el-card v-show="message">
    <div>
      <span>エラー</span>
    </div>
    <el-button class="el-icon-close" @click="deleteMessage()"></el-button>
    <p>{{ message }}</p>
  </el-card>
</template>

<script>
import { mapGetters, mapMutations } from 'vuex'

export default {
  computed: {
    ...mapGetters('alert', ['message'])
  },

  methods: {
    ...mapMutations('alert', ['deleteMessage'])
  }
}
</script>
