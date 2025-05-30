<template>
  <div class="container">
    <h1>Excel file(s) to Database(s) Converter and Shapefiles/Raster to GeoPackage Converter</h1>

    <input type="file" multiple @change="handleFileUpload" class="file-input" />

    <div class="section">
      <label>
        Database Mapping:
        <span class="tooltip">❓
          <span class="tooltiptext">
            Define which Excel files and sheets go into which SQLite database (.db3).
            You can reuse the same file across databases. Leave "Sheets" blank to automatically include sheets not
            already used in prior databases.
            <br /><br />
            Example: db1.db3 uses Sheet1 and Sheet3 from file1.xlsx.
            db2.db3 includes the remaining unused sheets from file1.xlsx or file2.xlsx.
          </span>
        </span>
      </label>
      <div v-for="(db, dbIndex) in mappingForm" :key="dbIndex" class="db-box">
        <button @click="removeDatabaseEntry(dbIndex)" class="delete-button">✕</button>
        <input v-model="db.name" placeholder="Database Name" class="text-input" />
        <div v-for="(sheetEntry, fileIndex) in db.files" :key="fileIndex" class="sheet-box">
          <button @click="removeFileEntry(dbIndex, fileIndex)" class="delete-button">✕</button>
          <select v-model="sheetEntry.filename" class="text-input">
            <option disabled value="">Select File</option>
            <option v-for="file in availableFilesPerDB[dbIndex][fileIndex]" :key="file.name" :value="file.name">
              {{ file.name }}
            </option>
          </select>
          <span>:</span>
          <input v-model="sheetEntry.sheets" placeholder="Sheets (comma-separated)" class="text-input ml" />
        </div>
        <button @click="addFileEntry(db)" class="add-button">+ Add File Entry</button>
      </div>
      <button @click="addDatabaseEntry" class="add-button">+ Add Database</button>
    </div>

    <div class="section">
      <label>
        Header Mapping:
        <span class="tooltip">❓
          <span class="tooltiptext">
            Specify which row to treat as column headers for each sheet.
            Use "default" for most sheets, or provide sheet-specific overrides.
            <br /><br />
            Example: default = 2, Sheet5 = 1<br />
            This will use row 2 for most sheets, but row 1 for Sheet5.
          </span>
        </span>
      </label>
      <div v-for="(entry, index) in headerMapping" :key="index" class="sheet-box">
        <button @click="removeHeaderMappingEntry(index)" class="delete-button">✕</button>
        <input v-model="entry.sheet" placeholder="Sheet Name" class="text-input" />
        <span>:</span>
        <input v-model.number="entry.row" placeholder="Header Row (1-based)" type="number" class="text-input ml" />
      </div>
      <button @click="addHeaderMappingEntry" class="add-button">+ Add Header Rule</button>
    </div>

    <div class="section">
      <label>
        Merged Mapping:
        <span class="tooltip">❓
          <span class="tooltiptext">
            Used to fill down merged cells or partially empty columns in specific sheets.
            Set how many of the left-most columns to auto-forward fill for merged cell correction.
            You can also list specific columns by name to forward-fill (e.g., repeated labels or categories).
            <br /><br />
            Example: sheet_name = "Sheet1" merged_columns = 2, columns = ["Country"]<br />
            This will forward-fill the first two columns from the left and also fill the "Country" column only in
            Sheet1.
          </span>
        </span>
      </label>
      <div v-for="(entry, index) in mergedMapping" :key="index" class="db-box">
        <button @click="removeMergedMappingEntry(index)" class="delete-button">✕</button>
        <input v-model="entry.sheet" placeholder="Sheet Name" class="text-input" />
        <span>:</span>
        <input v-model.number="entry.merged_columns" placeholder="Auto-fill Left Columns" type="number"
          class="text-input ml" />
        <input v-model="entry.columns" placeholder="Column Names (comma-separated)" class="text-input ml" />
      </div>
      <button @click="addMergedMappingEntry" class="add-button">+ Add Merged Rule</button>
    </div>

    <button @click="submitForm" class="submit-button">Submit</button>

    <div v-if="response" class="response">
      <h2>Response</h2>
      <div v-if="response.error" class="error-message">
        <div><strong>Status:</strong> {{ response.status || "Unknown" }}</div>
        <div><strong>Message:</strong> {{ response.error }}</div>
      </div>
      <ul v-else>
        <li v-for="(path, dbname) in response" :key="dbname">
          <strong>{{ dbname }}</strong>: {{ path }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      files: [],
      mappingForm: [
        {
          name: "PMs.db3",
          files: [{ filename: "", sheets: "1. EOF P Reductions,2. Performance Measures" }]
        },
        {
          name: "BMP.db3",
          files: [{ filename: "", sheets: "" }]
        },
        {
          name: "GeoDB.gpkg",
          files: [{ filename: "", sheets: "" }]
        },
        {
          name: "WQ.db3",
          files: [{ filename: "", sheets: "" }]
        }
      ],
      headerMapping: [{ sheet: "default", row: 4 },
      { sheet: "1. EOF P Reductions", row: 3 },
      { sheet: "Sheet1", row: 3 },
      ],
      mergedMapping: [{
        sheet: "1. EOF P Reductions",
        merged_columns: 4
      },
      {
        sheet: "2. Performance Measures",
        merged_columns: 1,
      }],
      response: null,
    };
  },
  computed: {
    availableFilesPerDB() {
      return this.mappingForm.map(db => {
        return db.files.map(currentEntry => {
          // Filter out files that are already selected in the current database
          // and also filter out the current entry to avoid duplicates
          const selectedFilenames = db.files
            .filter(entry => entry !== currentEntry)
            .map(entry => entry.filename);
          return this.files.filter(
            file =>
              !selectedFilenames.includes(file.name) ||
              file.name === currentEntry.filename
          );
        });
      });
    }
  },
  methods: {
    handleFileUpload(event) {
      const newFiles = Array.from(event.target.files);
      this.files = [...this.files, ...newFiles];
      this.autoSelectFiles();
    },
    removeDatabaseEntry(index) {
      this.mappingForm.splice(index, 1);
    },
    removeFileEntry(dbIndex, fileIndex) {
      this.mappingForm[dbIndex].files.splice(fileIndex, 1);
    },
    removeHeaderMappingEntry(index) {
      this.headerMapping.splice(index, 1);
    },
    removeMergedMappingEntry(index) {
      this.mergedMapping.splice(index, 1);
    },
    autoSelectFiles() {
      this.mappingForm.forEach(db => (db.files = []));

      this.files.forEach(file => {
        const name = file.name.toLowerCase();
        // Check if the file name contains specific keywords to determine the database
        if (name.includes("performance measures")) {
          // Assign to PMs.db3 or BMP.db3
          this.mappingForm.forEach(db => {
            if (db.name === "PMs.db3" || db.name === "BMP.db3") {
              db.files.push({
                filename: file.name,
                sheets: db.name === "PMs.db3" ? "1. EOF P Reductions,2. Performance Measures" : ""
              });
            }
          });
        }
        if (name.includes("water quality")) {
          // Assign to WQ.db3
          const wqDb = this.mappingForm.find(db => db.name === "WQ.db3");
          if (wqDb) wqDb.files.push({ filename: file.name, sheets: "" });
        }

        if ([".shp", ".tif", ".geojson", ".gpkg", ".shx", ".dbf", ".cpg", ".prj", ".sbn", ".sbx"].some(ext => name.endsWith(ext))) {
          // Assign to GeoDB.gpkg
          const geoDb = this.mappingForm.find(db => db.name === "GeoDB.gpkg");
          if (geoDb) geoDb.files.push({ filename: file.name, sheets: "" });
        }
      });
    },
    addDatabaseEntry() {
      this.mappingForm.push({ name: "", files: [] });
    },
    addFileEntry(db) {
      if (db.name === "PMs.db3") {
        db.files.push({ filename: "", sheets: "1. EOF P Reductions,2. Performance Measures" });
      } else {
        db.files.push({ filename: "", sheets: "" });
      }
    },
    addHeaderMappingEntry() {
      this.headerMapping.push({ sheet: "", row: 0 });
    },
    addMergedMappingEntry() {
      this.mergedMapping.push({ sheet: "", merged_columns: 0, columns: "" });
    },
    buildMappingFromList(mappingList) {
      const mapping = {};
      for (const db of mappingList) {
        mapping[db.name] = {};
        for (const file of db.files) {
          mapping[db.name][file.filename] = file.sheets
            ? file.sheets.split(",").map(s => s.trim())
            : [];
        }
      }
      return mapping;
    },
    async submitForm() {
      const geoDb = this.mappingForm.find(db => db.name === "GeoDB.gpkg");
      const otherDbs = this.mappingForm.filter(db => db.name !== "GeoDB.gpkg");

      // Submit standard Excel-to-DB conversion
      if (otherDbs.length > 0) {
        const formData = new FormData();
        this.files.forEach(file => formData.append("files", file));

        const headerMappingJson = {};
        this.headerMapping.forEach(e => {
          if (e.sheet) headerMappingJson[e.sheet] = Number(e.row);
        });

        const mergedMappingJson = {};
        this.mergedMapping.forEach(e => {
          if (e.sheet) {
            mergedMappingJson[e.sheet] = {
              merged_columns: Number(e.merged_columns),
              columns: e.columns ? e.columns.split(",").map(c => c.trim()) : []
            };
          }
        });

        formData.append("mapping", JSON.stringify(this.buildMappingFromList(otherDbs)));
        formData.append("header_mapping", JSON.stringify(headerMappingJson));
        formData.append("merged_mapping", JSON.stringify(mergedMappingJson));

        try {
          const response = await axios.post(`${import.meta.env.VITE_API_BASE_URL}/api/convert_excels_to_db`, formData, {
            headers: { "Content-Type": "multipart/form-data" }
          });
          this.response = response.data;
        } catch (error) {
          this.response = {
            error: error.response.data?.error || error.response.statusText || "An error occurred",
            status: error.response.status
          };
          return;
        }
      }

      // Submit GeoDB.gpkg files separately
      if (geoDb && geoDb.files.length > 0) {
        const geoForm = new FormData();
        geoDb.files.forEach(f => {
          const file = this.files.find(uploaded => uploaded.name === f.filename);
          if (file) geoForm.append("files", file);
        });

        try {
          const geoResp = await axios.post(`${import.meta.env.VITE_API_BASE_URL}/api/convert_to_gpkg`, geoForm, {
            headers: { "Content-Type": "multipart/form-data" }
          });

          if (!this.response) this.response = {};
          this.response["GeoDB.gpkg"] = geoResp.data;
        } catch (error) {
          if (!this.response) this.response = {};
          this.response["GeoDB.gpkg"] = {
            error: error.response.data?.error || error.response.statusText || "An error occurred",
            status: error.response.status
          };
        }
      }
    },
  }
};
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: 20px auto;
  padding: 20px;
  font-family: Arial, sans-serif;
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 8px;
  color: black;
}

h1 {
  font-size: 24px;
  margin-bottom: 20px;
}

.section {
  margin-bottom: 20px;
}

.text-input {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: calc(50% - 10px);
  margin-bottom: 5px;
}

.ml {
  margin-left: 10px;
}

.textarea {
  width: 100%;
  height: 100px;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-family: monospace;
}

.error-message {
  color: red;
  font-weight: bold;
  margin-bottom: 10px;
}

.delete-button {
  background: none;
  border: none;
  color: red;
  font-weight: bold;
  cursor: pointer;
}

.file-input {
  margin-bottom: 20px;
}

.db-box {
  background: white;
  padding: 10px;
  border: 1px solid #ccc;
  margin-top: 10px;
  border-radius: 4px;
}

.sheet-box {
  display: flex;
  margin-top: 5px;
}

.add-button {
  background: none;
  border: none;
  color: #007bff;
  cursor: pointer;
  margin-top: 5px;
}

.submit-button {
  background-color: #007bff;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.response {
  margin-top: 20px;
  background: #e9ecef;
  padding: 10px;
  border-radius: 4px;
}

.tooltip {
  margin-left: 6px;
  position: relative;
  display: inline-block;
  cursor: pointer;
  font-weight: bold;
}

.tooltip .tooltiptext {
  visibility: hidden;
  width: 280px;
  background-color: #333;
  color: white;
  text-align: left;
  border-radius: 6px;
  padding: 8px;
  position: absolute;
  z-index: 1;
  bottom: 125%;
  left: 50%;
  margin-left: -140px;
  opacity: 0;
  transition: opacity 0.3s;
  font-size: 12px;
  line-height: 1.4;
}

.tooltip:hover .tooltiptext {
  visibility: visible;
  opacity: 1;
}
</style>