<template>
  <div id="app">
    <uploadCustom v-model="queryVal3" clearable></uploadCustom>
  </div>
</template>

<script>
import uploadCustom from './iview/upload/uploadCustom'

export default {
  name: 'app',
  components: {
    uploadCustom
  },
  data () {
    return {
      queryVal3: ''
    }
  },
  methods: {
    selectChange (multipleShowVal, multipleHideVal) {
      console.log(multipleShowVal, multipleHideVal)
    }
  }
}
</script>

<style lang="scss">
</style>
