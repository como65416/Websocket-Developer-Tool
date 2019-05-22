<template>
  <div>
    <el-container>
      <el-header height="180">
        <p>Server URL : {{ url }}</p>
        Status :
        <div v-if="status == 'connected'">
          <el-tag type="success">{{ status }}</el-tag>
          <el-button size="mini" type="primary" @click="disconnectSocket">Disconnect</el-button>
        </div>
        <div v-else>
          <el-tag type="info">{{ status }}</el-tag>
          <el-button size="mini" type="info" @click="connectSocket">Connect</el-button>
        </div>
        <br /><br />
        <el-row>
          <el-col :span="18">
            <el-input
              placeholder="message..."
              v-model="message">
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-button type="primary" @click="sendMessage" :disabled="status != 'connected'">Send</el-button>
          </el-col>
        </el-row>
      </el-header>
      <el-main>
        <el-row>
          <el-col :span="24">
            <el-card class="box-card" style="height:400px; overflow:scroll;">
              <div v-for="info in logs.infos"
                :key="info.id"
                style="word-break: break-word;">
                <span style="color:#E6A23C;" v-if="info.type == 'warning'">
                  <i class="el-icon-info"></i> {{ info.time }} : {{info.message}}
                </span>
                <span style="color:#F56C6C;" v-else-if="info.type == 'error'">
                  <i class="el-icon-error"></i> {{ info.time }} : {{info.message}}
                </span>
                <span style="color:#67C23A;" v-else-if="info.type == 'send'">
                  <i class="el-icon-caret-top"></i> {{ info.time }} : {{info.message}}
                </span>
                <span style="color:#909399;" v-else-if="info.type == 'receive'">
                  <i class="el-icon-caret-bottom"></i> {{ info.time }} : {{info.message}}
                </span>
                <span style="color:#409EFF;" v-else-if="info.type == 'info'">
                  <i class="el-icon-info"></i> {{ info.time }} : {{info.message}}
                </span>
              </div>
            </el-card>
          </el-col>
        </el-row>
      </el-main>
    </el-container>
    <br />
  </div>
</template>

<style scoped>
</style>

<script>
import moment from 'moment';

export default {
  data() {
    this.socketBindingEvents(this.webSocket);

    return {
      socket: this.webSocket,
      status: "connected",
      url: this.webSocket.url,
      message: "",
      logs : {
        next_id: 1,
        infos : []
      },
      window: {
        width: 0,
        height: 0
      }
    };
  },
  props: ['webSocket'],
  methods: {
    sendMessage: function () {
      this.socket.send(this.message);
      this.addLog('send', this.message);
    },
    addLog: function (type, text) {
      this.logs.infos.push({
        id: this.logs.next_id++,
        type: type,
        time: moment().format("YYYY-MM-DD HH:mm:ss"),
        message: text
      });
    },
    disconnectSocket: function () {
      this.socket.close();
    },
    connectSocket: function () {
      let newSocket = new WebSocket(this.webSocket.url);
      newSocket.onopen = (event) => {
        newSocket.onopen = null;
        this.status = 'connected';
        this.socketBindingEvents(newSocket);
        this.socket = newSocket;
        this.addLog('info', 'reconnection success.');
      };
    },
    socketBindingEvents: function (webSocket) {
      webSocket.onmessage = (event) => {
        this.addLog('receive', event.data);
      }

      webSocket.onclose = (event) => {
        this.status = 'disconnected';
        this.addLog('warning', 'connection is closed.');
      }
    }
  },
  components: {
  }
}
</script>
