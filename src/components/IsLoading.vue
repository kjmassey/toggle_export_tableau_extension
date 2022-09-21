<template>
  <!-- This is just a spinner to show that something is happening :) -->
  <v-overlay :value="this.$props.show">
    <v-progress-circular indeterminate size="64"></v-progress-circular>
  </v-overlay>
</template>

<script>
export default {
  props: { show: Boolean },
};
</script>

<style></style>
