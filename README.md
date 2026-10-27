# NEXUS 404

NEXUS 404 is a local, single-user career-discovery demo. It combines live job listings from Remotive and Arbeitnow with technology stories from Hacker News, and stores your profile, skills, saved roles, application tracker, assessment reflection, and learning roadmap in SQLite.

## Requirements

- Node.js 22 LTS or newer (includes `npm`)
- Windows 10/11, macOS, or Linux
- Internet access for the three public data providers

## Run locally on Windows

1. Install Node.js LTS from [nodejs.org](https://nodejs.org/), then restart VS Code if it was open during installation.
2. Open this project folder in VS Code.
3. Open **Terminal → New Terminal** and run:

   ```powershell
   npm install
   ```

4. Start the development servers:

   ```powershell
   npm run dev
   ```

5. Open the Vite URL printed in the terminal (normally `http://127.0.0.1:5173`).

The Vite development server proxies `/api` requests to the Express API on `http://127.0.0.1:3001`. The SQLite database is created automatically at `server/data/nexus404.sqlite` the first time the backend starts.

For a production-style local run:

```powershell
npm run build
$env:NODE_ENV = "production"
npm start
```

Then open `http://127.0.0.1:3001`. To configure optional settings, copy `.env.example` to `.env`. The default ports and database path work without environment configuration.

## Features

- **Jobs:** Unified search, source/location/category/remote filters, sorting, and source attribution for live Remotive and Arbeitnow listings. Original application links open on the provider's site.
- **Matches:** Rule-based comparison of skills explicitly mentioned in available job descriptions against the skills in your profile. The recognized skill terms are shown on the matches page; an unavailable score is not treated as a failed match.
- **Technology news:** Top, new, and Show HN stories from the official Hacker News Firebase API. Stories are kept separate from job listings.
- **Career profile and skills:** Edit profile preferences, add technical and soft skills, and save a career reflection.
- **Saved jobs and application tracker:** SQLite-backed records with status updates, notes, search, editing, and deletion.
- **Learning roadmap:** Recommendations are generated from live descriptions returned for the selected target role, then compared with your saved technical skills. If a listing does not expose recognized skills, the roadmap says so rather than inventing requirements.
- **Dashboard:** Counts come from current API results or saved database records; unavailable data is not replaced with sample listings or fabricated counts.

## API endpoints

The backend exposes:

- `GET /api/health`
- `GET /api/jobs`, `GET /api/jobs/remotive`, `GET /api/jobs/arbeitnow`
- `GET /api/news`, `GET /api/news/top`
- `GET` and `PUT /api/profile`
- `GET` and `PUT /api/skills`
- `GET /api/matches`, `POST /api/matches/analyze`
- `GET /api/saved-jobs`, `POST /api/saved-jobs`, `DELETE /api/saved-jobs/:id`
- `GET /api/applications`, `POST /api/applications`, `PATCH /api/applications/:id`, `DELETE /api/applications/:id`
- `GET`, `POST /api/roadmap`, `PATCH /api/roadmap/:id`
- `GET` and `POST /api/assessment`

Job and news API calls use public providers and do not require API keys. Provider failures are reported to the user; listings and stories are not replaced with mock results.

## Data and security notes

This educational demo has a single local profile and no login, user isolation, or production authentication. Do not expose it to the public internet or enter sensitive information. The API validates input, limits request sizes, uses parameterized SQLite statements, restricts CORS to the configured local client origin, applies timeouts to provider calls, and treats provider descriptions as text rather than executable HTML. Use real authentication, per-user authorization, HTTPS, and production-grade deployment controls before adapting it for multiple users.

The public job boards can change or remove listings. Saved job snapshots remain in the local database, but check the original posting before applying. Job matches are transparent keyword comparisons, not eligibility decisions or guarantees of employment.

## Validation status

Run `npm run build` to build the React frontend. Run `npm run dev`, then visit `/api/health` on port 3001 to check the backend and initialized database. Provider APIs require internet access; the app displays a provider-specific or general retry state if they are unavailable.
