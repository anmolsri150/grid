<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Chatbot :options="options" :meta="meta" @submit="onSubmit" id="hello"/>
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
        topBottom : 'rgba(58, 58, 58, 0.2)',
        glassMorphed: true,
        userColor:'grey',
        inputRadius: "1" ,
        buttonShape:'rounded',
      },
      meta: [
        {
          type: 'text',
          name: 'name',
          label: 'Name',
          text: 'What is your name?',
          id: 0,
          next: 1,
        },
        {
          type: 'single',
          name: 'geek',
          label: 'Oppai',
          values: [
            {
              key: 'Yes',
              value: 'yes'
            },
            {
              key: 'No',
              value: 'no'
            },
            {
              key: 'Yes2',
              value: 'yes2'
            },
            {
              key: 'No2',
              value: 'no2'
            },
            {
              key: 'Yes22',
              value: 'yes22'
            },
            {
              key: 'No22',
              value: 'no22'
            },
          ],
          callbacks: {
            getText: function (tt) {
              console.log(tt)
              // eslint-disable-next-line no-unused-vars
              return new Promise(function(resolve, reject) {
                resolve('TEST QUESTIOn');
              });
            }
          },
          id: 1,
          next: 2,
        },
        {
          type: 'text',
          name: 'address',
          label: 'Address',
          text: 'Please enter your address?',
          id: 2,
          next: 3,
        },
        {
          type: 'text',
          name: 'address',
          label: 'Address',
          text: 'Please enter your address?',
          id: 3,
          next: 4,
        },
        {
          type: 'text',
          name: 'address',
          label: 'Address',
          text: 'Please enter your address?',
          id: 4,
          next: null,
        },
      ]
    }
  },
  methods: {
    onSubmit(val) {
      alert(JSON.stringify(val))
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
