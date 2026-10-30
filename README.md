# Daily Tracker

Version 2.2.0 of the daily log and weekly review app, with automatic workbook history, offline local copies, and cross-device syncing through Netlify Blobs. It is designed to sit alongside the separate exercise planner.

## What it includes

- Daily food total with a 1,700-calorie guide and 11,900-calorie weekly target.
- All-day Move Active Calories, workout-only Active Calories, and steps.
- Multiple workout sessions per day, with their Active Calories summed.
- Weekly review totals without repeating the daily table; missing values stay blank.
- Monday Withings weigh-ins: weight, fat mass %, lean mass %, BMI, and muscle mass %.
- A four-week weight trend and a reminder to review your records before choosing whether to adjust targets.
- Measurement tab cards for Weight, Fat Mass %, Lean Mass %, BMI, and Muscle Mass %, sourced directly from the Weight tab, plus monthly cards for neck, shoulders, chest, band (chest), bicep, waist, hips, thigh, and calf.
- Add any other monthly measurement name and unit, with month-to-month trend lines.
- The Daily Log keeps Move, Exercise, and Steps entry controls; weekly totals stay in Weekly Review.
- Weekly targets that carry forward until changed, with optional one-week and one-day overrides.
- One logging point per day, with no points removed for missed days.
- JSON backup and restore.
- Spreadsheet history appears automatically on first open.
- Cross-device sync through a Netlify Function and Netlify Blobs, with conditional writes and conflict merging.
- Installable PWA shell and device push reminder functions.

## Workbook history import

The workbook history is included in the app and appears automatically. It brings in 178 dates with at least one daily value and 43 weigh-ins, from January 5 through October 26, 2026. The original import file remains outside `public/`; a generated browser seed provides the history for local file mode and initializes the shared Netlify store. Missing cells remain blank. Historical exercise calorie totals are kept as dated daily totals because the workbook did not contain individual workout sessions. The spreadsheet did not contain monthly circumference measurements.

Existing app data takes priority if a date already has an entry. Imported records do not add logging points.

## Target schedule

The workbook targets remain attached to their historical weeks. The revised exercise and steps targets start Monday, November 2, 2026:

- Food: 11,900 calories/week (1,700/day guide)
- Move: 5,250 Active Calories/week
- Exercise: 2,000 workout Active Calories/week
- Steps: 5,600/day average

A target change made in the app starts the following Monday. One-week changes revert to the prior scheduled targets; one-day overrides change only that date's daily guide.

## Shared data and backups

Daily logs, weight readings, target history, monthly measurements, and logging points are saved locally first, then synced to the Netlify Function at `/api/data`, which stores one shared tracker record in a site-wide Netlify Blobs store. Other devices load that shared record on startup, when returning to the app, and periodically while it is open. A local copy remains available offline and syncs when the connection returns. Reminder credentials and per-device reminder settings are not synced.

On first use, the shared store is initialized with the included workbook history. Existing Version 1 browser data is merged into the shared store when that browser first connects. Each device should open the same deployed Netlify address. Use **More → Download backup** for a separate backup.

The data endpoint has no sign-in, as requested. Anyone who can access the deployed app can read or change the shared tracker data. The function uses ETags to reject stale writes and the app merges changes by record before retrying; if two devices edit the same record at nearly the same time, the most recent retry takes precedence. Netlify Blobs conditional write behavior is documented by Netlify.

## Run locally

1. Install Node.js 22.13.0 or later.
2. From this folder, run npm install.
3. Run npm run dev.
4. Open the local address shown by Netlify CLI.

Local file mode shows the bundled history and keeps data only in that browser. The bundled history seed is included in the public app files. Cross-device sync and device reminders require the HTTPS Netlify deployment; reminders also require their server configuration.

## Configure device reminders

The reminders are scheduled server-side so they can arrive when the app is closed. They send once after three full unlogged days and repeat once the next day. Saving any daily entry resets the count. The default time is 9:00 AM local time and can be changed in the app.

1. Install the app dependencies with npm install.
2. Generate a VAPID key pair using npx web-push generate-vapid-keys.
3. In the Netlify site environment, set VAPID_PUBLIC_KEY, VAPID_PRIVATE_KEY, and VAPID_SUBJECT. Use the site URL as the VAPID subject.
4. Set REMINDER_API_KEY to a long random secret. For example, generate one locally with Node: node -e "console.log(require('node:crypto').randomBytes(32).toString('hex'))".
5. Deploy the site, install it on the device, then open **More → Device reminders** and enter the same REMINDER_API_KEY to enable push notifications.

The reminder scheduler checks hourly in UTC and sends at the selected local hour. Netlify scheduled functions are used for that background check. Keep the project publish directory set to `public` and functions directory set to `netlify/functions`. The included `netlify.toml` already sets both.

