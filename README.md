# Daily Tracker

A private, local-first daily log and weekly review app. It is designed to sit alongside the separate exercise planner.

## What it includes

- Daily food total with a 1,700-calorie guide and 11,900-calorie weekly target.
- All-day Move Active Calories, workout-only Active Calories, and steps.
- Multiple workout sessions per day, with their Active Calories summed.
- Weekly review totals without repeating the daily table; missing values stay blank.
- Monday Withings weigh-ins: weight, fat mass %, lean mass %, BMI, and muscle mass %.
- A four-week weight trend and a reminder to review your records before choosing whether to adjust targets.
- Monthly body measurements with user-defined names and units.
- Weekly targets that carry forward until changed, with optional one-week and one-day overrides.
- One logging point per day, with no points removed for missed days.
- JSON backup and restore.
- Installable PWA shell and device push reminder functions.

## Workbook history import

The history-import.json file is intentionally outside public/. It contains personal health records and is not included in the files Netlify publishes. After opening the app, go to **More → Import workbook history** and select this file from your computer.

It brings in 178 dates with at least one daily value and 43 Monday weigh-ins, from January 5 through October 26, 2026. Missing cells remain blank. Historical exercise calorie totals are kept as dated daily totals because the workbook did not contain individual workout sessions. The spreadsheet did not contain monthly circumference measurements.

Existing app data takes priority if a date already has an entry. Imported records do not add logging points.

## Target schedule

The workbook targets remain attached to their historical weeks. The revised exercise and steps targets start Monday, November 2, 2026:

- Food: 11,900 calories/week (1,700/day guide)
- Move: 5,250 Active Calories/week
- Exercise: 2,000 workout Active Calories/week
- Steps: 5,600/day average

A target change made in the app starts the following Monday. One-week changes revert to the prior scheduled targets; one-day overrides change only that date's daily guide.

## Local data and backups

Daily logs, weight readings, target history, and monthly measurements stay in the current browser's local storage. They are not uploaded or synced to another device. Use **More → Download backup** before changing browsers or devices. The optional reminder service stores only the push subscription, local reminder time zone/time, last-log date, and reminder state; it does not store food, activity, steps, weight, or measurements.

## Run locally

1. Install Node.js 22.13.0 or later.
2. From this folder, run npm install.
3. Run npm run dev.
4. Open the local address shown by Netlify CLI.

The ordinary log, review, backup, and import screens work locally. Device reminders require a deployed HTTPS site and server configuration.

## Configure device reminders

The reminders are scheduled server-side so they can arrive when the app is closed. They send once after three full unlogged days and repeat once the next day. Saving any daily entry resets the count. The default time is 9:00 AM local time and can be changed in the app.

1. Install the app dependencies with npm install.
2. Generate a VAPID key pair using npx web-push generate-vapid-keys.
3. In the Netlify site environment, set VAPID_PUBLIC_KEY, VAPID_PRIVATE_KEY, and VAPID_SUBJECT. Use the site URL as the VAPID subject.
4. Set REMINDER_API_KEY to a long random secret. For example, generate one locally with Node: node -e "console.log(require('node:crypto').randomBytes(32).toString('hex'))".
5. Deploy the site, install it on the device, then open **More → Device reminders** and enter the same REMINDER_API_KEY to enable push notifications.

The reminder scheduler checks hourly in UTC and sends at the selected local hour. Netlify scheduled functions are used for that background check. Do not place history-import.json inside public/ or configure Netlify to publish the project root.

