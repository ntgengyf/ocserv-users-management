<template>
  <div>
    <v-card height="710">
      <v-card-subtitle>
        <v-row align="center" justify="center" class="mt-2">
          <v-col md="auto" class="mx-2 ma-0 pa-0">
            <v-btn color="secondary" outlined @click="checkStatus">
              Check Status
            </v-btn>
          </v-col>
          <v-col md="auto" class="mx-2 ma-0 pa-0">
            <v-btn color="error" outlined @click="doRestart">
              Do Restart
            </v-btn>
          </v-col>
        </v-row>
        <v-row align="center" justify="start" class="mt-10">
          <v-col md="12" class="info--text py-0 my-1">
            <v-icon color="info">mdi-lightbulb-on-outline</v-icon>
            (Systemd mode) 每分钟只允许重启 VPN 服务1次.
          </v-col>
          <v-col md="12" class="info--text py-0 my-1" v-if="dockerized">
            <v-icon color="info">mdi-lightbulb-on-outline</v-icon>
            (Dockerize mode) 重启操作仅显示系统日志结果.
          </v-col>
        </v-row>
      </v-card-subtitle>
      <v-divider class="mt-5 mb-2" />
      <v-card-text>
        <div v-if="status.length">
          <div v-if="dockerized" class="info--text text-h6 mb-2">
            注意: 结果来自Docker服务容器
          </div>
          <div v-else class="info--text text-h6 mb-2">Result:</div>
          <div v-for="(line, index) in status" :key="index">
            {{ line }}
          </div>
        </div>
      </v-card-text>
    </v-card>
  </div>
</template>
<script lang="ts">
import { systemServiceApi } from "@/utils/services";
import Vue from "vue";
export default Vue.extend({
  name: "OcservSystemd",
  data(): {
    status: string[];
    dockerized: boolean;
  } {
    return {
      status: [],
      dockerized: false,
    };
  },

  mounted(){
    this.checkStatus()  
  },

  methods: {
    async checkStatus() {
      let data = await systemServiceApi.ocserv_status();
      this.status = data.status;
      this.dockerized = data.dockerized || false;
    },

    async doRestart() {
      let data = await systemServiceApi.ocserv_restart();
      this.status = data.status;
      this.dockerized = data.dockerized || false;
    },
  },
});
</script>
