<template>
<div class="container">
  <div class="">
      <div class="container d-flex align-items-center flex-shrink-0 p-3 link-dark text-decoration-none border-bottom">
          <img src="../assets/GeeksBay-4.jpg" alt="logo">
        <input class="fs-6 fw-semibold" placeholder="Your name" v-model="username">
      </div>
      <div class="container list-group list-group-flush border-bottom scrollarea">
        <div class="list-group-item list-group-item-action py-3 lh-tight"
          v-for="message in messages" :key="message"
        >
        <div class="d-flex w-100 align-items-right justify-content-between">
          <strong class="mb-1">{{ message.username }}</strong>
        </div>
        <div class="col-10 mb-1 small">{{ message.message }}</div>
      </div>
    </div>
  </div>
  <form class="form-container" @submit.prevent="submit">
    <input class="form-control" placeholder="Write a message" v-model="message">
  </form>
</div>
</template>

<script>
import {ref, onMounted} from 'vue';
import Pusher from 'pusher-js';

export default {
  name: 'App',
  setup() {
    const username = ref('')
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
    }

    return {
      username,
      messages,
      message,
      submit
    }
  }
}
</script>

<style scoped>
.container {
  background-color:#330623;
}
.scrollarea {
  min-height: 724px;
}
.form-container {
  padding-bottom: 10px;
}
</style>
