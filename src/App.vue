
<template>
  <AppHeader></AppHeader>
  <AppMain/>
  <AppFooter/>
</template>

<script>

import AppHeader from './components/AppHeader.vue'
import AppMain from './components/AppMain.vue'
import AppFooter from './components/AppFooter.vue'

  export default {
    components: {
      AppHeader,
      AppMain,
      AppFooter
    },
  }
</script>

