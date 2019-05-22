<template>
  <div>
    <AddDialog @onConnectCreated="onConnectCreated"></AddDialog>
    <el-tabs tab-position="left" type="border-card">
      <el-tab-pane v-for="tabData in tabDatas" :key="tabData.id">
        <span slot="label">{{ tabData.name }}</span>
        <SocketConsole :webSocket="tabData.webSocket"></SocketConsole>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<style scoped>
</style>

<script>
import AddDialog from "./components/AddDialog.vue";
import SocketConsole from "./components/SocketConsole.vue";

export default {
  data() {
    return {
      nextTabId: 1,
      addConnectDialog: true,
      tabDatas: []
    };
  },
  methods: {
    onConnectCreated: function (name, webSocket) {
      this.tabDatas.push({
        id: this.nextTabId++,
        name: name,
        webSocket: webSocket
      });
    }
  },
  components: {
    AddDialog: AddDialog,
    SocketConsole: SocketConsole
  }
}
</script>
