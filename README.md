# AIUB Social Welfare Club - SHOMOY

**Student Availability Tracker**


Try it out [here](https://asif-aman-jihad.github.io/volunteer-availability-shomoy/)

## Overview

The app runs entirely in the browser – no server, no backend, no data transmission beyond fetching the public sheet. On load, it fetches the latest data from the published Google Sheet and displays it interactively. Coordinators can then:

- Select a day (Sunday to Thursday)

- Choose one or more hourly time slots

- View instantly which students are free during the selected period

- Click any student for detailed schedule and contact information

- Print or export filtered lists
## Features

- Fetch and parse data from published Google Sheets (CSV format)
- Filter by day, multiple time slots, and search query
- Visual availability indicators (free all day, partially free, busy all day)
- Detailed student modal with full weekly schedule grid
- Local data caching across sessions
- Refresh or clear data via settings
- Fully responsive (desktop and mobile)
- Minimal dependencies (SheetJS and PapaParse from CDN)

## How It Works

1. Club members submit their class schedules (busy times) via a Google Form.
2. Responses are collected in a Google Sheet.
3. The sheet is published to the web as CSV (File > Share > Publish to web > Choose sheet > CSV).
4. The app fetches the CSV data automatically on load.
5. It converts class time ranges into busy hourly slots, skipping irrelevant columns like Timestamp and screenshot uploads.
6. Filtering shows only students who are free during all selected slots on the chosen day.

## Required Google Sheet Format

The Google Sheet must have columns in this exact order (from Google Forms):

1. Timestamp (ignored)
2. Your Name
3. Student ID
4. Phone Number
5. Department
6. Sunday
8. Monday
9. Tuesday
10. Wednesday
11. Thursday

Each day column should contain class times (when the student is busy), for example:

`10:10 AM – 12:10 PM, 2:20 PM – 4:20 PM`

or

`No classes`

or left empty (treated as fully free).

Supported time formats:
- `8:00 AM - 10:00 AM`
- `12:10 PM – 2:10 PM`
- Multiple ranges separated by commas
- Dashes: `-` or `–` (en-dash)

The publish URL is hardcoded in the app's script. To change it, update the `fetch` URL in `fetchDataFromSheets()`:

```javascript
fetch('https://docs.google.com/spreadsheets/d/e/example/pub?output=csv')
```

## Current Time Slots

The app uses fixed hourly slots starting at:

- 8:00 AM
- 9:00 AM
- 10:00 AM
- 11:00 AM
- 12:00 PM
- 1:00 PM
- 2:00 PM
- 3:00 PM
- 4:00 PM

A slot is marked busy if any class overlaps with it.

## Customizing Time Slots

To change or add time slots, edit the following constants near the top of the `<script>` section in `index.html`:

```javascript
const TIME_SLOTS = ['8:00', '9:00', '10:00', '11:00', '12:00', '1:00', '2:00', '3:00', '4:00'];
const TIME_LABELS = ['8:00 AM', '9:00 AM', '10:00 AM', '11:00 AM', '12:00 PM', '1:00 PM', '2:00 PM', '3:00 PM', '4:00 PM'];
```

- `TIME_SLOTS`: Internal keys used in schedule objects (keep as 24-hour strings without AM/PM)
- `TIME_LABELS`: Display text shown in buttons and modal

Example: To add a 5:00 PM slot:

```javascript
const TIME_SLOTS = ['8:00', '9:00', '10:00', '11:00', '12:00', '1:00', '2:00', '3:00', '4:00', '5:00'];
const TIME_LABELS = ['8:00 AM', '9:00 AM', '10:00 AM', '11:00 AM', '12:00 PM', '1:00 PM', '2:00 PM', '3:00 PM', '4:00 PM', '5:00 PM'];
```

You must also update the overlap detection logic in `markBusySlots()`:

```javascript
const slotMapping = [
    { key: '8:00', start: 8, end: 9 },
    { key: '9:00', start: 9, end: 10 },
    // ... existing entries
    { key: '4:00', start: 16, end: 17 },
    { key: '5:00', start: 17, end: 18 }  // new entry
];
```

For finer granularity (e.g., 30-minute slots), add more entries accordingly.

## Adding or Changing Days

Current days are defined by:

```javascript
const DAYS = ['sunday', 'monday', 'tuesday', 'wednesday', 'thursday'];
```

To add Friday:

1. Update `DAYS` array:

```javascript
const DAYS = ['sunday', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday'];
```

2. Add a corresponding button in the HTML day selector (inside `.day-selector`):

```html
<button class="day-button" data-day="friday">Friday</button>
```

3. Ensure your Google Sheet has a "Friday" column (after Thursday).
4. Update the parsing loop in `fetchDataFromSheets()` to include the new column index:

```javascript
schedule: {
    sunday: parseDaySchedule(row[5] || ''),
    monday: parseDaySchedule(row[7] || ''),
    // ... existing
    thursday: parseDaySchedule(row[10] || ''),
    friday: parseDaySchedule(row[11] || '')  // new; adjust index as needed
}
```

5. In the student modal, add a new day section:

```html
<div class="day-schedule" id="fridaySchedule">
    <h4>Friday</h4>
    <div class="schedule-slots" id="fridaySlots"></div>
</div>
```

And update the `openStudentModal()` loop to include `'friday'`.

## Usage Instructions

1. Open the live demo or your local copy of `index.html`
2. The app automatically fetches and loads the latest data from the published Google Sheet
3. Select a day
4. (Optional) Click time slot buttons to require availability during those hours
5. (Optional) Use search bar to filter by name/ID/department
6. Click any student card for full details
7. Use the settings gear (top right) to refresh (re-fetch) or clear data

## Data Privacy

- Data is fetched from a public published Google Sheet
- All processing happens in your browser
- Fetched data is cached in `localStorage` on your device
- No additional data is uploaded or shared
- Clear data via settings when using shared computers
- Note: The sheet must be publicly published for the app to access it

## Limitations

- Relies on the Google Sheet being publicly published and the URL remaining valid
- Only supports days defined in `DAYS` array (currently Sunday–Thursday)
- Fixed time slots (customizable with code changes)
- No automatic merging of duplicate student entries
- No export functionality
- Potential delays if the sheet is large or network is slow

## Deployment

- Host on GitHub Pages (as currently done)
- Or simply open `index.html` locally
- No build step required

## License

MIT License – free to use, modify, and distribute.

Developed by [Asif Aman Jihad](https://www.linkedin.com/in/asif-aman-jihad/) for AIUB Social Welfare Club SHOMOY
