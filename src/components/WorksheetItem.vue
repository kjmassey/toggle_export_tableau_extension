<template>
  <div>
    <div class="px-2 py-1 obj-div sheet-div">
      <div>
        <v-icon medium>mdi-file-chart-outline</v-icon>
        <span class="caption font-weight-medium pl-2">{{ ws.name }}</span>
      </div>
      <div>
        <div class="d-flex">
          <div>
            <v-switch
              v-model="ws.isVisible"
              dense
              :ripple="false"
              color="#4E79A7"
              v-on:change="toggleWorksheet(ws)"
            ></v-switch>
          </div>
          <div>
            <v-icon color="#4E79A7" v-on:click="buildCsv(ws)"
              >mdi-file-delimited-outline</v-icon
            >
            <v-icon color="#4E79A7" v-on:click="downloadExcel(ws)"
              >mdi-microsoft-excel</v-icon
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* global tableau */
import * as XLSX from "xlsx";

export default {
  props: { ws: Object, db: Object },
  methods: {
    toggleWorksheet: function (ws) {
      this.$props.db.objects.forEach((e) => {
        if (e.id == ws.id) {
          var dbObjMap = new Map();
          var newState = e.isVisible
            ? tableau.DashboardObjectVisibilityType.Hide
            : tableau.DashboardObjectVisibilityType.Show;

          dbObjMap.set(e.id, newState);

          this.$props.db
            .setDashboardObjectVisibilityAsync(dbObjMap)
            .then(() => {});
        }
      });
    },
    buildCsv: async function (ws) {
      this.$emit("isLoading", true);

      this.$props.db.worksheets.forEach((sheet) => {
        if (sheet.name == ws.name) {
          sheet.getUnderlyingDataAsync().then((allData) => {
            let colNames = allData.columns.map((e) => e.fieldName);
            let colData = [];

            allData.data.forEach((el) => {
              var obj = {};

              for (let [index, col] of colNames.entries()) {
                obj[col] = el[index].value;
              }

              colData.push(obj);
            });

            let colHeaders = colNames.map((e) => `"${e}"`);
            let csvData = "";

            csvData += `${colHeaders.join(",")},\r\n`;

            colData.forEach((el) => {
              Object.values(el).forEach((v) => {
                csvData += `"${v}",`;
              });

              csvData += "\r\n";
            });
            this.downloadCsv(`${ws.name}.csv`, csvData);
            this.$emit("isLoading", false);
          });
        }
      });
    },
    downloadCsv: function (filename, data) {
      // https://ourcodeworld.com/articles/read/189/how-to-create-a-file-and-generate-a-download-with-javascript-in-the-browser-without-a-server
      var element = document.createElement("a");
      element.setAttribute(
        "href",
        "data:text/plain;charset=utf-8," + encodeURIComponent(data)
      );
      element.setAttribute("download", filename);

      element.style.display = "none";
      document.body.appendChild(element);

      element.click();

      document.body.removeChild(element);
    },
    downloadExcel: async function (ws) {
      this.$emit("isLoading", true);

      this.$props.db.worksheets.forEach((sheet) => {
        if (sheet.name == ws.name) {
          sheet.getUnderlyingTablesAsync().then((logicalTables) => {
            sheet
              .getUnderlyingTableDataAsync(logicalTables[0].id)
              .then((tblData) => {
                let colNames = tblData.columns.map((e) => e.fieldName);
                let colData = [];
                tblData.data.forEach((el) => {
                  var obj = {};
                  for (let [index, col] of colNames.entries()) {
                    obj[col] = el[index].value;
                  }
                  colData.push(obj);
                });
                var newWs = XLSX.utils.json_to_sheet(colData);
                var newWb = XLSX.utils.book_new();
                XLSX.utils.book_append_sheet(newWb, newWs, ws.name);
                XLSX.writeFile(newWb, `${ws.name}.xlsx`);
                this.$emit("isLoading", false);
              });
          });
        }
      });
    },
  },
};
</script>

<style scoped>
.obj-div {
  cursor: pointer;
  border-radius: 4px;
}

.obj-div:hover {
  background-color: lightblue;
}

.sheet-div {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

::v-deep .v-input--dense > .v-input__control > .v-input__slot {
  margin: 0 !important;
}

::v-deep .v-input--selection-controls {
  padding: 0px !important;
  margin: 0 !important;
}

::v-deep .v-messages {
  display: none;
}
</style>
