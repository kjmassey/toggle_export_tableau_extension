<template>
  <div>
    <div class="d-flex justify-space-between px-2 pt-2 pb-3">
      <h3>Toggle & Export</h3>
      <v-icon v-on:click="backToHome()" color="#4E79A7">mdi-close</v-icon>
    </div>
    <div class="px-2 text-justify">
      This is my first foray into the world of the Tableau Extensions API! This
      meant to be an example/tutorial of some introductory API functionality, as
      well as how to integrate with a modern JavaScript framework.
    </div>

    <div class="px-2 py-3 text-justify">
      I hope to have a blog post/YouTube to accompany this in the near future!
    </div>
    <div class="px-6 pb-2">
      <v-btn
        href="https://github.com/kjmassey/toggle_export_tableau_extension"
        target="_blank"
        elevation="0"
        width="100%"
        color="#4E79A7"
        class="white--text"
        >View Source @ GitHub</v-btn
      >
    </div>
    <div class="pt-4 d-flex justify-center">
      <a href="https://twitter.com/UpInYourVizness" target="_blank">
        <div class="px-2 d-flex align-center">
          <v-icon small color="#1DA1F2" class="pr-1">mdi-twitter</v-icon>
          <span class="caption font-weight-bold">@UpInYourVizness </span>
        </div>
      </a>

      <a href="https://github.com/kjmassey" target="_blank">
        <div class="px-2 d-flex align-center">
          <v-icon small color="black" class="pr-1">mdi-github</v-icon>
          <span class="caption font-weight-bold">@kjmassey</span>
        </div>
      </a>
    </div>
  </div>
</template>

<script>
import router from "../router";

export default {
  methods: {
    backToHome: function () {
      router.push("/");
    },
  },
};
</script>

<style scoped>
a {
  text-decoration: none;
}
</style>
