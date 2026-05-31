# Ranger Leaderboard Workflow

## Introduction

This workflow helps you to measure and compare per-ranger field activity by combining patrol records and reported events from **EarthRanger** into a single leaderboard.

**What this workflow does:**
- Downloads patrol observations and events from EarthRanger for a chosen time range
- Calculates per-ranger patrol metrics: number of patrols, total distance traveled, and total time on patrol
- Counts the number of events each ranger reported
- Produces a leaderboard table ranking rangers by activity
- Shows headline totals (total patrols, distance, duration, events) across the whole team
- Creates an interactive map of patrol tracks overlaid with reported event locations

**Who should use this:**
- Conservation managers monitoring ranger team activity and field effort
- Protected-area supervisors comparing ranger output across a period
- Anyone needing a quick "who did what" summary of patrol and event reporting activity in EarthRanger

---

## Prerequisites

Before using this workflow, you need:

1. **Ecoscope Desktop** installed on your computer
   - If you haven't installed it yet, please follow the installation instructions for Ecoscope Desktop

2. **EarthRanger Data Source** configured in Ecoscope Desktop
   - You must have already set up a connection to your EarthRanger server
   - Your data source should be configured with proper authentication credentials
   - You'll need to know the name of your configured data source (e.g., `"er_asia"`)

3. **Patrols and events recorded in EarthRanger**
   - At least one **patrol type** must exist in your EarthRanger site (e.g., `"demo_patrol"`)
     - You can find them at `https://<your-site>.pamdas.org/admin/activity/patroltype/`
   - At least one **event type** that rangers report (e.g., `"wildlife_sighting_rep"`)
     - You can find them at `https://<your-site>.pamdas.org/admin/activity/eventtype/`
   - Patrols must be linked to a **patrol subject** (the ranger) and events must include a **reporter** for them to appear on the leaderboard

---

## Installation

1. Select "Workflow Templates" tab
2. Click "+ Add Template"
3. Copy and paste this URL https://github.com/wildlife-dynamics/ranger and wait for the workflow template to be downloaded and initialized
4. The template will now appear in your available template list

---

## Configuration Guide

### Basic Configuration

#### 1. Workflow Details
Add information to help differentiate this workflow run from others.

- **Workflow Name** (required): A short, descriptive name for this run
  - Example: `"Ranger Leaderboard"`
- **Workflow Description** (optional): Additional context about this run
  - Example: `"Per-ranger patrol and event activity."`

#### 2. Data Source
Select the EarthRanger connection to pull patrols and events from.

- **Data Source** (required): The name of one of your configured EarthRanger data sources
  - Example: `"er_asia"`
  - Note: This must match a data source you've already configured in Ecoscope Desktop

#### 3. Time Range
Choose the period of time to analyze. Only patrols and events within this window will be included.

- **Since** (required): The start date and time
  - Example: `2026-05-01T00:00:00`
- **Until** (required): The end date and time
  - Example: `2026-05-06T23:59:59`
- **Timezone** (optional): The timezone used to interpret your dates and to display times in the outputs
  - Example: `UTC (UTC+00:00)` or `Africa/Nairobi (UTC+03:00)`

#### 4. Get Patrol Observations
Choose which patrols to include in the leaderboard.

- **Patrol Types** (required): One or more patrol types to analyze
  - Example: `["demo_patrol"]`
  - Note: The available options are loaded from your selected data source. Leave empty to include all patrol types.

#### 5. Get Events
Choose which events to count toward each ranger's reporting total.

- **Event Types** (required): One or more event types to analyze
  - Example: `["wildlife_sighting_rep"]`
  - Note: The available options are loaded from your selected data source. Leave empty to count all event types.

---

### Advanced Configuration

These optional settings provide additional control over your workflow. They are hidden by default in Ecoscope Desktop and can be revealed by expanding the "Advanced" section of each configuration group.

#### Patrol Filtering

- **Patrol Status**: Restrict the analysis to patrols in specific states
  - Default: `["done"]`
  - Options: `active`, `overdue`, `done`, `cancelled`
  - Note: Leave empty to include patrols of all statuses

- **Patrols Overlap Daterange**: Whether to include patrols that start before or end after your time range
  - Default: `true` (include overlapping patrols)
  - Set to `false` to only count patrols fully contained inside your time range

#### Event Detail

- **Include Details**: Include the full detail payload for each event
  - Default: `false`
- **Include Updates**: Include the edit history of each event
  - Default: `false`
- **Include Related Events**: Include events linked to the returned events
  - Default: `false`
- **Force Point Geometry**: Reduce polygon or multipolygon event geometries to a single point (their centroid) so they show as point markers on the map
  - Default: `true`
  - Set to `false` to preserve the original event shapes

#### Trajectory Segment Filter
Removes outlier patrol track segments so noisy GPS jumps don't inflate the per-ranger distance and duration totals.

- **Minimum Segment Length (Meters)**: Default `0.001`
- **Maximum Segment Length (Meters)**: Default `100000`
- **Minimum Segment Duration (Seconds)**: Default `1`
- **Maximum Segment Duration (Seconds)**: Default `172800`
- **Minimum Segment Speed (Kilometers per Hour)**: Default `0.01`
- **Maximum Segment Speed (Kilometers per Hour)**: Default `500`

Segments outside these bounds are dropped before distance and duration are summed.

#### Filter Event Locations
Limit the events shown on the map to a geographic area or drop events recorded at specific bogus coordinates.

- **Bounding Box**: Latitude/longitude box that events must fall inside
  - Defaults to the whole world: `min_y: -90`, `max_y: 90`, `min_x: -180`, `max_x: 180`
- **Filter Exact Point Coordinates**: A list of `(latitude, longitude)` pairs to exclude
  - Default: `[]` (no coordinates excluded)
  - Example: `[{y: 0.0, x: 0.0}]` will hide any event recorded exactly at the equator/prime meridian intersection (a common GPS error coordinate)

#### Map Base Layers
Customize the basemap tiles shown behind the patrol tracks and event points.

- **Base Layers**: A list of one or more base maps drawn from bottom to top
  - Default: `Terrain` (fully opaque) with `Satellite` (50% opacity) layered on top
  - Options: `Open Street Map`, `Roadmap`, `Satellite`, `Terrain`, `LandDx`, `USGS Hillshade`, or a custom tile URL
  - **Layer Opacity** (per layer): A value between `1` (fully visible) and `0` (hidden)

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

After the workflow completes successfully, you'll see an interactive dashboard with two output groups: headline totals and a per-ranger leaderboard, plus an interactive map.

### Visual Outputs (Dashboard)

The workflow creates an interactive dashboard with the following visualizations:

#### Headline Totals
Four single-value tiles summarizing activity across the whole team for the selected time range:

- **Total Patrols**: Number of unique patrols carried out (whole number)
- **Total Distance (km)**: Combined distance walked or driven across all patrols, in kilometers (1 decimal place)
- **Total Duration (hrs)**: Combined time spent on patrol across all rangers, in hours (1 decimal place)
- **Total Events**: Number of events reported by rangers (whole number)
  - Note: Events without a reporter name are excluded so this total is consistent with the per-ranger leaderboard

#### Ranger Leaderboard
- **Format**: Interactive sortable table
- **Features**:
  - Click any column header to sort by that metric
  - Download the table as a file using the download button
  - One row per ranger
- **Columns**:
  - `Ranger`: The patrol subject (and event reporter) name
  - `Patrol Count`: Number of distinct patrols completed by this ranger
  - `Total Distance (km)`: Total distance covered on patrol, in kilometers
  - `Total Duration (hrs)`: Total time on patrol, in hours
  - `Event Count`: Number of events this ranger reported

Note: Rangers who only patrolled (no events) or only reported events (no patrols) still appear in the leaderboard — the missing metric is shown as `0`.

#### Patrols & Events Map
- **Format**: Interactive map with patrol tracks and event points
- **Features**:
  - Gray polylines show each patrol track segment
  - Colored points show each reported event, with one color per event type (`tab20b` colormap)
  - Hovering over a track shows: `Patrol Subject`, `Start`, `Speed (kph)`
  - Hovering over an event point shows: `Event Serial`, `Event Time`, `Event Type`, `Reported By`
  - Legend (bottom-right) maps colors to event types
  - North arrow in the top-left
  - Pan, zoom, and toggle base layers using the map controls

---

## Common Use Cases & Examples

Here are some typical scenarios and how to configure the workflow for each:

### Example 1: Weekly Ranger Activity Snapshot
**Goal**: Review last week's patrol and wildlife-sighting activity by ranger.

**Configuration**:
- **Time Range**:
  - Since: `2026-05-01T00:00:00`
  - Until: `2026-05-06T23:59:59`
  - Timezone: `UTC (UTC+00:00)`
- **Data Source**: `"er_asia"`
- **Patrol Types**: `["demo_patrol"]`
- **Event Types**: `["wildlife_sighting_rep"]`

**Result**:
- Leaderboard ranking rangers active during 1–6 May 2026
- Headline totals for distance, duration, patrol count, and wildlife sightings
- Map of patrol tracks and reported wildlife sightings

---

### Example 2: Monthly Team Performance Report
**Goal**: Summarize a full month of activity for all patrol and event types.

**Configuration**:
- **Time Range**:
  - Since: `2026-04-01T00:00:00`
  - Until: `2026-04-30T23:59:59`
  - Timezone: `Africa/Nairobi (UTC+03:00)`
- **Data Source**: `"er_asia"`
- **Patrol Types**: `[]` (leave empty to include all patrol types)
- **Event Types**: `[]` (leave empty to include all event types)

**Result**:
- Comprehensive monthly leaderboard covering every patrol and event type
- Total distance and duration for the team across April 2026
- Map shows the full distribution of activity for the month

---

### Example 3: Completed Patrols Only
**Goal**: Focus the leaderboard on patrols that were officially completed (not active, overdue, or cancelled).

**Configuration**:
- **Time Range**:
  - Since: `2026-05-01T00:00:00`
  - Until: `2026-05-06T23:59:59`
  - Timezone: `UTC (UTC+00:00)`
- **Data Source**: `"er_asia"`
- **Patrol Types**: `["demo_patrol"]`
- **Event Types**: `["wildlife_sighting_rep"]`
- **Advanced — Patrol Status**: `["done"]` (this is the default)
- **Advanced — Patrols Overlap Daterange**: `false`

**Result**:
- Leaderboard only counts patrols whose start and end fall fully inside the time range and are marked `done`
- Useful for clean end-of-period reporting

---

### Example 4: Clean Map by Hiding Bogus GPS Coordinates
**Goal**: Build the leaderboard and map, but hide events recorded at the `(0, 0)` "null island" coordinate that some devices report on GPS failure.

**Configuration**:
- **Time Range**:
  - Since: `2026-05-01T00:00:00`
  - Until: `2026-05-06T23:59:59`
  - Timezone: `UTC (UTC+00:00)`
- **Data Source**: `"er_asia"`
- **Patrol Types**: `["demo_patrol"]`
- **Event Types**: `["wildlife_sighting_rep"]`
- **Advanced — Filter Exact Point Coordinates**:
  - `y: 0.0`, `x: 0.0`
  - `y: 1.0`, `x: 1.0`

**Result**:
- Leaderboard counts are unchanged
- Map drops any event point recorded at the listed bogus coordinates

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

#### Leaderboard is empty or shows fewer rangers than expected
**Problem**: The output table has no rows, or some rangers you expected to see are missing.

**Solutions**:
- Widen the **Time Range** — patrols and events outside the window are excluded
- Clear the **Patrol Types** and **Event Types** fields to include every type
- Check that the missing rangers' patrols have a **patrol subject** assigned in EarthRanger — patrols without a subject can't be attributed
- Check that the missing rangers' events have a **reporter** filled in — events with no reporter are dropped from the leaderboard and event totals
- Try setting **Patrol Status** to include `active` and `overdue` if you're looking at an in-progress period

#### Map is blank or missing event points
**Problem**: The map renders but shows no tracks or no event markers.

**Solutions**:
- Check that the selected patrols have GPS observations (some patrol types are not GPS-tracked)
- Confirm events have valid geometry — events with no location can still count on the leaderboard but won't appear on the map
- Widen the **Bounding Box** if you've narrowed it to a small region
- Remove entries from **Filter Exact Point Coordinates** if you've over-filtered

#### Total Distance or Total Duration looks too high
**Problem**: A few patrols are inflating the totals with implausible values (huge teleports, very long segments).

**Solutions**:
- Tighten the **Trajectory Segment Filter** in advanced settings — lower `Maximum Segment Length (Meters)` and `Maximum Segment Speed (Kilometers per Hour)` to drop GPS jumps
- Verify the underlying patrol observations in EarthRanger for outliers
- Consider narrowing **Patrol Types** to exclude vehicle patrols if you only want foot patrols

#### Workflow runs very slowly
**Problem**: The workflow takes a long time to complete.

**Solutions**:
- Narrow your **Time Range** — large date ranges over many patrols and events take longer to fetch
- Limit **Patrol Types** and **Event Types** to only what you need
- Avoid enabling **Include Details**, **Include Updates**, and **Include Related Events** unless required — they significantly increase the amount of data downloaded
- The first run after a cold start may be slower while the system warms up — subsequent runs are faster

#### Authentication errors
**Problem**: The workflow reports an authentication or permission error.

**Solutions**:
- Re-enter your EarthRanger credentials in Ecoscope Desktop
- Confirm your EarthRanger account has permission to view the requested patrol and event types
- Ask your EarthRanger administrator to grant access to the patrol and event types you selected
