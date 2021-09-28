<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Chatbot :top-bottom="options.topBottom" />
  </div>
</template>

<script>
import Chatbot from "@/components/chatbot";

export default {
  name: 'App',
  components: {
    Chatbot
  },
  data(){
    return{
      options: {
        topBottom : 'black',
      },

    }
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
