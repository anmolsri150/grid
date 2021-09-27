<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Chatbot />
  </div>
</template>

<script>
import Chatbot from "@/components/chatbot";

export default {
  name: 'App',
  components: {
    Chatbot
  }
}
</script>

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
