# SMART VOLUNTEER MANAGEMENT SYSTEM — START HERE

**Project:** CEP, Group 5, Batch C3 | **Team:** Kanishka Veer (367), Aishwarya Shinde (368)

## What this version is
A functional **local full-stack demonstration** with HTML/CSS/JavaScript frontend, a Node.js HTTP backend, and a persistent SQLite relational database. It is an independent upgrade based on the green-and-white layout and modules of the existing GitHub Pages prototype. This build deliberately uses **Node's built-in HTTP server instead of Express** and **SQLite instead of MySQL** so it runs offline with no package installation. Do **not** tell your mentor it uses Express or MySQL. If the approved syllabus/synopsis requires Express + MySQL specifically, this is a demonstration-ready alternative, not a substitute for that requirement.

## Install and run on Windows laptop
1. Install **Node.js 22.13 or later** from the official Node.js website (LTS 22 recommended for this build). During installation, accept defaults. Reopen Command Prompt afterward.
2. Extract this ZIP into a folder such as `Documents\smart-volunteer-fullstack`.
3. Open the extracted folder. Click the address bar, type `cmd`, and press Enter.
4. Type `node --version` (must show v22.13 or newer). Then type `node server.js`.
5. Open your browser and visit `http://localhost:3000`.
6. Admin demo login: **admin@volunteerhub.local** / **ChangeMe123!**. When an admin registers a volunteer, that person can choose **Volunteer** on the login screen and use the registered email plus the temporary password set in the form.
7. Keep Command Prompt open while demonstrating. To stop, press `Ctrl+C`.

**Important:** Do not double-click `index.html` to run this version; it needs the Node backend. Do not open the old GitHub Pages URL to demonstrate the database-backed version. The old link remains a static prototype.

## Demo script (8 minutes)
1. Sign in. Explain that the dashboard numbers come from SQL queries, not hardcoded sample figures.
2. Register a volunteer, e.g. `Demo Student` / `demo.student@example.org`.
3. Create an event, e.g. `Community Reading Drive` / a future date / `Pune`.
4. Enrol the volunteer in that event.
5. Assign a task, e.g. `Help with participant registration`.
6. Open Attendance and mark `Present`, hours `2` and save.
7. Open Reports, show the new row, download the CSV, and open the automatically generated certificate.
8. Refresh the browser: the data remains. Stop and restart Node; the records remain because they are saved in `volunteer.db`.
9. Open Reminders to show upcoming enrolled events. Sign out, select Volunteer, and demonstrate that the volunteer sees only their own events, tasks, hours, reminders and certificates.
10. Show Feedback and explain that only genuine survey responses should be entered; do not fabricate 20 responses.

## Troubleshooting
- `node is not recognized`: install Node.js and reopen Command Prompt.
- `Cannot find module node:sqlite`: Node version too old; upgrade to 22.13+.
- `EADDRINUSE`: another server is using port 3000. Close it or run `set PORT=3001` then `node server.js`.
- Browser shows blank/connection refused: start the server and keep the terminal open.
- Forgot demo password: for an empty practice database, stop the server, delete `volunteer.db` and restart. **This erases all stored records.**
- To copy a finished demo to another laptop, stop the server and copy the project folder including `volunteer.db`. Do not commit this file to GitHub; it may contain volunteer personal information.

## Limitations and integrity
- The administrator account is created automatically on first launch. Passwords are hashed with `scrypt`; sessions are stored in server memory and are lost after a restart.
- This version implements separate administrator and volunteer access, in-app seven-day reminders, printable certificates (the browser can save them as PDF), CSV contribution reports, and role-specific dashboards. It deliberately does not send real email/SMS because that requires external SMTP/SMS credentials and internet access.
- It is designed for **local demonstration**, not public production hosting. Before deploying online, add HTTPS, persistent session storage, CSRF protection, rate limiting, stricter input validation, proper password change flow, and privacy controls.
- Feedback entered here is not a replacement for the 20 volunteer Google Form responses and one NGO survey requested by your mentor.
- Do not submit invented survey results or claim the NGO has adopted the software.

## Files
- `server.js`: Node HTTP server, API routes, SQLite tables and queries, login.
- `public/index.html`: HTML layout and forms.
- `public/style.css`: colours, responsive layout and presentation.
- `public/app.js`: button events, form handling, API calls and table updates.
- `volunteer.db`: generated automatically on first run (not included in ZIP).
- `.gitignore`: prevents database and private environment files from being committed.
- `VIVA_AND_CODE_GUIDE.md`: explanations and sample viva answers.
