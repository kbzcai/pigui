<template>
  <el-config-provider :locale="locale">
    <router-view  v-if="isReload" />
  </el-config-provider>

</template>

<script>
import { messages } from '@/lang/';
export default {
  computed: {
    locale () {
      let languageType = this.$store.getters.language
      return messages[languageType]
    }
  },
  data() {
    return {
      isReload: true
    }
  },
  watch: {},
  created() {
  },
  methods: {
    reload() {
      this.isReload = false
      this.$nextTick(() => {
        this.isReload = true
      })
    }
  },
  provide() {
    return {
      reload: this.reload
    }
  }
} 
</script>

<style>
html,
body,
#app {
  width: 100%;
  height: 100%;
}
</style>
