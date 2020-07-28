<template>
  <div class="container"></div>
</template>
<script>
import Echo from "laravel-echo";
export default {
  data() {
    return {
      liveData: [],
      socketLiveStockInput: {
        channelName: "BalanceUpdateEvent",
        eventName: "BalanceUpdateEvent",
      },
      hostname: "<your_hostname>",
      port: 6001,
      Key: "<Secrate_socket_key>",
    };
  },
  created() {
    this.setUpSocketIO();
  },
  mounted() {
    this.listenForBroadcast(this.socketLiveStockInput, ({ data }) => {
      console.log(data);
      this.liveData.push(data.data);
    });
  },
  methods: {
    setUpSocketIO: (hostName = this.hostname, port = this.port) => {
      window.io = require("socket.io-client");
      window.Pusher = require("pusher-js");

      if (typeof io !== "undefined") {
        try {
          window.Echo = new Echo({
            broadcaster: "pusher",
            key: this.key, 
            wsHost: hostName,
            wsPort: port,
            disableStats: true,
          });
        } catch (error) {
          console.log(error.message);
        }
      }
    },
    listenForBroadcast({ channelName, eventName }, callback) {
      window.Echo.channel(channelName).listen(eventName, callback);
    },
  },
};
</script>
