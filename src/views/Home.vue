<template>
  <div class="home">
    <ToggleAndExport />
  </div>
</template>

<script>
// @ is an alias to /src
import ToggleAndExport from "@/components/ToggleAndExport.vue";

export default {
  name: "Home",
  components: {
    ToggleAndExport,
  },
};
</script>
