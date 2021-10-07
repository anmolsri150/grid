<template>
  <div id="app">
    <Chatbot :options="options" :meta="meta" :finalMessage="finalMessage" @submit="onSubmit" id="hello"/>
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
        topBottom : 'rgba(255,218,185, 1)',
        // topBottom : 'rgba(255,218,185, 0.3)',
        // glassMorphed: true,
        userColor: '#047BD5',
        inputRadius: "1",
        // button: 'pill',
        button: 'primary',
      },
      finalMessage: function(formData) {
        console.log(formData)
        // eslint-disable-next-line no-unused-vars
        return new Promise(function(resolve, reject) {
          resolve("Alright!, Your Appointment have been scheduled on " + formData.date + " at " + formData.time + " in the " + formData.department + " Department");
        });
      },
      meta: [
        {
          type: 'text',
          name: 'name',
          label: 'Name',
          text: 'Hey! What do you like to be called?',
          id: 0,
          next: 1,
        },
        {
          type: 'text',
          name: 'address',
          label: 'Address',
          callbacks: {
            getText: function(formData) {
              // eslint-disable-next-line no-unused-vars
              return new Promise(function(resolve, reject) {
                resolve("Hey " + formData.name + "!, Where do you live at?");
              });
            }
          },
          id: 1,
          next: 2,
        },
        {
          type: 'datepicker',
          name: 'appointment_time',
          label: 'Appointment Date',
          id: 2,
          next: 3,
        },
        {
          type: 'single',
          name: 'department',
          label: 'Department',
          text: 'Which department do you want your appointment to be booked?',
          values: [
            {
              key: 'cardiology',
              value: 'Cardiology'
            },
            {
              key: 'gastro',
              value: 'Gastro'
            },
            {
              key: 'ent',
              value: 'ENT'
            },
            {
              key: 'medicine',
              value: 'Medicine'
            },
          ],
          id: 3,
          next: 4,
        },
        {
          type: 'upload',
          name: 'prescription',
          label: 'Previous Prescription',
          callbacks: {
            uploadFile: function () {
              // FIle upload logic
              return new Promise(function (resolve) {
                resolve(true)
              })
            }
          },
          id: 4,
          next: 5,
        },
        {
          type: 'single',
          name: 'appointment',
          label: 'Appointment Time',
          text: 'What time do you want the Appointment?',
          callbacks: {
            getValues: function() {
              // eslint-disable-next-line no-unused-vars
              return new Promise(function(resolve, reject) {
                let values = [
                  {
                    key: '09:00',
                    value: '09:00'
                  },
                  {
                    key: '10:00',
                    value: '10:00'
                  },
                  {
                    key: '11:00',
                    value: '11:00'
                  },
                  {
                    key: '12:00',
                    value: '12:00'
                  },
                ]
                resolve(values);
              });
            }
          },
          id: 5,
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
