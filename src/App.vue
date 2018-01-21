<template>
  <div id="app"
       class="container"
  >
    <input type="checkbox" id="disable" v-model="disable">
    <label for="disable">Disable textarea</label>
    <br>
    Width<input type="text" v-model="width">px
    <br>
    Height<input type="text" v-model="height">px
    <br>
    Resize:
    <input type="radio" id="fixed" value="none" v-model="resize">
    <label for="fixed">Fixed</label>
    <input type="radio" id="vertical" value="vertical" v-model="resize">
    <label for="vertical">Vertical</label>
    <input type="radio" id="both" value="both" v-model="resize">
    <label for="both">Both</label>
    <br>
    Validate:
    <input type="radio" id="none" value="none" v-model="validateName">
    <label for="none">None</label>
    <input type="radio" id="email" value="email" v-model="validateName">
    <label for="email">Email</label>
    <input type="radio" id="telephone" value="telephone" v-model="validateName">
    <label for="telephone">Telephone Number</label>
    <br>
    Word wrap:
    <input type="radio" id="wrap" value="false" v-model="nowrap">
    <label for="wrap">Wrap</label>
    <input type="radio" id="nowrap" value="true" v-model="nowrap">
    <label for="nowrap">No Wrap</label>
    <br>
    <br>
    <lined-textarea 
     :disabled="disable"
     :nowrap="nowrap === 'true'"
     :validate="validate"
     :styles="{ height: height + 'px', width: width + 'px', resize }"
     v-model="content"
    ></lined-textarea>
  </div>
</template>

<script>
import LinedTextarea from './LinedTextarea.vue'

import './reset.css'

const sammpleInput = `email@exampleexampleexampleexampleexampleexampleexampleexample.com
firstname.lastname@example.com
email@subdomain.exampexampleexampleexampleexampleexamplele.com
firstname+lastname@example.com
not valid
email@example.co.jp
A long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long long input
(123) 456-7890
(123)456-7890
(123)456-xxxx
123-456-7890
123.456.7890`

export default {
  name: 'app',
  components: {
    LinedTextarea
  },
  data() {
    return {
      email: (email) => /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/i.test(email),
      telephone: (telephone) => /^[\+]?[(]?[0-9]{3}[)]?[-\s\.]?[0-9]{3}[-\s\.]?[0-9]{4,6}$/im.test(telephone),

      content: sammpleInput,
      disable: false,
      height: 300,
      nowrap: 'false',
      resize: 'both',
      validateName: 'none',
      width: 450
    }
  },
  computed: {
    validate() {
      switch(this.validateName) {
        case 'email':
          return this.email
        case 'telephone':
          return this.telephone
        default:
          return () => (true)
      }
    }
  }
}
</script>

<style lang="scss">
.container {
  margin: 50px auto auto 50px;
}
</style>
