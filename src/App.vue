<template>
  <div class="container">
    <div class="main-bg">
        <div class="left">
          <img src="../assets/GeeksBay-4.jpg" alt="logo" />
        </div>
        <div class="right">
          <nav>
            <li><a href="#">Login</a></li>
            <li><a href="#">Sign Up</a></li>
          </nav>
        </div>
      <div class="geek">
        <h1>GeekCentric</h1>
      </div>
      <div class="post-detail">        
        <form class="submit" @submit.prevent="submit">
          <input class="post" placeholder="Type your favourite topic..." v-model="message"/>
        </form>
      </div>
      <div class="postages list-group list-group-flush border-bottom scrollarea">
        <div class="list-group-item list-group-item-action py-3 lh-tight"
             v-for="message in messages" :key="message"
        >
          <div class="post-details col-10 mb-1 small">{{ message.message }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {ref, onMounted} from 'vue';
import Pusher from 'pusher-js';

export default {
  name: 'App',
  setup() {
    const post = ref('');
    const messages = ref([]);
    const message = ref('');

    onMounted(() => {
      Pusher.logToConsole = true;


      const pusher = new Pusher('068133b23cfaf634458b', {
        cluster: 'us3'
      });

      const channel = pusher.subscribe('chat');
      channel.bind('message', data => {
        messages.value.push(data);
      });
    });

    const submit = async () => {
      await fetch('http://localhost:8000/api/messages', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({
          post: post.value,
          message: message.value
        })
      })
      message.value = '';
      post.value = '';
    }

    return {
      post,
      messages,
      message,
      submit
    }
  }
}
</script>

<style scoped>
.container {
  background-color: #330624;
  padding: 30px;
  text-align: center;
  box-sizing: border-box;
}
.right {
  float: right;
}
.left {
  padding-bottom: 25px;
  float: left
}
h1 {
  color: #fdb924;
  font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
}
.submit {
  margin-top: 10;
  width: 100%;
}
.logo {
  margin-left: -900px;
}
.post-detail {
  margin: 1px;
  flex-direction: row;
}
.post-details {
  background-color: #330624;
  padding: 10px;
  box-sizing: border-box;
  color: #fff;
  font-size: 18px;
  border-bottom: 3px solid #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.postages {
  padding: 10px;
  box-sizing: border-box;  
  color: rgb(88, 88, 88);
  margin: 0 10px;
  border-bottom: 3px solid #ccc;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  flex-direction: row;
}
.scrollarea {
  min-height: 808px;
  margin: auto;
}
</style>
