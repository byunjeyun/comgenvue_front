<template>
  <div>
    <HelloWorld msg="Vue 유지보유내역리스트 Vue"/>
    <RequestsData />
  </div>
</template>

<script>
import axios from 'axios';
import HelloWorld from './components/HelloWorld.vue'
import RequestsData from './components/RequestsData.vue'


export default {
  name: 'App',
  components: {
    HelloWorld,
    RequestsData,
  },
  data() {
    return {
      requests: [],
    };
  },
  mounted() {
    axios.get('http://localhost:8085/requests')
      .then(response => {
        this.requests = response.data;
      })
      .catch(error => {
        console.error(error);
      });
  },
};
</script>