<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>
</template>

<script>
import ROSLIB from "roslib" // 追加
import HelloWorld from './components/HelloWorld.vue'

const ros =  new ROSLIB.Ros({
    url: 'ws://localhost:9090'
});

export default {
  name: 'App',
  components: {
    HelloWorld
  },

  // *** 追加 ここから *** //
  mounted() {
    this.init();
  },
  methods: {
    init: function () {
      ros.on("connection", function() {
        console.log("connected to websocket server.");
      });
      ros.on("error", function(error) {
        console.log("error connecting to websocket server: ", error);
      });
      ros.on("close", function() {
        console.log("connection to websocket server closed.");
      });
    }
    // *** 追加 ここまで *** //
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
