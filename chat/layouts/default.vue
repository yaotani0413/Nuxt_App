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

</style>
