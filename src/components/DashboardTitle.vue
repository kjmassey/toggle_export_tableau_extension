<template>
  <div class="d-flex justify-space-between align-center">
    <h3 class="py-2">{{ dbName }}</h3>
    <div>
      <v-icon v-on:click="showAbout()" color="#4E79A7"
        >mdi-information-outline</v-icon
      >
    </div>
  </div>
</template>

<script>
import router from "../router";

export default {
  props: { dbName: String },
  methods: {
    showAbout: function () {
      router.push("/about");
    },
  },
};
</script>
