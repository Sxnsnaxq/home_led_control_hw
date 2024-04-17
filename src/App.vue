<template>
  <div class="col">
    <div class="grid grid-cols-1 container mx-auto justify-items-center">
      <img :src="require(`${picture[led_status].file}`)" :width="picture[led_status].size" :height="picture[led_status].size" />
      <div class="h-12 my-5">
        <h3>LED Control Switch</h3>
      </div>
      <div class="h-10 space-x-5">
        <button class="mb-4 bg-green-500 text-white px-4 rounded focus: outline-none" @click="switchClicked(1)">
          Turn ON
        </button>
        <button class="mb-4 bg-red-500 text-white px-4 rounded focus: outline-none" @click="switchClicked(0)">
          Turn OFF
        </button>
      </div>
    </div>
    <div class="grid grid-cols-1 container mx-auto justify-items-center">
      <img :src="require(`${picture[led_status].file}`)" :width="picture[led_status].size" :height="picture[led_status].size" />
      <div class="h-12 my-5">
        <h3>LED Control Switch</h3>
      </div>
      <div class="h-10 space-x-5">
        <button class="mb-4 bg-green-500 text-white px-4 rounded focus: outline-none" @click="switchClicked(0)">
          Turn ON
        </button>
        <button class="mb-4 bg-red-500 text-white px-4 rounded focus: outline-none" @click="switchClicked(1)">
          Turn OFF
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import "./assets/tailwind.css";

export default {
  name: 'App',
  data() {
    return {
      red_status: 0,
      green_status: 0,
      picture: [
        {
          file: './assets/ledoff.png',
          size: 150
        },
        {
          file: './assets/ledon.png',
          size: 150
        }
      ],
    }
  },
  mounted() {
    let apiurl = "https://api.netpie.io/v2/device/shadow/data";
    let headers ={
      "content-type": "application/json",
      "Authorization": "Device gvLwz8t5mruCf3BccAWYYNEoeQRp5kJD:PR2FbiMvCKevdxkJRynEYnP8vzU2EGEm"
    };
    axios.get(apiurl, { headers })
    .then(response => {
      console.log(response.data);
      this.led_status = response.data.data.red_led;
    }).catch(error => {
      console.log(error);
    });
  },
  methods:{
    switchClicked(pramValue){
      let _payload = '';
      if (pramValue == 0){
        _payload = 'RED_ON';
        this.led_status = 1;
      }else{
        _payload = 'RED_OFF';
        this.led_status = 0;
      }
      this.publishToNetpie(_payload); 
    },
    publishToNetpie(payload){
      let target = "topic=lab_ict_kps%2Fcommand";
      let apiurl = "http://api.netpie.io/v2/device/message" + "?" + target;
      let headers ={
        "Content-Type": "text/plain",
        "Authorization": "Device gvLwz8t5mruCf3BccAWYYNEoeQRp5kJD:PR2FbiMvCKevdxkJRynEYnP8vzU2EGEm"
      }
      let data = payload;
      axios.put(apiurl, data, { headers}).then(response => {
        console.log(response.data);
      }).catch(error => {
        console.log(error);
      });
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
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
