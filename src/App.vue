<template>
  <div id="app"
       class="container"
  >
    <div class="a">
      <lined-textarea :validate="email"></lined-textarea>
    </div>
    <br>
    <div class="b">
      <lined-textarea :validate="telephone"></lined-textarea>
    </div>
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
      email: (email) => /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/i.test(email),
      telephone: (telephone) => /^[\+]?[(]?[0-9]{3}[)]?[-\s\.]?[0-9]{3}[-\s\.]?[0-9]{4,6}$/im.test(telephone)
    }
  }
}
</script>

<style lang="scss">
.a {
  width: 210px;
  margin: 50px auto;
}

.b {
  width: 500px;
  margin: 50px auto;
}
</style>
