<template>
  <div class="main">
    <div :class="{ none: isCreateMode }">
      <el-card v-for="room in rooms" :key="room.id" class="room" shadow="hover">
        <el-avatar :src="room.topImageUrl">
        </el-avatar>
        <p>{{ room.name }}</p>
      </el-card>
    </div>
    <ModalBase v-if="isCreateMode" @closeModal="closeModal" class="modal">
      <CreateRoomModal @closeModal="closeModal" />
    </ModalBase>
    <el-button @click="openModal"  class="create-btn" :class="{ none: isCreateMode }">ルーム作成</el-button>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import ModalBase from '~/components/ModalBase.vue'
import CreateRoomModal from '~/components/CreateRoomModal.vue'

export default {
  middleware: ['checkAuth'],
  components: {
    ModalBase,
    CreateRoomModal
  },
  data() {
    return {
      isCreateMode: false,
      unsubscribe: null
    }
  },
  computed: {
    ...mapGetters('rooms', ['rooms'])
  },
  async asyncData({ store }) {
    const unsubscribe = await store.dispatch('rooms/subscribe')
    return {
      unsubscribe
    }
  },
  destroyed() {
    this.$store.dispatch('rooms/clear')
    if (this.unsubscribe) this.unsubscribe()
  },
  methods: {
    openModal() {
      this.isCreateMode = true

    },
    closeModal() {
      this.isCreateMode = false
    }
  }
}
</script>

<style scoped>
.main {
  width: 100vw;
  height: calc(100vh - 80px);
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  margin-top: 80px;
}

.none {
  display: none;
}

.room {
  width: 500px;
  display: flex;
  margin-bottom: 20px;
  cursor: pointer;
}

.el-button {
  margin-right: 80px;
  position: fixed;
  bottom: 70px;
  right: 30px;
  background-color: #1E90FF;
  color: #fff;
}

.modal {
  position: absolute;
}
</style>
