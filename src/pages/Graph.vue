<template>
    <!-- Main Content -->
    <div :class="[theme, 'content']" :style="{ height: heightVar() }">

        <!-- Component 1: Folder Navigation -->
        <div class="folder-navigation">
            <DatabaseDropdown />
        </div>

        <!-- Component 2: Column Navigation -->
        <div class="column-navigation">
            <ColumnDropdown :selectedDbsTables="selectedDbsTables" />
        </div>
        <div class="right-panel">
            <!-- Component 3: Selection, Interval, Aggregation, and Export Config -->
            <div class="settings-panel">
                <Selection />
                <IntervalDropdown />
                <StatisticsDropdown />
                <ExportConfig />
                <span>
                    <ExportTableStats />
                    <button @click="fetchData">Fetch Graph</button>
                    <button @click="exportData">Export Graph</button>
                    <!-- Button to open the filter popup -->
                    <button @click="showFilterPopup = true">
                        Filter Columns
                    </button>
                </span>
                <!-- Filter Popup -->
                <div v-if="showFilterPopup" class="filter-popup-overlay" @click.self="showFilterPopup = false">
                    <div class="filter-popup">
                        <h3>Filter Columns</h3>
                        <div v-for="col in selectedColumns" :key="col" class="filter-row">
                            <label :for="'filter-' + col">{{ col }}:</label>
                            <input :id="'filter-' + col" v-model="columnFilters[col]"
                                placeholder="Enter values, comma separated" class="filter-input" />
                        </div>
                        <div class="popup-actions">
                            <button @click="applyFilters">Apply</button>
                            <button @click="showFilterPopup = false">Close</button>
                        </div>
                    </div>
                </div>
            </div>
            <!-- Component 4: Main View -->
            <div class="main-view">
                <!-- Graph Display -->
                <GraphDisplay :data="filteredData"
                    :selectedColumns="onlyShowSelected ? [onlyShowSelected] : selectedColumns"
                    :selectedIds="selectedIds" :dateType="dateType" :ID="idColumn" :theme="theme"
                    :refreshKey="refreshKey" :currentZoomStart="currentZoomStart" :currentZoomEnd="currentZoomEnd"
                    :multiGraphType="multiGraphType" :graphType="graphType" />

            </div>
        </div>
    </div>
</template>

<script>
import { mapState, mapActions } from "vuex";
import DatabaseDropdown from "../components/DatabaseDropdown.vue";
import ColumnDropdown from "../components/ColumnDropdown.vue";
import Selection from "../components/Selection.vue";
import IntervalDropdown from "../components/IntervalDropdown.vue";
import StatisticsDropdown from "../components/StatisticsDropdown.vue";
import ExportConfig from "../components/ExportConfig.vue";
import ExportTableStats from "../components/ExportTableStats.vue";
import GraphDisplay from "../components/GraphDisplay.vue";
import axios from 'axios';

export default {
    name: "Graph",
    components: {
        DatabaseDropdown,
        ColumnDropdown,
        Selection,
        IntervalDropdown,
        StatisticsDropdown,
        ExportConfig,
        ExportTableStats,
        GraphDisplay,
    },
    data() {
        return {
            stats: [],
            statsColumns: [],
            data: [],
            refreshKey: 0,
            onlyShowSelected: null,
            showFilterPopup: false,
            columnFilters: {},
            filteredData: [],
        };
    },
    watch: {
        data: {
            immediate: true,
            handler() {
                this.applyFilters();
            }
        },
        selectedColumns: {
            immediate: true,
            handler(newCols) {
                // Initialize filters for new columns
                newCols.forEach(col => {
                    if (!(col in this.columnFilters)) this.columnFilters[col] = '';
                });
                this.applyFilters();
            }
        }
    },
    computed: {
        ...mapState(["selectedDbsTables", "selectedColumns", "allSelectedColumns", "idColumn", "mathFormula", "multiGraphType", "currentZoomStart", "currentZoomEnd", "selectedIds", "dateRange", "selectedInterval", "selectedStatistics", "selectedMethod", "exportColumns", "graphType", "exportIds", "exportDate", "exportInterval", "dateType", "exportPath", "exportFilename", "exportFormat", "exportOptions", "theme"]),
    },
    methods: {
        ...mapActions(["updateSelectedColumns", "updateExportOptions", "updateAllSelectedColumns", "pushMessage", "clearMessages"]),
        heightVar() {
            // Set the height based on the environment
            const isTauri = !!window.__TAURI__;
            return isTauri ? "calc(100vh - 14vh)" : "calc(100vh - 16vh)";
        },
        // Fetch data from the API
        async fetchData() {
            try {
                // Choose all the columns if they are not selected
                if (this.selectedColumns.length === 0) {
                    this.updateSelectedColumns("All");
                    this.updateAllSelectedColumns(true);
                }

                this.pushMessage({ message: `Graph Loading`, type: 'info' });

                // Fetch the data for the map
                const response = await axios.get(`${import.meta.env.VITE_API_BASE_URL}/api/get_data`, {
                    params: {
                        db_tables: JSON.stringify(this.selectedDbsTables),
                        columns: JSON.stringify(this.allSelectedColumns ? "All" : this.selectedColumns.filter((column) => column !== 'Season')),
                        id: JSON.stringify(this.selectedIds),
                        id_column: this.idColumn,
                        start_date: this.dateRange.start,
                        end_date: this.dateRange.end,
                        date_type: this.dateType,
                        interval: this.selectedInterval,
                        statistics: JSON.stringify(this.selectedStatistics),
                        method: JSON.stringify(this.selectedMethod),
                        math_formula: this.mathFormula,
                    },
                    headers: {
                        Authorization: `Bearer ${localStorage.getItem("token")}`
                    }
                });
                // Check if the new feature is added to columns
                if (response.data.new_feature && !this.selectedColumns.includes(response.data.new_feature)) {
                    this.updateSelectedColumns(this.selectedColumns.concat([response.data.new_feature]));
                    this.onlyShowSelected = response.data.new_feature;
                }
                if (this.selectedInterval === 'seasonally' && !this.selectedMethod.includes('Equal') && !this.selectedColumns.includes('Season')) {
                    this.updateSelectedColumns(this.selectedColumns.concat(['Season']));
                } else if (this.selectedColumns.includes('Season') && this.selectedInterval !== 'seasonally') {
                    this.updateSelectedColumns(this.selectedColumns.filter((column) => column !== 'Season'));
                }
                this.data = response.data.data;
                this.stats = response.data.stats;
                this.statsColumns = response.data.statsColumns;
                this.applyFilters();
                this.refreshKey += 1;
                if (response.data.error) {
                    alert('Error fetching data: ' + response.data.error);
                    return;
                } else {
                    this.$nextTick(() => {
                        this.pushMessage({ message: `Graph Loaded ${this.selectedColumns.length} columns x ${this.data.length} rows`, type: 'success' });
                    });

                }
            } catch (error) {
                console.error('Error fetching data: ', error.message);
            }
        },
        // Export data to a file
        async exportData() {
            try {
                const response = await axios.get(`${import.meta.env.VITE_API_BASE_URL}/api/export_data`, {
                    params: {
                        db_tables: JSON.stringify(this.selectedDbsTables),
                        columns: JSON.stringify(this.allSelectedColumns ? "All" : this.exportColumns.filter((column) => column !== 'Season')),
                        id: JSON.stringify(this.exportIds),
                        id_column: this.idColumn,
                        start_date: this.exportDate.start,
                        end_date: this.exportDate.end,
                        interval: this.exportInterval,
                        statistics: JSON.stringify(this.selectedStatistics),
                        method: JSON.stringify(this.selectedMethod),
                        date_type: this.dateType,
                        export_path: this.exportPath,
                        export_filename: this.exportFilename,
                        export_format: this.exportFormat,
                        options: JSON.stringify(this.exportOptions),
                        graph_type: this.graphType,
                        multi_graph_type: JSON.stringify(this.multiGraphType),
                        math_formula: this.mathFormula,
                    },
                    headers: {
                        Authorization: `Bearer ${localStorage.getItem("token")}`
                    },
                    responseType: 'blob'
                });
                if (response.data.error) {
                    alert('Error fetching data: ' + response.data.error);
                    return;
                } else { this.pushMessage({ message: `Exported ${this.exportColumns.length} columns x ${this.data.length} rows`, type: 'success' }); }
                if (this.selectedInterval === 'seasonally' && !this.selectedMethod.includes('Equal') && !this.selectedColumns.includes('Season')) {
                    this.updateSelectedColumns(this.selectedColumns.concat(['Season']));
                } else if (this.selectedColumns.includes('Season') && this.selectedInterval !== 'seasonally') {
                    this.updateSelectedColumns(this.selectedColumns.filter((column) => column !== 'Season'));
                }

                if (!window.__TAURI__) {
                    // Download the file using the browser as a blob
                    const blob = new Blob([response.data], { type: response.headers['content-type'] });
                    const link = document.createElement('a');
                    const url = URL.createObjectURL(blob);
                    link.href = url;
                    link.setAttribute('download', this.exportFilename);
                    document.body.appendChild(link);
                    link.click();
                    document.body.removeChild(link);
                    URL.revokeObjectURL(url);
                }
            } catch (error) {
                console.error('Error exporting data: ', error.message);
            }
        },
        applyFilters() {
            // Filter this.data using columnFilters and selectedColumns
            if (!this.data || !Array.isArray(this.data)) {
                this.filteredData = [];
                return;
            }
            this.filteredData = this.data.filter(row => {
                return this.selectedColumns.every(col => {
                    const filter = this.columnFilters[col];
                    if (!filter) return true;
                    const filterValues = filter.split(",").map(f => f.trim().toLowerCase()).filter(f => f);
                    if (filterValues.length === 0) return true;
                    const cellValue = String(row[col] ?? "").toLowerCase();
                    return filterValues.some(fv => cellValue.includes(fv));
                });
            });
            this.showFilterPopup = false;
        },
    },
};
</script>
<style src="../assets/pages.css"></style>
<style scoped>
.filter-popup-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.3);
    z-index: 1000;
    display: flex;
    align-items: center;
    justify-content: center;
}

.filter-popup {
    background: var(--bg-color, #fff);
    color: var(--text-color, #222);
    border-radius: 8px;
    padding: 24px 32px;
    min-width: 320px;
    box-shadow: 0 2px 16px rgba(0, 0, 0, 0.18);
}

.filter-row {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
}

.filter-row label {
    min-width: 90px;
    font-weight: bold;
}

.filter-input {
    flex: 1;
    padding: 6px 8px;
    border: 1px solid #bbb;
    border-radius: 4px;
    margin-left: 8px;
}

.popup-actions {
    display: flex;
    justify-content: flex-end;
    gap: 10px;
    margin-top: 18px;
}
</style>