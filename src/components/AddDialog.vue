<template>
  <div>
    <el-button type="success" icon="el-icon-circle-plus" @click="dialogOpened = true">Add</el-button>
    <el-dialog title="Add New Connect" :visible.sync="dialogOpened">
      <el-form>
        <el-form-item label="Name">
          <el-input autocomplete="off" v-model="name"></el-input>
        </el-form-item>
        <el-form-item label="Websocket URI">
          <el-input autocomplete="off" v-model="uri"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="sumbit">Create</el-button>
        <el-button @click="dialogOpened = false">Cancel</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<style scoped>
</style>

<script>
export default {
  data() {
    return {
      dialogOpened: false,
      name: "New Websocket",
      uri: "ws://127.0.0.1:8080"
    };
  },
  methods: {
    sumbit: function () {
      let webSocket = new WebSocket(this.uri);

      webSocket.onclose = (event) => {
        this.$message({
          type: 'error',
          message: 'connect ' + this.uri + ' fail'
        });
      };

      webSocket.onopen = (event) => {
        this.dialogOpened = false;
        webSocket.onclose = null;
        webSocket.onopen = null;
        this.$emit("onConnectCreated", this.name, webSocket);
      };
    }
  },
  components: {
  }
}
</script>
