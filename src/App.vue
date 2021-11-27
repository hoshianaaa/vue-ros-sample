<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>
</template>

<script>
import ROSLIB from "roslib"
import HelloWorld from './components/HelloWorld.vue'

const ros =  new ROSLIB.Ros({
    url: 'ws://localhost:9090'
});

// publisher
var cmdVel = new ROSLIB.Topic({
    ros : ros,
    name : '/cmd_vel',
    messageType : 'geometry_msgs/Twist'
});

var twist = new ROSLIB.Message({
    linear : {
      x : 0.1,
      y : 0.2,
      z : 0.3
    },
    angular : {
      x : -0.1,
      y : -0.2,
      z : -0.3
    }
});

cmdVel.publish(twist);

// listener
var listener = new ROSLIB.Topic({
    ros : ros,
    name : '/listener',
    messageType : 'std_msgs/String'
});

listener.subscribe(function(message) {
    console.log('Received message on ' + listener.name + ': ' + message.data);
    listener.unsubscribe();
});

// Service client
var addTwoIntsClient = new ROSLIB.Service({
    ros : ros,
    name : '/add_two_ints',
    serviceType : 'rospy_tutorials/AddTwoInts'
});

var request = new ROSLIB.ServiceRequest({
    a : 1,
    b : 2
});

// Service server
addTwoIntsClient.callService(request, function(result) {
    console.log('Result for service call on ' + addTwoIntsClient.name + ': ' + result.sum);
});

var setBoolServer = new ROSLIB.Service({
    ros : ros,
    name : '/set_bool',
    serviceType : 'std_srvs/SetBool'
});

setBoolServer.advertise(function(request, response) {
    console.log('Received service request on ' + setBoolServer.name + ': ' + request.data);
    response['success'] = true;
    response['message'] = 'Set successfully';
    return true;
});

// Param
ros.getParams(function(params) {
    console.log(params);
});

var maxVelX = new ROSLIB.Param({
    ros : ros,
    name : 'max_vel_y'
});

maxVelX.set(0.8);
maxVelX.get(function(value) {
    console.log('MAX VAL: ' + value);
});

var favoriteColor = new ROSLIB.Param({
    ros : ros,
    name : 'favorite_color'
});

favoriteColor.set('red');
favoriteColor.get(function(value) {
    console.log('My robot\'s favorite color is ' + value);
});

export default {
  name: 'App',
  components: {
    HelloWorld
  },

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
