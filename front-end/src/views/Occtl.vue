<template>
  <v-container fluid fill-height>
    <v-row align="center" justify="center">
      <v-col class="d-flex justify-center" md="12" cols="12">
        <v-card
          class="text-center align-center justify-center"
          flat
          width="1400"
          min-height="800"
        >
          <v-card-subtitle
            class="text-h5 grey darken-1 mb-8 white--text text-start"
          >
            Occtl 命令运行器
          </v-card-subtitle>
          <v-card-text>
            <v-row align="center" justify="start" class="mx-15">
              <v-col md="4" class="my-0 py-0 mx-3">
                <v-select
                  v-model="command"
                  label="命令"
                  :items="commands"
                  item-text="text"
                  item-value="command"
                  :rules="[rules.required]"
                  persistent-hint
                  return-object
                  :hint="command ? command.help : ''"
                  @change="CommandVars"
                />
              </v-col>

              <v-col md="3">
                <v-form ref="argsForm">
                  <v-text-field
                    v-model="args"
                    :label="argLable"
                    :rules="argRequired ? [rules.required] : []"
                    :disabled="argDisable"
                  />
                </v-form>
              </v-col>

              <v-col md="auto">
                <v-btn
                  color="primary"
                  outlined
                  :disabled="disabledComput"
                  @click="run"
                >
                  运行
                </v-btn>
              </v-col>
            </v-row>

            <v-col md="12">
              <Result
                :result="result"
                :command="command ? command.command : ''"
              />
            </v-col>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import Vue from "vue";
import { required } from "@/utils/rules";
import { occtlServiceApi } from "@/utils/services";
import { StringToJson } from "@/utils/methods";

interface OcservCommands {
  text: string;
  command: string;
  help: string;
  needArg: boolean;
  label: string | null;
}

export default Vue.extend({
  name: "Occtl",

  components: {
    Result: () => import("@/components/occtl/Result.vue"),
  },

  data(): {
    commands: Array<OcservCommands>;
    command: OcservCommands | null;
    rules: object;
    args: string | null;
    argLable: string | null;
    argRequired: boolean;
    argDisable: boolean;
    result: any;
    StringToJson: Function;
  } {
    return {
      commands: [
        {
          text: "显示状态",
          command: "show_status",
          help: "显示服务器的状态和统计信息",
          needArg: false,
          label: null,
        },
        {
          text: "显示在线用户",
          command: "show_users",
          help: "显示已连接的用户",
          needArg: false,
          label: null,
        },
        {
          text: "显示用户信息",
          command: "show_user",
          help: "显示指定用户的信息",
          needArg: true,
          label: "用户名",
        },
        {
          text: "断开用户",
          command: "disconnect_user",
          help: "断开指定用户的连接",
          needArg: true,
          label: "Username",
        },
        {
          text: "断开 ID",
          command: "disconnect_id",
          help: "断开指定 ID 的连接",
          needArg: true,
          label: "User ID",
        },
        {
          text: "显示路由",
          command: "show_iroutes",
          help: "显示服务器用户提供的路由",
          needArg: false,
          label: null,
        },
        {
          text: "显示被禁止的Ip",
          command: "show_ip_bans",
          help: "显示被禁止的IP地址",
          needArg: false,
          label: null,
        },
        {
          text: "Show Ip Bans Points",
          command: "show_ip_ban_points",
          help: "显示所有已知的被禁止的IP地址",
          needArg: false,
          label: null,
        },
        {
          text: "解除被禁止的IP",
          command: "unban_ip",
          help: "取消指定IP的屏蔽",
          needArg: true,
          label: "被禁止的IP",
        },
        {
          text: "重新加载配置",
          command: "reload_configs",
          help: "重新加载服务器配置",
          needArg: false,
          label: null,
        },
        // {
        //   text: "显示所有会话",
        //   command: "show_sessions_all",
        //   help: "显示所有会话ID",
        //   needArg: false,
        //   label: null,
        // },
        // {
        //   text: "显示有效会话",
        //   command: "show_sessions_valid",
        //   help: "打印所有有效的重新连接会话",
        //   needArg: false,
        //   label: null,
        // },
      ],
      command: null,
      args: null,
      argLable: null,
      argRequired: false,
      argDisable: true,
      rules: { required: required },
      result: null,
      StringToJson: StringToJson,
    };
  },

  computed: {
    disabledComput() {
      if (this.command == null) {
        return true;
      } else {
        if (this.command.needArg && !this.args) {
          return true;
        }
        return false;
      }
    },
  },

  methods: {
    async run() {
      if (this.command?.command == "reload_configs") {
        await occtlServiceApi.reload();
        let status = occtlServiceApi.status();
        if (status == 202) {
          this.$store.commit("setSnackBar", {
            text: "Reloading in progress ...",
            color: "success",
          });
        }
      } else {
        this.result = await occtlServiceApi.config(
          this.command?.command!,
          this.args!
        );
      }
    },

    CommandVars(command: OcservCommands) {
      if (command && command.needArg) {
        this.argRequired = true;
        this.argLable = command.label;
        this.argDisable = false;
      } else {
        this.argRequired = false;
        this.argLable = null;
        this.argDisable = true;
      }
      (this.$refs.argsForm as HTMLFormElement).reset();
    },
  },
});
</script>
