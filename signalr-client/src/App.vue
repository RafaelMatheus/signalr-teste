<template>
  <div id="app">
    <div class="container">
      <div class="row">&nbsp;</div>
      <div class="row">
        <div class="col-6">&nbsp;</div>
        <div class="col-6">
          <form class="form-inline">
            <div class="input-prepend input-append">
              <input
                type="text"
                name="group-message"
                id="group-message-text"
                placeholder="Type a message"
                v-model="message"
              />
              <input v-model="groupName" type="text" name="group-name" id="group-name" placeholder="Type a group name" />
              <button @click.prevent="sendMessage" id="groupmsg" class="btn">Send to group</button>
              <button @click.prevent="joinGroup" id="join-group" class="btn">Join Group</button>
              <button @click.prevent="leaveGroup" id="leave-group" class="btn">Leave Group</button>
            </div>
          </form>
        </div>
      </div>
      <div class="row">
        <div class="col-12">
          <hr />
        </div>
      </div>
      <div class="row">
        <div class="col-6">&nbsp;</div>
        <div class="col-6">
          <ul id="messagesList">
            <li v-for="(message, index) in messages" :key="index">{{message}}</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { HubConnectionBuilder, LogLevel }from '@aspnet/signalr';

export default {
  name: "App",
  components: {},
  data(){
    return {
      connection: {},
      groupName: '',
      message: '',
      messages: []
    }
  },
  methods: {
    joinGroup(){
      this.connection.invoke("AddToGroup", this.groupName).then(e => console.log(e));
    },
    sendMessage(){
      this.connection.invoke("SendMessageToGroup", this.groupName, this.message).then(() => {
        this.message = '';
      })
    },
    leaveGroup(){
      this.connection.invoke("RemoveFromGroup", this.groupName).then(e => console.log(e));
    }
  },
  created(){
    this.connection = new HubConnectionBuilder()
    .configureLogging(LogLevel.Debug)
    .withUrl("https://localhost:5001/chathub")
    .build();
    this.connection.start().then(e => console.log(e));
  },
  mounted(){
    this.connection.on("Send", (message) =>{
        this.messages.push(message);
        console.log(message);
    });
  }
};
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
