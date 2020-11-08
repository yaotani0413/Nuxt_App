<template>
  <div class="main">
    <ModalBase v-if="isCreateMode" @closeModal="closeModal" class="modal">
      <CreateRoomModal @closeModal="closeModal" />
    </ModalBase>
    <el-button @click="openModal" type="primary">ルーム作成</el-button>
  </div>
</template>

<script>
import ModalBase from '~/components/ModalBase.vue'
import CreateRoomModal from '~/components/CreateRoomModal.vue'

export default {
  components: {
    ModalBase,
    CreateRoomModal
  },
  data() {
    return {
      isCreateMode: false
    }
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
}

.el-button {
  margin-right: 80px;
  position: fixed;
  bottom: 70px;
  right: 30px;
}

/* .modal {
  position: fixed;
  top: 50%;
  left: 45%;
} */
</style>
