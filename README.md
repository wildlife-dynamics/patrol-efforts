# Patrol Efforts Workflow

## Introduction

This workflow helps you to measure and compare **patrol effort** across your rangers, patrol types, or time periods by combining patrol tracks and reported events from **EarthRanger** into a summary table and a set of interactive maps.

**What this workflow does:**
- Downloads patrol observations and their linked events from EarthRanger for a chosen time range
- Builds a configurable **summary table** of patrol effort per category (per ranger, per patrol type, or per patrol status) with metrics such as patrol count, patrol days, distance, duration, and area covered
- Creates an interactive **Patrols & Events Map** of patrol tracks overlaid with reported event locations, colour-coded by category
- Creates a **Patrol Coverage Map** showing how much patrol effort (time or distance) fell on each area of the landscape
- Creates an **Event Density Map** showing where reported events concentrate
- Optionally splits every output into per-group dashboard views (by ranger, patrol type, patrol serial number, or a time period such as month)

**Who should use this:**
- Conservation managers monitoring patrol coverage and field effort
- Protected-area supervisors comparing effort across rangers, patrol types, or periods
- Researchers analyzing where and how much patrolling happened relative to reported events

---

## Prerequisites

Before using this workflow, you need:

1. **Ecoscope Desktop** installed on your computer
   - If you haven't installed it yet, please follow the installation instructions for Ecoscope Desktop

2. **EarthRanger Data Source** configured in Ecoscope Desktop
   - You must have already set up a connection to your EarthRanger server
   - Your data source should be configured with proper authentication credentials
   - You'll need to know the name of your configured data source (e.g., `"mep_dev"`)

3. **Patrols and events recorded in EarthRanger**
   - At least one **patrol type** must exist in your EarthRanger site (e.g., `"ecoscope_patrol"`)
     - You can find them at `https://<your-site>.pamdas.org/admin/activity/patroltype/`
   - Optionally, **event types** that rangers report (e.g., `"wildlife_sighting_rep"`)
     - You can find them at `https://<your-site>.pamdas.org/admin/activity/eventtype/`
   - Patrols must be linked to a **patrol subject** (the ranger) for the per-ranger summary and colour-by options to work

---

## Installation

1. Select "Workflow Templates" tab
2. Click "+ Add Template"
3. Copy and paste this URL https://github.com/wildlife-dynamics/patrol-efforts and wait for the workflow template to be downloaded and initialized
4. The template will now appear in your available template list

---

## Configuration Guide

### Basic Configuration

#### 1. Workflow Details
Add information to help differentiate this workflow run from others.

- **Workflow Name** (required): A short, descriptive name for this run
  - Example: `"Patrol Efforts"`
- **Workflow Description** (optional): Additional context about this run
  - Example: `"Per-category patrol effort summary, coverage and event density."`

#### 2. Data Source
Select the EarthRanger connection to pull patrols and events from.

- **Data Source** (required): The name of one of your configured EarthRanger data sources
  - Example: `"mep_dev"`
  - Note: This must match a data source you've already configured in Ecoscope Desktop

#### 3. Time Range
Choose the period of time to analyze. Only patrols and events within this window will be included.

- **Since** (required): The start date and time
  - Example: `2015-01-10T00:00:00`
- **Until** (required): The end date and time
  - Example: `2015-02-28T23:59:59`
- **Timezone** (required): The timezone used to interpret your dates and to display times in the outputs
  - Example: `Africa/Nairobi (UTC+03:00)` or `UTC (UTC+00:00)`

#### 4. Patrol and Event Types
Choose which patrols and events to include.

- **Patrol Types** (optional): One or more patrol types to analyze
  - Example: `["ecoscope_patrol"]`
  - Note: The available options are loaded from your selected data source. Leave empty to include all patrol types.
- **Event Types** (optional): One or more event types to analyze
  - Example: `["wildlife_sighting_rep"]`
  - Note: Leave empty to include all event types.

#### 5. Group Data (optional)
Split the summary table and every map into separate per-group dashboard views. Leave empty for a single combined view.

- **Category** grouper: Group by a data column
  - Options: `Patrol Serial Number`, `Patrol Type`, `Patrol Subject`
- **Time** grouper: Group by a time period
  - Options: `Year`, `Month`, `Year and Month`, `Day of the year`, `Day of the month`, `Day of the week`, `Hour`, `Date`
- Note: You can add more than one grouper (e.g., Patrol Subject **and** Month) to split by every combination.

#### 6. Patrol Effort Summary
Configure the summary table.

- **Aggregator** (required): The category that becomes the table's rows
  - Options: `Patrol Subject` (default), `Patrol Type`, `Patrol Status`
  - You may select more than one to group by a combination.
- **Summary Metrics** (required): The columns shown for each category. Add one or more of:
  - **Patrol Count**: Number of distinct patrols
  - **Patrol Days**: Number of distinct days on which patrolling occurred
  - **Total Distance**: Distance travelled, in `km` or `m`
  - **Total Duration**: Time on patrol, in `h` (hours) or other units
  - **Area Covered (Merged)**: Ground area covered, with overlapping patrol swaths merged (set **Swath Width (m)**)
  - **Area Covered (Unmerged)**: Ground area covered, summed per patrol without merging overlaps
  - **Custom**: An escape hatch — pick any **Statistic** (count, sum, mean, median, min, max, nunique) over any **Column**, set **Decimal Places**, and optionally **Convert Units**

#### 7. Patrols & Events Map
Control how the tracks and event points are coloured.

- **Colour Tracks By**: The category used to colour patrol tracks
  - Options: `Patrol Subject` (default), `Patrol Type`, `Patrol Status`
- **Colour Events By**: The category used to colour event points
  - Options: `Event Type` (default), `Event Category`

#### 8. Patrol & Event Density Map
Control how patrol effort is weighted on the coverage map.

- **Calculate Patrol Effort per Cell** (required): Weight each grid cell by
  - `Time` — total patrol time in the cell (shown as **hours**), the default
  - `Distance` — total distance travelled in the cell (shown as **km**)

#### 9. Map Base Layers
Customize the basemap tiles drawn behind the maps.

- **Base Layers**: One or more base maps drawn from bottom to top
  - Default: a topographic layer (fully opaque)
  - **Layer Opacity** (per layer): A value between `1` (fully visible) and `0` (hidden)

---

### Advanced Configuration

These optional settings are hidden by default in Ecoscope Desktop and can be revealed by expanding the "Advanced Configurations" section of the relevant card.

#### Patrol Filtering (Patrol and Event Types card)

- **Patrol Status**: Restrict the analysis to patrols in specific states
  - Default: `["done"]`
  - Options: `active`, `overdue`, `done`, `cancelled`
  - Note: Leave empty to include patrols of all statuses
- **Patrols Overlap Daterange**: Whether to include patrols that start before or end after your time range
  - Default: `true` (include overlapping patrols)
  - Set to `false` to only count patrols fully contained inside your time range

#### Filter Data

- **Bounding Box**: Latitude/longitude box that patrols and events must fall inside
  - Defaults to the whole world: `min_y: -90`, `max_y: 90`, `min_x: -180`, `max_x: 180`
- **Filter Exact Point Coordinates**: A list of `(latitude, longitude)` pairs to exclude
  - Default: excludes common GPS-error coordinates such as `(0, 0)` "null island"
  - Example: adding `{y: 0.0, x: 0.0}` hides any observation or event recorded exactly at the equator/prime meridian intersection
- **Trajectory Filter**: Removes outlier patrol track segments so noisy GPS jumps don't inflate distance, duration, and coverage
  - **Minimum / Maximum Segment Length (Meters)**: Defaults `0.001` / `100000`
  - **Minimum / Maximum Segment Duration (Seconds)**: Defaults `1` / `172800`
  - **Minimum / Maximum Segment Speed (Kilometers per Hour)**: Defaults `0.01` / `500`

#### Patrol & Event Density Map — Advanced Configurations
One set of grid options applies to **both** the Patrol Coverage Map and the Event Density Map.

- **Heatmap Layer Opacity**: Transparency of the density layers, from `1` (fully visible) to `0` (hidden)
  - Default: `0.7`
- **Grid Cell Size**: `Auto-scale` (default) or `Customize` with a **Custom Grid Cell Size** (in the units of the chosen CRS; must be greater than `0` and less than `10000`)
- **Coordinate Reference System**: The CRS used for the density calculation
  - Default: `EPSG:3857`
  - Must be a valid CRS authority code (e.g., `ESRI:53042`)

---

## Running the Workflow

Once you've configured all the settings:

1. **Review your configuration**
   - Double-check your time range, data source, and the patrol and event types you selected

2. **Save and run**
   - Click "Submit" and the workflow will show up in the "My Workflows" table in Ecoscope Desktop
   - Click "Run" and the workflow will begin processing

3. **Monitor progress and wait for completion**
   - You'll see status updates as the workflow runs
   - Processing time depends on:
     - The size of your date range
     - The number of patrols and events in the system
     - The number of rangers active in the period
   - The workflow completes with status "Success" or "Failed"

---

## Understanding Your Results

After the workflow completes successfully, you'll see an interactive dashboard with four visualizations. If you set one or more groupers in **Group Data**, a view selector appears at the top so you can switch between per-group dashboards (e.g., one per ranger, or one per month).

### Visual Outputs (Dashboard)

#### Patrol Effort Summary
- **Format**: Interactive sortable table
- **Features**:
  - Click any column header to sort by that metric
  - Download the table using the download button
  - One row per category value (per ranger, patrol type, or patrol status, depending on your **Aggregator**)
- **Columns**: The category column (e.g., `Patrol Subject`, `Patrol Type`, `Patrol Status`) followed by one column per metric you selected (Patrol Count, Patrol Days, Total Distance, Total Duration, Area Covered, and any Custom metrics)

#### Patrols & Events Map
- **Format**: Interactive map with patrol tracks and event points
- **Features**:
  - Coloured polylines show each patrol track, coloured by your **Colour Tracks By** choice
  - Coloured points show each reported event, coloured by your **Colour Events By** choice
  - Two titled legend boxes — **Patrols** (track categories) and **Events** (event categories) — map colours to categories; north arrow in the corner
  - Pan, zoom, and toggle base layers using the map controls

#### Patrol Coverage Map
- **Format**: Interactive gridded heatmap
- **Features**:
  - Each grid cell is shaded by how much patrol effort fell inside it
  - Weighted by **time** (legend: `Patrol Effort (hours)`) or **distance** (legend: `Patrol Effort (km)`) per your **Calculate Patrol Effort per Cell** choice
  - Darker/warmer cells indicate more patrol effort — use it to spot well-covered areas and gaps

#### Event Density Map
- **Format**: Interactive gridded heatmap
- **Features**:
  - Each grid cell is shaded by the **number of events** recorded inside it
  - Use it to see where reported events concentrate relative to patrol coverage

### Grouped Outputs

If you configured groupers under **Group Data**, all four visualizations are produced once per group and a view selector lets you switch between them — for example a separate summary table and set of maps for each ranger, or for each month in your time range.

---

## Common Use Cases & Examples

Here are some typical scenarios and how to configure the workflow for each:

### Example 1: Per-Ranger Effort Summary
**Goal**: Rank rangers by patrol effort with full metrics and see coverage across the landscape.

**Configuration**:
- **Time Range**:
  - Since: `2015-01-10T00:00:00`
  - Until: `2015-02-28T23:59:59`
  - Timezone: `Africa/Nairobi (UTC+03:00)`
- **Data Source**: `"mep_dev"`
- **Patrol Types**: `["ecoscope_patrol"]`
- **Aggregator**: `Patrol Subject`
- **Summary Metrics**: Patrol Count, Total Distance (km), Total Duration (h), Patrol Days, Area Covered (Merged & Unmerged)
- **Calculate Patrol Effort per Cell**: `Time`

**Result**:
- A per-ranger summary table with all selected metrics
- Patrols & Events map coloured by ranger, plus time-weighted coverage and event-density maps

---

### Example 2: Coverage Weighted by Distance
**Goal**: See patrol coverage weighted by distance travelled rather than time spent.

**Configuration**:
- **Time Range**: `2015-01-10T00:00:00` to `2015-02-28T23:59:59`, Timezone `UTC (UTC+00:00)`
- **Data Source**: `"mep_dev"`
- **Patrol Types**: `["ecoscope_patrol"]`
- **Aggregator**: `Patrol Subject`
- **Summary Metrics**: Patrol Count, Total Distance (km), Total Duration (h)
- **Calculate Patrol Effort per Cell**: `Distance`

**Result**:
- The Patrol Coverage Map legend reads `Patrol Effort (km)` and shades cells by distance travelled inside them

---

### Example 3: Split by Ranger and Month
**Goal**: Produce a separate dashboard per ranger for each month of the period.

**Configuration**:
- **Time Range**: `2015-01-10T00:00:00` to `2015-02-28T23:59:59`, Timezone `UTC (UTC+00:00)`
- **Data Source**: `"mep_dev"`
- **Patrol Types**: `["ecoscope_patrol"]`
- **Group Data**:
  - Category grouper: `Patrol Subject`
  - Time grouper: `Month`
- **Aggregator**: `Patrol Subject`

**Result**:
- A view selector at the top of the dashboard lets you pick each ranger × month combination, each with its own summary table and maps

---

### Example 4: Custom Density Grid and Metrics
**Goal**: Use a finer coverage grid and add a custom "distinct patrols" metric to the table.

**Configuration**:
- **Time Range**: `2015-01-10T00:00:00` to `2015-02-28T23:59:59`, Timezone `UTC (UTC+00:00)`
- **Data Source**: `"mep_dev"`
- **Aggregator**: `Patrol Type`
- **Summary Metrics**:
  - Patrol Count
  - Custom → Statistic `nunique`, Column `patrol_serial_number`, Decimal Places `0` (labelled "Distinct Serials")
- **Patrol & Event Density Map — Advanced**:
  - Grid Cell Size: `Customize`, Custom Grid Cell Size `2500`
  - Heatmap Layer Opacity: `0.5`

**Result**:
- A summary table grouped by patrol type with a custom distinct-serials count
- Coverage and event-density maps drawn on a finer, more detailed grid

---

## Troubleshooting

### Common Issues and Solutions

#### Workflow fails to start
**Problem**: The workflow exits immediately or reports a connection error.

**Solutions**:
- Confirm your **Data Source** name matches a connection you've configured in Ecoscope Desktop
- Re-enter your EarthRanger password if your session has expired
- Verify the EarthRanger server is reachable from your computer (open it in a browser)
- If you're on a VPN, try a different VPN server — some endpoints return 402/502 errors when the VPN routes incorrectly

#### Summary table is empty or shows fewer rows than expected
**Problem**: The table has no rows, or some categories you expected are missing.

**Solutions**:
- Widen the **Time Range** — patrols and events outside the window are excluded
- Clear the **Patrol Types** field to include every type
- Set **Patrol Status** to include `active` and `overdue` if you're looking at an in-progress period
- Check that the missing patrols have a **patrol subject** assigned in EarthRanger — patrols without one can't be attributed to a ranger

#### Patrols & Events Map is blank or missing points
**Problem**: The map renders but shows no tracks or no event markers.

**Solutions**:
- Check that the selected patrols have GPS observations (some patrol types are not GPS-tracked)
- Confirm events have valid geometry — events with no location still count in the summary but won't appear on the map
- Widen the **Bounding Box** if you've narrowed it to a small region
- Remove entries from **Filter Exact Point Coordinates** if you've over-filtered
- If you set **Colour Events By** to `Event Category`, make sure your events actually carry an event category — otherwise colour events by `Event Type`

#### Coverage or Event Density Map is empty
**Problem**: A density map shows no shaded cells.

**Solutions**:
- The Event Density Map only appears when events were returned — widen the time range or clear the **Event Types** filter
- Confirm the selected patrols produced trajectory segments (a coverage map needs GPS tracks)
- If you set a **Custom Grid Cell Size**, make sure it is appropriate for your **Coordinate Reference System** — an overly large cell can collapse the grid to a single cell

#### Total Distance or Total Duration looks too high
**Problem**: A few patrols are inflating the totals with implausible values (huge teleports, very long segments).

**Solutions**:
- Tighten the **Trajectory Filter** in advanced settings — lower `Maximum Segment Length (Meters)` and `Maximum Segment Speed (Kilometers per Hour)` to drop GPS jumps
- Verify the underlying patrol observations in EarthRanger for outliers

#### Workflow runs very slowly
**Problem**: The workflow takes a long time to complete.

**Solutions**:
- Narrow your **Time Range** — large date ranges over many patrols and events take longer to fetch
- Limit **Patrol Types** and **Event Types** to only what you need
- The first run after a cold start may be slower while the system warms up — subsequent runs are faster

#### Authentication errors
**Problem**: The workflow reports an authentication or permission error.

**Solutions**:
- Re-enter your EarthRanger credentials in Ecoscope Desktop
- Confirm your EarthRanger account has permission to view the requested patrol and event types
- Ask your EarthRanger administrator to grant access to the patrol and event types you selected
