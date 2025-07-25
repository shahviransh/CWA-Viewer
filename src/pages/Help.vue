<template>
    <div :class="[theme, 'help-container']">
        <h1>NutriView Help Page</h1>

        <nav class="toc">
            <h2>Table of Contents</h2>
            <ul>
                <li><a href="#overview">Overview</a></li>
                <li><a href="#common-concepts">Common Concepts</a></li>
                <li><a href="#project-table-section">Project / Table Section</a></li>
                <li><a href="#calculator">Calculator</a></li>
                <li><a href="#graph-section">Graph Section</a></li>
                <li><a href="#map-section">Map Section</a></li>
                <li><a href="#converter-section">Converter</a></li>
                <li><a href="#guest-permissions-section">Guest Permissions</a></li>
                <li><a href="#desktop-vs-web">Desktop App vs. Web App</a></li>
                <li><a href="#troubleshooting">Troubleshooting & Common Issues</a></li>
                <li><a href="#contact-support">Contact & Support</a></li>
            </ul>
        </nav>

        <section id="overview">
            <h2>Overview</h2>
            <p>
                The NutriView is a tool designed for exploring, analyzing, and visualizing environmental model data. It
                supports both tabular and geospatial data formats and provides features for data filtering,
                aggregation,
                calculation, graphing, mapping, and exporting.
            </p>
            <p>
                The application is organized into several main sections accessible via the top taskbar:
                <strong>Project/Table</strong>, <strong>Graph</strong>, and <strong>Map</strong>.
            </p>
            <p>
                NutriView is a new CWA in-house developed software tool that helps manage and utilize the large volume
                of data related to implementation of best management practices (BMPs) supported by a $49.4 million
                (2024-2028) federal investment under the Lake Erie Precision Conservation sub-stream of the Great
                Lakes Freshwater Ecosystem Initiative (FEI).
            </p>
            <p>
                These data include headline program performance measures and detailed information on thousands of BMPs
                implemented.
            </p>
            <p>
                Once located on the Cloud, the tool will allow any permitted CWA staff to visually explore the data,
                dynamically produce maps and track trends including the transition to the precision conservation
                approach to nutrient management.
            </p>
            <p>
                In addition, CWA will be able to efficiently share data with selected and permitted members of the
                research community starting with ECCC S&T. This will enhance their related research and modelling
                efforts and further advise CWA on future programming decisions to restore and protect Lake Erie water
                quality.
            </p>
        </section>

        <section id="common-concepts">
            <h2>Common Concepts</h2>

            <h4>Folder Selection & Navigation (Left Panel)</h4>
            <ul>
                <li>The leftmost panel displays a tree structure of your selected model folder.</li>
                <li>Click the <strong>📁 Select Folder</strong> button to choose the root folder containing your
                    IMWEBs
                    model data.
                    <ul>
                        <li><strong>Desktop App:</strong> Opens a system dialog to select a folder directly on your
                            computer.</li>
                        <li><strong>Web App:</strong> Opens a dialog to select and upload a folder from your
                            computer to
                            the server. You'll see an upload progress indicator.</li>
                    </ul>
                </li>
                <li>The panel shows folders and supported files: Databases (<code>.db3, .gpkg</code>), Shapefiles
                    (<code>.shp</code>), and Rasters (<code>.tif</code>/<code>.tiff</code>).</li>
                <li>Click on folders (📁) or databases (🗄️) to expand or collapse them.</li>
                <li>Click on database files (<code>.db3, .gpkg</code>) to fetch and display the tables/layers within
                    them in the tree.
                </li>
                <li>Click on individual tables (📑) or geospatial files (📄) to select them for analysis in the
                    current
                    view (Project/Table, Graph, or Map). Selected items are highlighted. Click again to deselect.
                </li>
                <li>Multiple files across different databases or folders can be selected simultaneously.</li>
            </ul>

            <h4>Lookup Tables (Human-Readable Names)</h4>
            <ul>
                <li>The application can use a <code>lookup.db3</code> file within your selected project's
                    <code>Database</code> folder to display more user-friendly names (aliases) for tables and
                    columns.
                </li>
                <li>This lookup database needs specific tables (e.g., `scenario_2`, `BMP`, `Hydroclimate`)
                    containing
                    columns: <code>Table Name</code>, <code>Table Alias</code>, <code>Column Name</code>,
                    <code>Column Alias</code>.
                </li>
                <li>If a `lookup.db3` is found and correctly formatted, aliases will be used in dropdowns and
                    displays
                    where possible. Calculations and exports often use the original database names for consistency.
                </li>
            </ul>

            <h4>Parameters & Export Columns (Middle Panel)</h4>
            <ul>
                <li><strong>Select Columns:</strong> This list shows available columns from the *currently selected*
                    database table(s). Use this to choose columns for viewing, analysis, or graphing in the *current
                    view*. You can select multiple columns.</li>
                <li><strong>Export Select Columns:</strong> This list mirrors the available columns. Use this
                    specifically to choose which columns will be included when you *export* data. This allows you to
                    export a different set of columns than what you are currently viewing.</li>
                <li>You can select multiple columns by clicking on them. Click again to deselect. Use Ctrl/Cmd +
                    Click
                    for non-contiguous selection or Shift + Click for a range.</li>
            </ul>

            <h4>Data Selection & Filtering (Settings Panel)</h4>
            <p>The middle-top panel contains controls to filter and configure the data you want to analyze or view:
            </p>
            <ul>
                <li><strong>Model Interval/Dates:</strong> Displays the time range (Start/End Date or Month/Year)
                    and
                    the native interval (Daily, Monthly, etc.) available in the selected data. These fields are
                    read-only.</li>
                <li><strong>Select IDs:</strong> Filter data by specific ID(s) (e.g., Subbasin ID, Reach ID). Select
                    one
                    or more IDs from the dropdown. Leave blank to include all IDs.</li>
                <li><strong>Select Date Range:</strong> Filter data by a specific start and end date/month/year.
                </li>
                <li><strong>Export Select IDs/Date Range:</strong> Similar to the above, but these filters apply
                    *only*
                    when exporting data, allowing you to export a different subset than currently viewed.</li>
                <li><strong>Select Interval:</strong> Choose the time interval for viewing/analyzing data (Daily,
                    Monthly, Yearly, Seasonally).</li>
                <li><strong>Export Select Interval:</strong> Choose the time interval for *exporting* data. Must be
                    equal to or coarser than the 'Select Interval' (e.g., if viewing Daily, you can export Daily,
                    Monthly, Yearly; if viewing Monthly, you can only export Monthly or Yearly).</li>
                <li><strong>Method Conversion:</strong> When viewing data at coarser intervals (Monthly, Yearly,
                    Seasonally), select how to aggregate the finer-grained data (Average, Sum, Minimum, Maximum).
                    You
                    *must* select at least one method if the selected interval is not 'Daily'. 'Equal' means no
                    aggregation (only works if view interval matches data's native interval).</li>
                <li><strong>Select Statistics:</strong> Choose statistics to calculate over the selected time range
                    and
                    interval (Average, Sum, Minimum, Maximum, Standard Deviation). 'None' calculates no statistics.
                    Statistics are displayed below the main data table.</li>
            </ul>

            <h4>Export Panel (Top Right)</h4>
            <ul>
                <li><strong>Export Path:</strong>
                    <ul>
                        <li><strong>Desktop App:</strong> Specify the directory path on your computer where exported
                            files will be saved. Defaults to a 'dataExport' folder within the application's internal
                            data directory (`{Nutri-View App Path}/_up_/backend/apppy/_internal/dataExport`). You
                            can change this to folders like Desktop, Downloads, or Documents.</li>
                        <li><strong>Web App:</strong> This field is ignored. Exported files will be offered as
                            downloads
                            through your browser.</li>
                    </ul>
                </li>
                <li><strong>Export Filename:</strong> Enter the desired base name for your exported file(s). The app
                    may
                    append details like interval or feature name depending on the export type.</li>
                <li><strong>Export Format:</strong> Choose the output format (e.g., CSV, TXT, XLSX, PNG, SHP).
                    Available
                    formats depend on the active section (Table, Graph, Map).</li>
                <li><strong>Export Table and/or Stats:</strong> Choose whether to include the main data table, the
                    calculated statistics table, or both in the export (available in Project/Table section for
                    CSV/TXT
                    formats).</li>
            </ul>
        </section>

        <section id="project-table-section">
            <h2>Project / Table Section</h2>
            <p>These sections focus on viewing and exporting tabular data.</p>

            <h4>Fetching and Viewing Data</h4>
            <ol>
                <li>Use the <strong>Folder Selection Panel</strong> to navigate and select one or more database
                    tables
                    (📑).</li>
                <li>Use the <strong>Parameters Panel</strong> (Select Columns) to choose the specific columns you
                    want
                    to view.</li>
                <li>Use the <strong>Settings Panel</strong> to filter by IDs, date range, and select the desired
                    viewing
                    interval and aggregation method.</li>
                <li>Click the <strong>Fetch Data</strong> button.</li>
                <li>The main view will display the filtered and aggregated data in a table. If the data exceeds 100
                    rows, a "Load More" button appears.</li>
                <li>If statistics were selected, a second table displaying the calculated statistics will appear
                    below
                    the main data table.</li>
            </ol>

            <h4>Exporting Tabular Data</h4>
            <ol>
                <li>Fetch the data you want to export first using the steps above.</li>
                <li>Use the <strong>Export Select Columns</strong> list to choose the specific columns for the
                    export
                    file.</li>
                <li>Use the <strong>Export Settings Panel</strong> (Export Select IDs, Export Date Range, Export
                    Select
                    Interval) to apply filters specific to the export, if different from the view filters.</li>
                <li>Configure the <strong>Export Panel</strong> (Export Path [Desktop only], Export Filename, Export
                    Format [CSV, TXT, XLSX], Export Table and/or Stats).</li>
                <li>Click the <strong>Export Data</strong> button.</li>
                <li><strong>Desktop App:</strong> The file(s) will be saved to the specified Export Path.</li>
                <li><strong>Web App:</strong> The file will be downloaded via your browser.</li>
            </ol>
        </section>

        <section id="calculator">
            <h2>Calculator ➕➖✖️➗</h2>
            <p>The calculator allows you to perform mathematical operations on numerical columns or create new
                columns
                based on formulas.</p>

            <h4>Accessing the Calculator</h4>
            <ul>
                <li>Click the <strong>Calculator</strong> button in the top taskbar.</li>
            </ul>

            <h4>Modes</h4>
            <ul>
                <li><strong>Auto Mode:</strong> Applies a simple operation (Number + Operator or Operator only) to
                    *all*
                    currently selected numerical columns (excluding ID and Time). Updates existing columns or
                    creates
                    new ones depending on the operation.</li>
                <li><strong>Manual Mode:</strong> Allows you to build a custom formula using selected columns,
                    numbers,
                    and operators (+, -, *, /). Creates a *new* column with the result.</li>
            </ul>

            <h4>Usage</h4>
            <ol>
                <li>Select the desired mode (Auto or Manual).</li>
                <li><strong>Auto Mode:</strong>
                    <ul>
                        <li>Optionally, enter a number using the number buttons.</li>
                        <li>Click an operator (+, -, *, /). The preview shows the operation applied to all selected
                            columns (using original database names).</li>
                        <li>Click '=' to apply the changes and add/update the columns in the current view.</li>
                    </ul>
                </li>
                <li><strong>Manual Mode:</strong>
                    <ul>
                        <li>Click column names from the "Selected Columns" list, numbers, and operators to build
                            your
                            formula in the preview area.</li>
                        <li>Use the '⌫' button for backspace.</li>
                        <li>Click '=' to save the formula. A new column named after the formula (using original
                            database
                            names) will be added to the view upon the next "Fetch Data" or "Fetch Graph".</li>
                    </ul>
                </li>
                <li>Formulas are saved per session. Click the 'X' or outside the popup to close. Click '=' to save
                    and
                    close.</li>
                <li><strong>Division by Zero:</strong> The calculator automatically handles potential division by
                    zero
                    by replacing zero values in the denominator column with a very small number (0.001) during
                    calculation.</li>
            </ol>
        </section>

        <section id="graph-section">
            <h2>Graph Section 📊</h2>
            <p>This section allows you to visualize tabular data as various types of graphs.</p>

            <h4>Creating Graphs</h4>
            <ol>
                <li>Use the <strong>Folder Selection Panel</strong> to select one or more database tables (📑).</li>
                <li>Use the <strong>Parameters Panel</strong>:
                    <ul>
                        <li>Select the time column (e.g., 'Time', 'Date') for the <strong>X-Axis</strong>.</li>
                        <li>Select one or more numerical columns for the <strong>Y-Axis</strong>.</li>
                    </ul>
                </li>
                <li>Use the <strong>Settings Panel</strong> to filter by IDs, date range, interval, and aggregation
                    method (if needed).</li>
                <li>Select the desired <strong>Graph Type</strong> from the dropdown in the Export Panel (e.g.,
                    Scatter,
                    Line, Bar, or combinations like Bar & Scatter; <strong>Pie</strong> is available for non-time
                    series data).</li>
                <li>If using a combined graph type (e.g., Bar & Scatter), additional dropdowns will appear below the
                    Graph Type selector. Use these to assign specific Y-Axis columns to be displayed as Bars or
                    Scatters
                    (or Lines). *All selected Y-axis columns must be assigned.*</li>
                <li>Click the <strong>Fetch Graph</strong> button.</li>
                <li>The graph will be displayed in the main view.</li>
            </ol>

            <h4>Graph Features</h4>
            <ul>
                <li><strong>Tooltips:</strong> Hover over data points to see their exact values.</li>
                <li><strong>Legend:</strong> Click on legend items to toggle the visibility of specific data series.
                </li>
                <li><strong>Zoom & Pan:</strong> Use the data zoom slider at the bottom or mouse wheel/dragging
                    within
                    the plot area to zoom and pan. Use the "Zoom In", "Zoom Out", and "Reset Zoom" buttons in the
                    taskbar.</li>
                <li><strong>Secondary Y-Axis:</strong> Columns with significantly larger value ranges (typically >
                    100)
                    are automatically plotted against a secondary Y-axis on the right for better readability.</li>
            </ul>

            <h4>Exporting Graphs</h4>
            <ol>
                <li>Generate the graph you want to export.</li>
                <li>Configure the <strong>Export Panel</strong> (Export Path [Desktop only], Export Filename).</li>
                <li>Select the desired <strong>Export Format</strong>:
                    <ul>
                        <li><strong>Graph in Excel (XLSX):</strong> Exports an Excel file containing the graph
                            embedded
                            alongside the raw data used to create it.</li>
                        <li><strong>Image Formats (PNG, JPG, JPEG, SVG, PDF):</strong> Exports the graph as a static
                            image file.</li>
                    </ul>
                </li>
                <li>Click the <strong>Export Graph</strong> button.</li>
            </ol>
        </section>

        <section id="map-section">
            <h2>Map Section 🗺️</h2>
            <p>This section visualizes geospatial data (Shapefiles, Rasters) and can overlay tabular data onto
                Shapefiles.</p>

            <h4>Loading Geospatial Layers</h4>
            <ol>
                <li>Use the <strong>Folder Selection Panel</strong> to select one or more Shapefiles
                    (<code>.shp</code>)
                    or Raster files (<code>.tif</code>/<code>.tiff</code>).</li>
                <li>Click the <strong>Fetch Map</strong> button. A "Customize Map Style" popup appears.</li>
            </ol>

            <h4>Customizing Map Styles (Popup)</h4>
            <ul>
                <li><strong>Colors & Opacity:</strong> Adjust the default colors and opacity (0.0 to 1.0) for
                    Polygons,
                    Lines, and Points using the color pickers and sliders.</li>
                <li><strong>Selected Feature:</strong> If you have also selected a database table, choose a
                    numerical
                    column from that table here. The map polygons will be colored based on the values in this
                    feature
                    column.</li>
                <li><strong>Selected Feature Statistic:</strong> Choose how to aggregate the feature data if
                    multiple
                    data points correspond to a single polygon (Average, Sum, Min, Max).</li>
                <li><strong>Selected Spatial Scale:</strong> Select the type of geometry the *Shapefile* represents
                    (Subbasin, Subarea, Field, Reach). This helps link the Shapefile geometry to the correct ID in
                    the
                    tabular data. The application attempts to auto-detect this based on the filename, but manual
                    selection might be needed if filenames are non-standard.</li>
                <li>Click <strong>Confirm</strong>.</li>
            </ul>

            <h4>Viewing Maps</h4>
            <ul>
                <li>The map displays loaded layers over a base map (OpenStreetMap).</li>
                <li><strong>Shapefiles:</strong> Displayed as polygons, lines, or points.
                    <ul>
                        <li>If linked with tabular data via the "Selected Feature", polygons are colored according
                            to
                            the feature's aggregated value. A legend appears showing the color scale.</li>
                        <li>If not linked, they use the default style set in the popup.</li>
                        <li>Lines (Reach) are weighted (thicker lines for higher values) based on the selected
                            feature
                            data.</li>
                    </ul>
                </li>
                <li><strong>Rasters:</strong> Displayed as colored overlays. A legend shows the value ranges
                    corresponding to the colors, based on the raster's internal color map or a default.</li>
                <li><strong>Interaction:</strong>
                    <ul>
                        <li><strong>Hover:</strong> Hover over Shapefile features (polygons, lines, points) to see
                            their
                            attributes (from the <code>.dbf</code> file) in a tooltip.</li>
                        <li><strong>Click:</strong> Click on a Shapefile feature (polygon or line) to open a popup
                            showing the associated tabular data (from the selected <code>.db3</code> table) and a
                            graph
                            for that specific feature ID.</li>
                        <li><strong>Zoom/Pan:</strong> Use map controls (+/- buttons, mouse wheel, dragging).</li>
                        <li><strong>Fullscreen:</strong> Use the fullscreen button (top right).</li>
                        <li><strong>Scale Bar:</strong> A scale bar is shown (bottom left).</li>
                    </ul>
                </li>
                <li><strong>Layers:</strong> If multiple layers are loaded, they will overlay. Polygons render
                    first,
                    then lines, then points.</li>
            </ul>

            <h4>Exporting Maps</h4>
            <ol>
                <li>Generate and style the map view you want to export.</li>
                <li>Configure the <strong>Export Panel</strong> (Export Path [Desktop only], Export Filename).</li>
                <li>Select the desired <strong>Export Format</strong>:
                    <ul>
                        <li><strong>Map As Shapefiles (SHP):</strong> Exports a ZIP file containing modified
                            Shapefile(s). The attribute table (<code>.dbf</code>) of the exported Shapefile(s) will
                            include the aggregated data from the "Selected Feature" column. This allows using the
                            visualized data directly in GIS software. The filename automatically includes details
                            about
                            the shapefile, interval, feature, and statistic used.</li>
                        <li><strong>Image Formats (PNG, JPG, JPEG, PDF):</strong> Exports the current map view as a
                            static image. The export includes a North arrow. A ZIP file containing the map image and
                            separate images for each loaded geospatial layer (rendered with legends/styling) will be
                            generated.</li>
                    </ul>
                </li>
                <li>Click the <strong>Export Map</strong> button.</li>
            </ol>
        </section>

        <section id="converter-section">
            <h2>Converter</h2>
            <p>
                The Converter page allows you to upload and convert Excel files to SQLite databases (<code>.db3</code>),
                and shapefiles or rasters to GeoPackage (<code>.gpkg</code>) format. This helps prepare your data for
                use in NutriView's analysis and mapping tools.
            </p>
            <h4>How to Use</h4>
            <ol>
                <li>Go to the <strong>Converter</strong> tab in the top taskbar.</li>
                <li>Select one or more Excel or geospatial files to upload.</li>
                <li>Map Excel sheets to database tables and configure options as needed.</li>
                <li>Choose how to handle table conflicts (Replace or Append).</li>
                <li>Click <strong>Submit</strong> to upload and convert your files.</li>
                <li>Converted files will be available for use in other sections of NutriView.</li>
            </ol>
        </section>

        <section id="guest-permissions-section">
            <h2>Guest Permissions</h2>
            <p>
                The Guest Permissions page allows an administrator to control which actions guest users are allowed to
                perform in the application.
            </p>
            <h4>How to Use</h4>
            <ol>
                <li>Open the <strong>Guest Permissions</strong> page from the taskbar (admin login required).</li>
                <li>Toggle the checkboxes to enable or disable permissions such as <strong>read</strong>,
                    <strong>write</strong>, <strong>upload</strong>, and <strong>download</strong> for guest users.
                </li>
                <li>Click <strong>Save Changes</strong> to update the permissions.</li>
            </ol>
        </section>

        <section id="desktop-vs-web">
            <h2>Desktop App vs. Web App</h2>
            <p>While both versions offer the same core functionality, there are key differences:</p>
            <ul>
                <li><strong>Authentication:</strong> The Web App requires login with an email and password. The
                    Desktop
                    App does not require login.</li>
                <li><strong>Folder Access:</strong>
                    <ul>
                        <li><strong>Desktop App:</strong> You select folders directly from your local filesystem.
                            It's
                            available for installation from the <a
                                href="https://github.com/shahviransh/ECCC-IMWEBs-Viewer/releases/latest"
                                target="_blank">GitHub Releases page</a>.</li>
                        <li><strong>Web App:</strong> You *upload* folders to the server. It's accessible via a web
                            browser. Find the link on the project's main <a
                                href="https://github.com/shahviransh/ECCC-IMWEBs-Viewer" target="_blank">GitHub
                                page</a>
                            (check the About).</li>
                    </ul>
                </li>
                <li><strong>Exporting:</strong>
                    <ul>
                        <li><strong>Desktop App:</strong> You specify an "Export Path" on your local machine.</li>
                        <li><strong>Web App:</strong> Exported files are downloaded through your browser.</li>
                    </ul>
                </li>
            </ul>
        </section>

        <section id="troubleshooting">
            <h2>Troubleshooting & Common Issues</h2>
            <ul>
                <li><strong>Data Not Loading/Fetching Fails:</strong> Ensure the selected database
                    (<code>.db3</code>)
                    file is not corrupted and contains the expected tables and columns. Check if the file paths are
                    correct. Try refreshing the application (Right-click -> Refresh or Ctrl+R/Cmd+R).</li>
                <li><strong>Export Fails ("Permission Denied" / Files Not Saving) [Desktop App]:</strong> The
                    default
                    export location might be in a system-protected directory. Change the "Export Path" in the
                    top-right
                    panel to a user-writable location like your Desktop, Documents, or Downloads folder.</li>
                <li><strong>Incorrect Map Colors/Legend:</strong> Ensure you have selected the correct "Selected
                    Feature", "Selected Feature Statistic", and "Selected Spatial Scale" in the map customization
                    popup
                    ("Fetch Map").</li>
                <li><strong>Web App Issues:</strong> If experiencing problems with the web app, consider installing
                    and
                    using the Desktop App. Download links and installation instructions can be found on the <a
                        href="https://github.com/shahviransh/ECCC-IMWEBs-Viewer" target="_blank">project's GitHub
                        page</a>. Alternatively, contact the administrator using the "Contact Admin" button on the
                    Login
                    page.</li>
                <li><strong>Desktop App Issues:</strong> If experiencing installation or runtime problems with the
                    Desktop App, ensure you have downloaded the correct version for your operating system and
                    architecture (check the <a
                        href="https://github.com/shahviransh/ECCC-IMWEBs-Viewer#%EF%B8%8F-installation"
                        target="_blank">Installation Guide on GitHub</a>). You could also try accessing the Web App
                    (find the link on the <a href="https://github.com/shahviransh/ECCC-IMWEBs-Viewer"
                        target="_blank">GitHub page</a>) as an alternative.</li>
                <li><strong>General Issues:</strong> Sometimes, simply refreshing the application (Right-click ->
                    Refresh or Ctrl+R/Cmd+R) or restarting it can resolve temporary glitches.</li>
            </ul>
        </section>

        <section id="contact-support">
            <h2>Contact & Support</h2>
            <ul>
                <li><strong>Web App:</strong> Use the "Contact Admin" button on the login page for issues or
                    password
                    requests.</li>
                <li><strong>General Help/Bugs:</strong> Refer to the project's main <a
                        href="https://github.com/shahviransh/ECCC-IMWEBs-Viewer" target="_blank">GitHub
                        repository</a>,
                    particularly the README file and the <a
                        href="https://github.com/shahviransh/ECCC-IMWEBs-Viewer/issues" target="_blank">Issues
                        section</a>.</li>
            </ul>
        </section>
    </div>
</template>

<script>
import { mapState } from 'vuex';

export default {
    name: 'HelpPage',
    computed: {
        ...mapState(['theme'])
    }
}
</script>

<style scoped>
/* Theme variables */
.light {
    --bg-color: #f9f9f9;
    --text-color: #333;
    --header-color: #0056b3;
    --link-color: #007bff;
    --code-bg: #eee;
    --border-color: #ddd;
}

.dark {
    --bg-color: #2d2d2d;
    --text-color: #f0f0f0;
    --header-color: #64b5f6;
    --link-color: #90caf9;
    --code-bg: #444;
    --border-color: #555;
}

.help-container {
    padding: 20px;
    background-color: var(--bg-color);
    text-align: left;
    color: var(--text-color);
    font-family: sans-serif;
    line-height: 1.6;
    overflow-y: auto;
    height: calc(100vh - 100px);
}

.toc {
    background: var(--code-bg);
    border: 1px solid var(--border-color);
    border-radius: 6px;
    padding: 1em 1.5em;
    margin-bottom: 2em;
    max-width: 400px;
}

.toc h2 {
    margin-top: 0;
    font-size: 1.2em;
    color: var(--header-color);
    border-bottom: none;
    padding-bottom: 0;
}

.toc ul {
    margin-left: 0;
    padding-left: 1em;
}

.toc li {
    margin-bottom: 0.3em;
}

.toc a {
    color: var(--link-color);
    text-decoration: underline;
    cursor: pointer;
}

h1,
h2,
h3,
h4 {
    color: var(--header-color);
    margin-top: 1em;
    margin-bottom: 0.5em;
    border-bottom: 1px solid var(--border-color);
    padding-bottom: 0.2em;
}

h1 {
    text-align: center;
    font-size: 2em;
    border-bottom: 2px solid var(--header-color);
}

h2 {
    font-size: 1.6em;
}

h3 {
    font-size: 1.3em;
    border-bottom: none;
}

h4 {
    font-size: 1.1em;
    font-style: italic;
    border-bottom: none;
    margin-top: 1em;
}


p {
    margin-bottom: 1em;
}

ul,
ol {
    margin-left: 10px;
    margin-bottom: 1em;
}

li {
    margin-bottom: 0.5em;
}

code {
    background-color: var(--code-bg);
    padding: 0.2em 0.4em;
    border-radius: 3px;
    font-family: monospace;
}

strong {
    font-weight: bold;
}

a {
    color: var(--link-color);
    text-decoration: none;
}

a:hover {
    text-decoration: underline;
}

section {
    margin-bottom: 2em;
}
</style>