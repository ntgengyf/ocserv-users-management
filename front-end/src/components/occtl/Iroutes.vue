<template>
  <v-card flat max-width="1200">
    <v-card-title>
      <v-row v-if="routes.length > 5">
        <v-col md="4">
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="搜索路由"
            single-line
            hide-details
          />
        </v-col>
      </v-row>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="routes"
      :search="search"
      :hide-default-footer="routes.length < 5"
    >
    </v-data-table>
  </v-card>
</template>

<script lang="ts">
import Vue from "vue";

export default Vue.extend({
  name: "Iroutes",
  props: {
    routes: Array,
  },
  data() {
    return {
      search: "",
      headers: [
        {
          text: "ID",
          align: "start",
          filterable: true,
          value: "ID",
        },
        {
          text: "用户名",
          align: "start",
          filterable: true,
          value: "Username",
        },
        {
          text: "vhost",
          align: "start",
          filterable: true,
          value: "vhost",
        },
        {
          text: "设备",
          align: "start",
          filterable: false,
          value: "Device",
        },
        {
          text: "IP",
          align: "start",
          filterable: true,
          value: "IP",
        },
        {
          text: "路由",
          align: "start",
          filterable: false,
          value: "iRoutes",
        },
      ],
    };
  },
});
</script>
