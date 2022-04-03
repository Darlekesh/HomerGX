<template>
  <Generic :item="item">
    <template #content>
      <p class="title is-4">{{ item.name }}</p>
      <p class="subtitle is-6">
        <template v-if="item.subtitle">
          {{ item.subtitle }}
        </template>
        <template v-else-if="onlinec">
          {{ onlinec }} {{ offlinec}}
        </template>
      </p>
    </template>
  </Generic>
</template>

<script>
import service from "@/mixins/service.js";
import Generic from "./Generic.vue";

export default {
  name: "Portainer",
  mixins: [service],
  props: {
    item: Object,
  },
  components: {
    Generic,
  },
  data: () => ({
    api: null,
  }),
  computed: {
    onlinec: function () {
      if (this.online) {
        return this.online.toFixed();
      }
      return "";
    },
    offlinec: function () {
      if (this.offline) {
        return this.offline.toFixed();
      }
      return "";
    },
  },
  created() {
    this.fetchStatus();
  },
  methods: {
    fetchStatus: async function () {
      if (this.item.subtitle != null) return;

      const apikey = this.item.apikey;
      if (!apikey) {
        console.error(
          "apikey is not present in config.yml for the portainer entry!"
        );
        return;
      }
      const result = await this.fetch("/api/endpoints/2?all=true", {
        headers: {
          'X-API-Key': + this.item.apikey,
        },
      }).catch((e) => console.log(e));

      this.online = result.Snapshots.RunningContainerCount;
      this.offline = result.Snapshots.StoppedContainerCount;
    },
  },
};
</script>
