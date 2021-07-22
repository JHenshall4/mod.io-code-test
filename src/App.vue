<script setup>
import loginForm from './components/loginForm.vue'
import gamesGrid from './components/gamesGrid.vue'

var gridEnabled = true

// This starter template is using Vue 3 experimental <script setup> SFCs
// Check out https://github.com/vuejs/rfcs/blob/master/active-rfcs/0040-script-setup.md

</script>


<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <loginForm/>
  <gamesGrid v-if="gridEnabled==true"/>
</template>



<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
