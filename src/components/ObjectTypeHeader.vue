<template>
  <div class="py-1">
    <h5 class="px-2 py-2 rounded">{{ objType }}</h5>
  </div>
</template>

<script>
export default {
  props: { objType: String },
};
</script>

<style scoped>
h5 {
  background-color: lightgray;
}
</style>
