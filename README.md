# VolunteerHub – Smart Volunteer Management System

A full-stack web application designed to help NGOs manage volunteers, events, enrolments, attendance, tasks, reminders, feedback, reports, and certificates from one platform.

## Key Features

- Secure administrator and volunteer authentication
- Volunteer registration and profile management
- Event creation and enrolment
- Attendance tracking
- Task assignment and progress management
- Reminders and notifications
- Volunteer feedback collection
- CSV report generation
- Printable participation certificates
- Persistent SQLite database

## Technology Stack

- **Frontend:** HTML5, CSS3 and JavaScript
- **Backend:** Node.js HTTP server
- **Database:** SQLite using `node:sqlite`
- **Authentication:** Cookie-based sessions and scrypt password hashing
- **APIs:** JSON-based REST-style endpoints

## Project Structure

```text
volunteerhub-fullstack/
├── public/
│   ├── index.html
│   ├── style.css
│   └── app.js
├── server.js
├── package.json
├── README.md
├── START_HERE.md
└── VIVA_AND_CODE_GUIDE.md
