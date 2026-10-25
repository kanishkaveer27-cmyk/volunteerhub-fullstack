# CEP Viva and Source Code Guide

## 1. The project in 30 seconds
“Our Smart Volunteer Management System is a web application designed to help NGOs organise volunteer registrations, events, enrolments, tasks, attendance and contribution hours. We visited Seva Sahayog Foundation to understand practical coordination problems. Our application has a frontend made using HTML, CSS and JavaScript; a backend built with Node.js; and a SQLite relational database. We tested the main workflow by saving records, viewing reports, refreshing the website and confirming that data remains stored.”

## 2. First learn these 10 words
- **Website:** Pages displayed by a web browser.
- **Frontend:** Everything the user sees and clicks.
- **HTML:** Defines elements such as headings, forms, buttons and tables.
- **CSS:** Controls appearance, spacing, colours and mobile layout.
- **JavaScript:** Runs interactions and sends requests to the backend.
- **Backend:** Server program that processes requests and manages database access.
- **API:** Agreed route such as `/api/volunteers` used to exchange data between frontend and backend.
- **HTTP:** Protocol used for browser–server communication; GET reads, POST creates, PATCH updates.
- **Database:** Structured persistent storage; here it is the `volunteer.db` file.
- **SQL:** Language used to create, query and update relational database records.

## 3. Complete architecture
```
User types in a form
  ↓
public/index.html (HTML input elements)
  ↓
public/app.js (submit event and fetch('/api/...'))
  ↓ HTTP JSON request
server.js (checks login, validates input, executes SQL)
  ↓
volunteer.db (SQLite database file)
  ↓
server.js returns JSON result
  ↓
app.js calls refresh() and rebuilds visible tables
```
**Important:** HTML/JavaScript in the browser never opens the SQLite file directly. Only the backend has database access.

## 4. Explain each file

### `public/index.html` — structure
- `<!doctype html>` tells the browser this is modern HTML.
- `<html lang="en">` identifies the document language.
- `<head>` contains metadata, page title and the stylesheet link.
- `<link rel="stylesheet" href="/style.css">` loads the styling.
- `<aside class="sidebar">` contains the left navigation.
- `<nav>` contains buttons that switch between modules.
- `<main>` contains the visible website content.
- `<section id="volunteers">` is the volunteer module; other sections hold events, enrolments, attendance, tasks, reports and feedback.
- `<form id="volunteer-form">` groups volunteer fields and allows form submission.
- `name="email"` tells JavaScript which key to send to the API.
- `<tbody id="volunteer-list">` is an empty table body populated by JavaScript using database results.
- `<script src="/app.js"></script>` loads the frontend logic after the HTML.

### `public/style.css` — design
- `:root` stores reusable colour variables such as `--green`.
- `*{box-sizing:border-box}` makes sizing predictable.
- `.shell{display:grid;grid-template-columns:240px 1fr}` creates sidebar and main content columns.
- `.card` styles white information panels.
- `.fields{display:grid...}` arranges form inputs in two columns.
- `@media(max-width:900px)` changes layout on smaller screens.
- `@media(max-width:550px)` changes forms to a single column on phones.

### `public/app.js` — interactions
- `'use strict'` enables stricter JavaScript error checks.
- `const $ = id => document.getElementById(id)` is a short helper for finding an element.
- `state` holds the latest information returned by the backend in memory while the page is open.
- `request(route,method,data)` uses `fetch()` to call an API endpoint; `JSON.stringify(data)` converts an object to JSON.
- `await response.json()` converts the server's JSON response back into a JavaScript object.
- `show(page)` changes which page section is visible.
- `refresh()` requests all data again and updates counts, dropdown lists and tables.
- `table()` builds table rows using `textContent`, which avoids interpreting user names as HTML.
- `connectForm()` attaches a submit event to a form, prevents browser navigation, posts data to the backend and refreshes the screen.
- `attendance-list` creates a status dropdown and hours input for each enrolment. Save sends `PATCH /api/enrollments/:id`.
- `download-report` converts report rows into CSV text and downloads a file.

### `server.js` — backend and database
- `require('node:http')` loads Node's built-in HTTP server (not Express).
- `require('node:fs')` reads static HTML/CSS/JS files from disk.
- `require('node:path')` safely constructs file paths.
- `require('node:crypto')` supports password hashing and random session identifiers.
- `require('node:sqlite')` opens the SQLite database using Node's built-in database module.
- `new DatabaseSync(...)` creates or opens `volunteer.db`.
- `db.exec('CREATE TABLE IF NOT EXISTS ...')` defines database tables once; existing data remains.
- `PRIMARY KEY` uniquely identifies each record; `FOREIGN KEY` connects related records.
- `UNIQUE(volunteer_id,event_id)` prevents duplicate enrolments in the same event.
- `hash(password,salt)` uses `scrypt` so the database stores password hashes, not plain-text passwords.
- `sessions = new Map()` stores active session tokens while the server runs.
- `reply()` sends JSON with an HTTP status code.
- `body()` reads and parses incoming JSON request data.
- `auth()` checks whether a request contains a valid login cookie.
- `api()` matches the HTTP route and method and performs the appropriate SQL operation.
- `db.prepare(...).run(...)` executes INSERT or UPDATE SQL with bound parameters.
- `db.prepare(...).all(...)` reads several database rows.
- `db.prepare(...).get(...)` reads one row.
- `http.createServer(...)` starts the backend and serves the website.
- `.listen(PORT)` waits for browser requests on port 3000.

## 5. Database table explanations
| Table | Main fields | Purpose |
|---|---|---|
| `admins` | id, email, salt, hash | Stores administrator login credentials securely |
| `volunteers` | id, name, email, phone, interest | Volunteer information |
| `events` | id, title, event_date, location | NGO events |
| `enrollments` | id, volunteer_id, event_id, attendance, hours | Connects volunteers to events and records participation |
| `tasks` | id, enrollment_id, title, status | Work assigned to an enrolled volunteer |
| `feedback` | id, respondent, role, rating, comments | Website feedback entered after testing |

**Relationships:** One volunteer can have many enrolments. One event can have many enrolments. Each enrolment connects exactly one volunteer and one event. An enrolment can have multiple tasks.

## 6. Explain a real API request step by step
**Example: adding a volunteer**
1. The user fills in Full Name and Email on the Volunteers page.
2. The form submit event in `app.js` runs.
3. `formData()` collects field values into a JavaScript object.
4. `request('volunteers','POST',data)` sends JSON to `/api/volunteers`.
5. `server.js` checks that the user is logged in.
6. The backend checks that name and email are valid.
7. SQL `INSERT INTO volunteers(name,email,phone,interest) VALUES(?,?,?,?)` saves the record.
8. The database creates a unique volunteer ID.
9. The backend returns HTTP 201 with the new ID.
10. `refresh()` calls `GET /api/volunteers` and displays the newly saved volunteer.

## 7. Explain the key SQL queries
```sql
CREATE TABLE volunteers (...);   -- Create a structure to store volunteers.
INSERT INTO volunteers(name,email,phone,interest) VALUES(?,?,?,?); -- Save a new volunteer.
SELECT * FROM volunteers ORDER BY id DESC; -- Retrieve newest records first.
UPDATE enrollments SET attendance=?, hours=? WHERE id=?; -- Update attendance and hours.
SELECT COALESCE(SUM(hours),0) FROM enrollments WHERE attendance='Present'; -- Calculate actual saved service hours.
```
`?` means a **bound parameter**: the value is passed separately from SQL text, which helps prevent SQL injection.

## 8. Likely viva questions and short answers
**Q: Why this project?** A: Our NGO visit highlighted difficulties with separate registration, attendance and contribution records, so we built a central workspace.

**Q: What is frontend?** A: The pages and controls seen by the user; ours use HTML, CSS and JavaScript.

**Q: What is backend?** A: The server that validates requests, applies rules and performs database queries; ours uses Node.js.

**Q: Is this Express.js?** A: No. This offline version uses Node's built-in HTTP module. Express is a framework we could adopt later; we should not claim to have used it.

**Q: Is this MySQL?** A: No. This version uses SQLite, a real relational SQL database stored in a local file. MySQL would require a separate database service.

**Q: Why SQLite?** A: It gives reliable local persistence without requiring a separate database server during the demonstration.

**Q: How is the data saved after refresh?** A: The backend writes SQL records to `volunteer.db`; refresh retrieves them again using API calls.

**Q: What is an API?** A: A defined route through which the frontend sends requests to the backend, such as `POST /api/events`.

**Q: What is JSON?** A: A text format used to transfer structured data between browser and server.

**Q: What is a primary key?** A: A unique ID identifying a database record.

**Q: What is a foreign key?** A: A field that links a record to a related table, such as `volunteer_id` in `enrollments`.

**Q: Why use GitHub?** A: To store, version and share project source code. The existing GitHub Pages link hosts a static prototype; the new local backend version must run with Node.

**Q: Can GitHub Pages run this database?** A: No. GitHub Pages hosts static assets only; a separate server is needed for the backend.

**Q: Is the system ready for a real NGO?** A: It is a working academic prototype. Public deployment would require stronger security, privacy controls, extensive testing and NGO approval.

**Q: How will you do impact analysis?** A: Ask at least 20 volunteers and one NGO representative to test the website, collect real responses through separate questionnaires, and analyse them with charts.

## 9. Team division
- **Kanishka:** project motivation, NGO findings, frontend, HTML/CSS, user flow, survey analysis.
- **Aishwarya:** backend API, SQLite tables, SQL relationships, login, integration and testing.
- **Both:** demonstrate the complete workflow and answer the core architecture questions.

## 10. Before final submission
- Run the website on the **actual demonstration laptop** and test every major button.
- Confirm that a newly saved volunteer and event persist after restarting Node.
- Check whether your mentor insists on **MySQL and Express** because those were proposed in the synopsis; explain any technology changes accurately.
- Collect genuine survey results; do not invent impact statistics.
- Take screenshots of each tested page and add them to the final report.
- Practice the 8-minute demonstration twice as a team.
