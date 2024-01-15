<template>
  <v-container>
    <DashboardTitle :dbName="this.dashboard.name" />
    <ObjectTypeHeader objType="Worksheets" />

    <WorksheetItem
      :ws="ws"
      :db="dashboard"
      v-for="(ws, index) in this.getFilteredObjects('worksheet')"
      :key="`sheet-${index}`"
      v-on:isLoading="setOverlay"
      v-on:sheetSelection="setSheet"
    />
    <DownloadCombinedButton :db="this.dashboard" v-on:isLoading="setOverlay" />
    <div class="pb-2"></div>

    <!-- <GetSelectedMarks /> -->
    <IsLoading :show="this.showOverlay" />
  </v-container>
</template>

<script>
/* global tableau */
import DashboardTitle from "@/components/DashboardTitle";
import ObjectTypeHeader from "@/components/ObjectTypeHeader";
import WorksheetItem from "@/components/WorksheetItem";
import DownloadCombinedButton from "@/components/DownloadCombinedButton";
// import GetSelectedMarks from "@/components/GetSelectedMarks.vue";
import IsLoading from "@/components/IsLoading";

export default {
  name: "ToggleAndExport",

  data: () => ({
    dashboard: {},
    objKeys: ["id", "isFloating", "isVisible", "name", "type"],
    objects: [],
    paramVals: {},
    showOverlay: false,
    selectedSheet: null,
  }),
  components: {
    DashboardTitle,
    ObjectTypeHeader,
    WorksheetItem,
    DownloadCombinedButton,
    // GetSelectedMarks,
    IsLoading,
  },
  methods: {
    setOverlay: function (val) {
      this.showOverlay = val;
    },
    setSheet: function (val) {
      this.selectedSheet = val;
      console.log("+++ ", this.selectedSheet);
    },
    initTab: async function () {
      // Show loader
      this.showOverlay = true;

      // Needed to capture correct instance of 'this' when used below
      var self = this;
      var arr = [];

      // Initialize the Extensions, then do more stuff
      tableau.extensions.initializeAsync().then(() => {
        let db = tableau.extensions.dashboardContent.dashboard;
        self.dashboard = db;

        // Add objects to array in component's state
        db.objects.forEach((e) => {
          var obj = {};

          // The Extensions API returns some items that can't easily stored in component's state
          // This creates a new, simplified object we can use later
          self.objKeys.forEach((x) => {
            obj[x] = e[x];
          });

          arr.push(obj);
        });

        self.objects = arr;

        // hide loader
        this.showOverlay = false;
      });
    },
    getFilteredObjects: function (objType) {
      return this.objects.filter((e) => e.type == objType);
    },
  },
  mounted() {
    this.initTab();
  },
};
</script>

<style scoped>
html {
  overflow-y: hidden !important;
  background: red;
}
</style>
