<template>
  <transition appear>
    <div class="modal_base">
      <slot />
      <el-button @click="close" class="el-icon-close" circle></el-button>
    </div>
  </transition>
</template>

<script>
export default {
  methods: {
    close() {
      this.$emit("closeModal")
    }
  }
}
</script>


<style scoped>
.modal_base {
  height: 50%;
  width: 50%;
}

.el-button {
  display: block;
  margin: 0 auto;
  margin-top: 30px;
}

/* .v-leave-active,
.v-enter-active {
  transition: opacity 0.4s ease-out;
}

.v-enter,
.v-leave-to {
  opacity: 0;
}

.v-enter-to,
.vleave {
  opacity: 1;
} */
</style>
