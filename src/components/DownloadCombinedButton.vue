<template>
  <div class="px-2 pt-3">
    <div>
      <v-btn
        class="white--text"
        color="#4E79A7"
        height="34px"
        width="100%"
        elevation="0"
        style="font-size: 9pt"
        v-on:click="downloadCombinedExcel()"
      >
        <v-icon color="white" v-on:click="downloadExcel(ws)" class="pr-2"
          >mdi-microsoft-excel</v-icon
        >
        Export All
      </v-btn>
    </div>
  </div>
</template>

<script>
import * as XLSX from "xlsx";

export default {
  props: { db: Object },
  methods: {
    downloadCombinedExcel: async function () {
      // Set loader by emitting event to parent
      this.$emit("isLoading", true);

      const db = this.$props.db;

      const wb = await this.createCombinedExcel();
      XLSX.writeFile(wb, `${db.name}.xlsx`);

      // Set loader by emitting event to parent
      this.$emit("isLoading", false);
    },
    createCombinedExcel: async function () {
      // Create a new workbook using the SheetJS library
      // https://www.npmjs.com/package/xlsx

      const newWb = XLSX.utils.book_new();

      // Loop through each worksheet in Tableau dashboard
      for (const sheet of this.$props.db.worksheets) {
        let colData = [];
        let newWs = null;

        // Get sheet's underlying tables, then the logical table's data
        await sheet.getUnderlyingTablesAsync().then(async (logicalTables) => {
          await sheet
            .getUnderlyingTableDataAsync(logicalTables[0].id)
            .then((tblData) => {
              let colNames = tblData.columns.map((e) => e.fieldName);

              // Loop through table data and create simplified JSON object for exporting
              for (let el of tblData.data) {
                var obj = {};
                for (let [index, col] of colNames.entries()) {
                  obj[col] = el[index].value;
                }
                colData.push(obj);
              }

              // Create Excel sheet from JSON
              newWs = XLSX.utils.json_to_sheet(colData);

              // Append Excel sheet to workbook
              XLSX.utils.book_append_sheet(newWb, newWs, sheet.name);
            });
        });
      }
      return newWb;
    },
  },
};
</script>

<style></style>
