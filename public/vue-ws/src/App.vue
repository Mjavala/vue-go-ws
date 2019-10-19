<template>
<div id="app">
  <button @click="disconnect" v-if="status === 'connected'">Disconnect</button>
  <button @click="connect" v-if="status === 'disconnected'">Connect</button> {{ status }}
  <br /><br />
  <div v-if="status === 'connected'">
    <form @submit.prevent="sendMessage" action="#">
      <input v-model="message"><button type="submit">Send Message</button>
    </form>
    <ul id="logs">
      <li v-for="(log, index) in logs" class="log" :key="index">
        {{ log.event }}: {{ log.data }}
      </li>
    </ul>
  </div>
</div>
</template>

<script>
export default{
  el: '#app',
  data () {
    return {
      message: '',
      logs: [],
      status: 'disconnected'
    }
  },
  methods: {
    connect () {
      this.socket = new WebSocket('ws://localhost:8080/ws')
      this.socket.onopen = () => {
        this.status = 'connected'
        this.logs.push({event: 'Connected to', data: 'wss://echo.websocket.org'})
        this.socket.onmessage = ({data}) => {
          this.logs.push({ event: 'Recieved message', data })
        }
      }
    },
    disconnect () {
      this.socket.close()
      this.status = 'disconnected'
      this.logs = []
    },
    sendMessage (e) {
      this.socket.send(this.message)
      this.logs.push({ event: 'Sent message', data: this.message })
      this.message = ''
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
