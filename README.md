# Trackly

A job application tracker with built-in analytics, a kanban board, and a resume-vs-job-posting analyzer. Track every application in one place, see what is actually working, and get nudged when an application goes quiet.

<!-- Add a screenshot: upload dashboard.png to the repo, then replace this line with: ![Dashboard](dashboard.png) -->

## Features

- **Dashboard:** donut chart of applications by stage, plus cards for interviews booked, waiting for reply, and offers.
- **Table and kanban board:** filter by stage, search by company or role, change a stage from a dropdown, or drag cards between columns.
- **Add, edit, delete:** applications are saved to a local SQLite database.
- **Follow-up reminders:** applications stuck in "Applied" for 7+ days are flagged, with a one-click "Mark followed up".
- **Analytics:** conversion funnel, response rate per resume version (A/B), response rate per source, and a weekly application cadence against a goal you set.
- **Insights:** best source and best resume so far, and a response-likelihood estimate for pending applications (a Laplace-smoothed statistical estimate from your own history, so treat it as directional on small samples).
- **Job fit and keyword gaps:** paste your resume and a job posting to get a fit score, matched keywords, and missing keywords with suggestions.
- **Settings:** weekly goal, light/dark theme, CSV export.

## Tech stack

- **Frontend:** vanilla HTML, CSS, and JavaScript (single page, no build step)
- **Backend:** Node.js and Express
- **Database:** SQLite via `better-sqlite3`
- **AI (optional):** Claude API (Messages endpoint) for the job-fit analysis

## Run locally

Requires [Node.js](https://nodejs.org) 18 or newer.

```bash
git clone https://github.com/deboshruti17/trackly.git
cd trackly
npm install
npm start
```

Open http://localhost:3000. The database file (`jobs.db`) is created automatically on first run with a few sample applications. To reset the demo data, stop the server, delete `jobs.db`, and start again.

## Optional: AI-powered job fit

Without an API key, the Job fit page uses a local keyword-overlap check. To have Claude analyze the match instead, create an API key in the [Anthropic Console](https://console.anthropic.com) and set it before starting the server.

PowerShell (Windows):

```powershell
$env:ANTHROPIC_API_KEY="your-key"; npm start
```

macOS / Linux:

```bash
ANTHROPIC_API_KEY="your-key" npm start
```

The key stays on the server and is never sent to the browser. Set `CLAUDE_MODEL` to use a different model. API usage is billed to your Anthropic account.

## API

| Method | Endpoint | Purpose |
| --- | --- | --- |
| GET | `/api/applications` | List all applications |
| POST | `/api/applications` | Add an application (`company` and `role` required) |
| PATCH | `/api/applications/:id` | Edit an application; also resets its follow-up timer |
| DELETE | `/api/applications/:id` | Delete an application |
| GET | `/api/stats` | Funnel, response rates, weekly cadence, follow-ups, insights |
| GET / PUT | `/api/resume` | Read or save your resume text |
| POST | `/api/fit` | Analyze a resume against a job description |

## Project structure

```
trackly/
├── server.js        # Express server, SQLite schema, API routes
├── package.json
└── public/
    └── index.html   # The whole frontend
```

## Limitations

- Single user and no login, so it is meant to run on your own machine.
- Response rates count Screening, Interview, or Offer as a response. Rejected applications are not counted, because the app does not record how far they got.
- The response-likelihood estimate needs a few dozen applications before it means much.

## Roadmap

- Login and multi-user support
- Browser extension to clip jobs from LinkedIn, Greenhouse, and Lever
- Email reminders for stale applications
- Save the job-fit score on each application
- Cover letter and follow-up email drafts
