<template>
  <div :class="currentOptions.position" id="hello">
    <div class="contacts">
      <i class="fas fa-bars fa-2x"></i>
      <h2>
        Responses
      </h2>
      <div style="overflow-y: scroll; height: 90%">
        <div class="contact" v-for="(x, i) in qa" :key="x" @click="navigateTo(x.id)">
          <div class="badge">
            {{ i + 1 }}
          </div>
          <div class="name">
            {{ x.question }}
          </div>
          <div class="message">
            {{ x.answer }}
          </div>
        </div>
      </div>
    </div>
    <div class="chat" :class="{glassmorphed:currentOptions.glassMorphed}" :style="baseOptions">
      <div class="contact bar">
        <div class="chatbot name">
          <img :src="currentOptions.logoUrl" style="height: 4rem"  class="leftHeading" :class="{centerHeading : currentOptions.logoCenter}" />
          <span>{{ currentOptions.title }}</span>
        </div>

      </div>
      <div class="messages" id="chat">
        <div class="time">
          Today
        </div>
        <template v-for="x in messages">
          <div v-if="x.type === 'answer'" class="message user" :id="x.id" :key="x.id">
            {{ x.text }}
          </div>
          <div v-else class="message chatbot" :id="x.id" :key="x.id">
            {{ x.text }}
          </div>
        </template>
        <template v-if="current !== -1">
          <div class="message chatbot" v-if="loading">
            <div class="typing typing-1"></div>
            <div class="typing typing-2"></div>
            <div class="typing typing-3"></div>
          </div>
          <div class="message chatbot" v-else-if="current !== null && metadata[current] !== null">
            {{ metadata[current].text }}
          </div>
        </template>
        <div class="message chatbot" v-else-if="finalMessage !== undefined && finalMessage">
          {{ "Alright! Your Appointment have been scheduled on " + formData.appointment_time + " at " + formData.appointment + " in the " + formData.department + " Department" }}
        </div>
      </div>
      <template v-if="loading === false && current !== -1">
        <div v-if="current !== null && metadata[current] !== null">
          <div class="input" v-if="metadata[current].type === 'text'">
            <input placeholder="Type here!" type="text" v-model="textInput" v-on:keyup.enter="submitCurrent(textInput)"/><div class="Buttons"> <button class="btn btn2" id="input-btn" @click="submitCurrent(textInput)"><i class="far fa-paper-plane"></i></button></div>
          </div>
          <div class="input" v-if="metadata[current].type === 'single'">
            <div class="Buttons" v-for="x in metadata[current].values" :key="x.key"> <button class="btn btn2" @click="submitCurrent(x.value)">{{ x.value }}</button></div>
          </div>
          <div class="input" v-if="metadata[current].type === 'bool'">
            <div class="Buttons"> <button class="btn btn2" @click="submitCurrent(true)">Yes</button></div><div class="Buttons"> <button class="btn btn2" @click="submitCurrent(false)">No</button></div>
          </div>
  <!--        <div class="input" >-->
  <!--          <div class="Buttons"> <button class="btn btn2">Hover Me</button></div>-->
  <!--        </div>-->
        </div>
        <div v-else>
          <div class="input" >
            <div class="Buttons"> <button class="btn btn2" @click="submit">Submit</button></div>
          </div>
        </div>
        <div class="input" v-if="current !== null && metadata[current].type=='upload'">
          <input placeholder="Upload your file!" type="file" id="fileUpload" /><div class="Buttons"> <button class="btn btn2" id="input-btn" @click="chooseFiles"><i class="fa fa-upload" style="padding-right: 5px"></i> Upload</button></div>
        </div>
        <div class="input" v-if="current !== null && metadata[current].type=='datepicker'">
          <span style="font-weight: 600;font-size: 1.2rem">Select Date</span><Datepicker format="dd-MMM-yyyy" v-model="textInput" style="padding: 1.5rem 1rem; margin: auto"></Datepicker>
          <div class="Buttons"> <button class="btn btn2" @click="submitCurrent(textInput)">Submit</button></div>
        </div>
      </template>
    </div>
  </div>

</template>

<script>
import Datepicker from 'vuejs-datepicker';
const options = {
  topBottom: "white",
  userColor: "#333",
  chatbotMsgColor: "white",
  glassMorphed: false,
  position: "center",
  inputRadius:"1",
  button: 'primary',
  logoCenter: false,
  logoUrl: 'https://www.freepnglogos.com/uploads/flipkart-logo-png/flipkart-inventory-management-system-zap-inventory-1.png',
  title:'Doctors Appointment',
}
export default {
  name: "chatbot",
  components:{
    Datepicker
  },
  props:{
    options: {
      type: Object,
      default: null,
    },
    meta: {
      type: Array,
      default: null,
    },
    finalMessage: {
      type: Function
    }
  },
  data(){
    return{
      loading: true,
      textInput: '',
      currentOptions: {},
      metadata: {},
      current: 0,
      currentControl: -1,
      formData: {},
      messages: [],
      qa: [],
      finalMessageText: ''
    }
  },
  mounted () {
    this.currentOptions = options
    Object.keys(this.options).forEach((key) => {
      this.currentOptions[key] = this.options[key]
    })
    this.initialize()
  },
  methods: {
    initialize() {
      this.meta.forEach((meta) => {
        this.formData[meta.name] = null
        this.metadata[meta.id] = {
          next: null,
          callbacks: null,
          ...meta,
        }
      })
      this.next()
      this.finalMessage(this.formData).then(value => {
        this.finalMessageText = value
      })
    },
    submit() {
      this.$emit('submit', this.formData)
      this.current = -1
    },
    next() {
      this.loading = true
      const vm = this
      setTimeout(() => {
        if (this.currentControl === -1) {
          this.currentControl = 0
        } else {
          this.currentControl = this.metadata[this.currentControl].next
        }
        console.log(this.current)
        console.log(this.currentControl)
        if (this.currentControl === null) {
          this.current = null
          this.loading = false
        } else {
          console.log("SAFs")
          if (this.metadata[this.currentControl].callbacks !== undefined && this.metadata[this.currentControl].callbacks) {
            if (this.metadata[this.currentControl].callbacks.getValues !== undefined) {
              this.metadata[this.currentControl].callbacks.getValues(this.formData).then((res) => {
                vm.metadata[vm.currentControl].values = res;
                vm.current = vm.currentControl
              }).catch((err) => {
                console.error(err)
                if (vm.metadata[vm.currentControl].callbacks && vm.metadata[vm.currentControl].callbacks.onError !== undefined) {
                  vm.metadata[vm.currentControl].callbacks.onError(err, vm.metadata[vm.current])
                }
              })
            }
            if (this.metadata[this.currentControl].text === undefined || this.metadata[this.currentControl].text === null || this.metadata[this.currentControl].text === '') {
              if (this.metadata[this.currentControl].callbacks.getText !== undefined) {
                this.metadata[this.currentControl].callbacks.getText(this.formData).then((res) => {
                  vm.metadata[vm.currentControl].text = res;
                }).catch((err) => {
                  console.error(err)
                  vm.metadata[vm.currentControl].text = "Please enter text for field " + vm.metadata[vm.currentControl].name
                })
              } else {
                if(vm.metadata[vm.currentControl].name === 'name') {
                  vm.metadata[vm.currentControl].text = "What is your name?"
                } else if(vm.metadata[vm.currentControl].name === 'appointment_time') {
                  vm.metadata[vm.currentControl].text = "What time would you like the appointment to be Scheduled?"
                } else if(vm.metadata[vm.currentControl].name === 'rating') {
                  vm.metadata[vm.currentControl].text = "Don't forget to give a rating."
                } else {
                  vm.metadata[vm.currentControl].text = "Please enter text for field " + vm.metadata[vm.currentControl].name
                }
              }
            }
            vm.current = this.currentControl
            this.loading = false
          } else {
            if (this.metadata[this.currentControl].text === undefined || this.metadata[this.currentControl].text === null || this.metadata[this.currentControl].text === '') {
              if(vm.metadata[vm.currentControl].name === 'name') {
                vm.metadata[vm.currentControl].text = "What is your name?"
              } else if(vm.metadata[vm.currentControl].name === 'appointment_time') {
                vm.metadata[vm.currentControl].text = "What time would you like the appointment to be Scheduled?"
              } else if(vm.metadata[vm.currentControl].name === 'rating') {
                vm.metadata[vm.currentControl].text = "Don't forget to give a rating."
              } else {
                vm.metadata[vm.currentControl].text = "Please enter text for field " + vm.metadata[vm.currentControl].name
              }
            }
            vm.current = this.currentControl
            this.loading = false
          }
        }
        this.loading = false
      }, 1200);
    },
    submitCurrent(val) {
      this.formData[this.metadata[this.current].name] = val
      console.log(val)
      this.textInput = ''
      this.qa.push({
        question: this.metadata[this.current].text,
        answer: val,
        id: 'question-' + this.metadata[this.current].id,
      })
      this.messages.push({
        type: 'question',
        text: this.metadata[this.current].text,
        id: 'question-' + this.metadata[this.current].id
      })
      this.messages.push({
        type: 'answer',
        text: val,
        id: 'answer-' + this.metadata[this.current].id
      })
      this.loading = true
      this.next()
    },
    chooseFiles() {
      document.getElementById("fileUpload").click()
    },

    navigateTo(id) {
      document.getElementById(id).scrollIntoView();
    },
  },
  computed: {
    baseOptions(){
      let options= {
        '--bg-color': this.currentOptions.topBottom,
        '--user':this.currentOptions.userColor,
        '--chatbot-msg':this.currentOptions.chatbotMsgColor,
        '--chatbot':this.currentOptions.position,
        '--input-radius':this.currentOptions.inputRadius+ 'rem',

      }
      switch (this.currentOptions.button){
        case 'primary': options['--button-radius']= '0rem'; break;
        case 'rounded': options['--button-radius']= '1rem'; break;
        case 'pill': options['--button-radius']= '5rem'; break;
      }
      if(this.currentOptions.logoCenter){
        options['--center']='none';
      }
      return options;
    }
  },
}
</script>

<style>

@import url("https://fonts.googleapis.com/css?family=Red+Hat+Display:400,500,900&display=swap");

body, html {
  font-family: Red hat Display, sans-serif;
  font-weight: 400;
  line-height: 1.25em;
  letter-spacing: 0.025em;
  color: #333;
  background: #F7F7F7;
}

.center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.bottom-right{
  position: absolute;
  bottom: 2rem;
  right: 2rem;
}
.pic {
  width: 4rem;
  height: 4rem;
  background-size: cover;
  background-position: center;
  border-radius: 50%;
}
.leftHeading{
  position: absolute;
  left:0;
  top: 0;
}
.centerHeading{
  position: relative;
  margin:0;
}

.contact {
  position: relative;
  margin-bottom: 1rem;
  padding-left: 5rem;
  height: 4.5rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.contact .pic {
  position: absolute;
  left: 0;
}

.contact .name {
  font-weight: 700;
  font-size:1.4rem;

  margin-bottom: 0.125rem;
}
.contact .name span{
  display:var(--center);
}
.contact .message, .contact .seen {
  font-size: 0.9rem;
  color: #999;
}
.contact .badge {
  box-sizing: border-box;
  position: absolute;
  width: 4rem;
  height: 4rem;
  text-align: center;
  font-size: 1.5rem;
  padding-top: 1.25rem;
  border-radius: 3rem;
  top: 0;
  left: 0rem;
  background: #ffe5b4;
  color: black;
}

.contacts {
  position: absolute;
  top: 50%;
  left: 0;
  transform: translate(-6rem, -50%);
  width: 24rem;
  height: 32rem;
  padding: 1rem 2rem 1rem 1rem;
  box-sizing: border-box;
  border-radius: 1rem 0 0 1rem;
  cursor: pointer;
  background: white;
  box-shadow: 0 0 8rem 0 rgba(0, 0, 0, 0.1), 2rem 2rem 4rem -3rem rgba(0, 0, 0, 0.5);
  transition: transform 500ms;
}
.contacts h2 {
  margin: 0.5rem 0 1.5rem 5rem;
}
.contacts .fa-bars {
  position: absolute;
  left: 2.25rem;
  color: #999;
  transition: color 200ms;
}
.contacts .fa-bars:hover {
  color: #666;
}
.contacts .contact:last-child {
  margin: 0;
}
.contacts:hover {
  transform: translate(-23rem, -50%);
}

.chat {
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 30rem;
  height: 38rem;
  z-index: 2;
  box-sizing: border-box;
  border-radius: 1rem;
  background: var(--bg-color);
  box-shadow: 0 0 8rem 0 rgba(0, 0, 0, 0.1), 0rem 2rem 4rem -3rem rgba(0, 0, 0, 0.5);
}
.glassmorphed{
  backdrop-filter: blur(10px) saturate(146%);
  -webkit-backdrop-filter: blur(10px) saturate(146%);

  border-radius: 12px;
  border: 1px solid rgba(255, 255, 255, 0.125);
}
.chat .contact.bar {
  flex-basis: 3.5rem;
  flex-shrink: 0;
  margin: 1rem;
  box-sizing: border-box;
}
.chat .messages {
  padding: 1rem;
  background: #F7F7F7;
  flex-shrink: 2;
  height: 100%;
  overflow-y: auto;
  box-shadow: inset 0 2rem 2rem -2rem rgba(0, 0, 0, 0.05), inset 0 -2rem 2rem -2rem rgba(0, 0, 0, 0.05);
}
.chat .messages::-webkit-scrollbar {
  width: 15px;
}


.chat .messages::-webkit-scrollbar-thumb {
  border-radius: 100px;
  border: 5px solid transparent;
  background-clip: content-box;
  background-color: var(--bg-color);
}
.chat .messages .time {
  font-size: 0.8rem;
  background: #EEE;
  padding: 0.25rem 1rem;
  border-radius: 2rem;
  color: #999;
  width: -webkit-fit-content;
  width: -moz-fit-content;
  width: fit-content;
  margin: 0 auto;
}
.chat .messages .message {
  box-sizing: border-box;
  padding: 0.5rem 1rem;
  margin: 1rem;
  background: var(--chatbot-msg);
  border-radius: 1.125rem 1.125rem 1.125rem 0;
  min-height: 2.25rem;
  width: -webkit-fit-content;
  width: -moz-fit-content;
  width: fit-content;
  max-width: 66%;
  box-shadow: 0 0 2rem rgba(0, 0, 0, 0.075), 0rem 1rem 1rem -1rem rgba(0, 0, 0, 0.1);
}
.chat .messages .message.user {
  margin: 1rem 1rem 1rem auto;
  border-radius: 1.125rem 1.125rem 0 1.125rem;
  background: var(--user);
  color: white;
}
.chat .messages .message .typing {
  display: inline-block;
  width: 0.8rem;
  height: 0.8rem;
  margin-right: 0rem;
  box-sizing: border-box;
  background: #ccc;
  border-radius: 50%;
}
.chat .messages .message .typing.typing-1 {
  -webkit-animation: typing 3s infinite;
  animation: typing 3s infinite;
}
.chat .messages .message .typing.typing-2 {
  -webkit-animation: typing 3s 250ms infinite;
  animation: typing 3s 250ms infinite;
}
.chat .messages .message .typing.typing-3 {
  -webkit-animation: typing 3s 500ms infinite;
  animation: typing 3s 500ms infinite;
}
.chat .input {
  box-sizing: border-box;
  flex-basis: 4rem;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  padding: 0 0.5rem 0 1.5rem;
}
.chat .input i {
  font-size: 1.2rem;
  color:var(--user);
  cursor: pointer;
  transition: color 200ms;
  padding: -10px;
}
.chat .input i:hover {
  color: #fff;
}
#input-btn{
  padding: 10px 15px;
}
.chat .input input {
  border: none;
  background-image: none;
  background-color: white;
  padding: 0.6rem 1rem;
  margin-right: 1rem;
  border-radius: var(--input-radius);
  flex-grow: 2;
  box-shadow: 0 0 1rem rgba(0, 0, 0, 0.1), 0rem 1rem 1rem -1rem rgba(0, 0, 0, 0.2);
  font-family: Red hat Display, sans-serif;
  font-weight: 400;
  letter-spacing: 0.025em;
}
.chat .input input:placeholder {
  color: #999;
}
/*.chat .input input:focus{*/
/*  transition-property: outline;*/
/*  transition-duration: 4s;*/
/*}*/

@-webkit-keyframes typing {
  0%, 75%, 100% {
    transform: translate(0, 0.25rem) scale(0.9);
    opacity: 0.5;
  }
  25% {
    transform: translate(0, -0.25rem) scale(1);
    opacity: 1;
  }
}

@keyframes typing {
  0%, 75%, 100% {
    transform: translate(0, 0.25rem) scale(0.9);
    opacity: 0.5;
  }
  25% {
    transform: translate(0, -0.25rem) scale(1);
    opacity: 1;
  }
}
.pic.chatbot {
  background-image: url("https://vignette.wikia.nocookie.net/marvelcinematicuniverse/images/7/73/SMH_Mentor_6.png");
}

.pic.banner {
  background-image: url("https://vignette.wikia.nocookie.net/marvelcinematicuniverse/images/4/4f/BruceHulk-Endgame-TravelingCapInPast.jpg");
}

.pic.thor {
  background-image: url("https://vignette.wikia.nocookie.net/marvelcinematicuniverse/images/9/98/ThorFliesThroughTheAnus.jpg");
}

.pic.danvers {
  background-image: url("https://vignette.wikia.nocookie.net/marvelcinematicuniverse/images/0/05/HeyPeteruser.png");
}

.pic.rogers {
  background-image: url("https://vignette.wikia.nocookie.net/marvelcinematicuniverse/images/7/7c/Cap.America_%28We_Don%27t_Trade_Lives_Vision%29.png");
}
.btn {
  border: 2px solid var(--user);
  background: none;
  padding: 15px 20px;
  border-radius:var(--button-radius) ;
  font-size: 14px;
  font-family: "Segoe UI";
  font-weight: 500;
  cursor: pointer;
  margin: 10px;
  transition: 0.4s;
  position: relative;
  overflow: hidden;
  white-space: nowrap;
}
.btn2{
  color: var(--user);
}
.btn2:hover i{
  color: #fff;
}
.btn2:hover{
  color: #fff;
}

.btn::before{
  content: "";
  position: absolute;
  left: 0;
  width: 100%;
  height: 0%;
  background: var(--user);
  z-index: -1;
  transition: 0.4s;
}
.btn2::before{
  bottom: 0;
  border-radius: 50% 50% 0 0;
}
.btn2:hover::before{
  height: 180%;
}
#fileUpload button{
  display: none;
}
</style>

<style scoped>
</style>
