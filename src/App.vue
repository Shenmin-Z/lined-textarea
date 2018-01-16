<template>
  <div id="app"
       class="container"
  >
    <lined-textarea></lined-textarea>
  </div>
</template>

<script>
import LinedTextarea from './LinedTextarea.vue'

import './reset.css'

export default {
  name: 'app',
  components: {
    LinedTextarea
  },
  data () {
    return {
    }
  }
}
</script>

<style lang="scss">
.container {
  width: 210px;
  margin: 50px auto;
}
</style>
