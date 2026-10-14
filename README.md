# CareBridge AI — VS Code demo (Doctor and Patient sign-in)

CareBridge AI is a **fictional-data prototype** for organizing post-discharge recovery tasks and health-history records. It now opens on a sign-in page, and **doctors and patients get different screens and different permissions**. SignalCare is a transparent, deterministic checklist-rule module; HealthHistory Lens provides a cautious history timeline and comparison-candidate gate.

## Publish as a live website on GitHub Pages

This repository includes a GitHub Actions workflow (`.github/workflows/deploy.yml`) that builds and publishes the app when you push to the `main` branch.

1. Create a new **public** repository on GitHub (for example, `carebridge-ai`).
2. Upload the **contents** of this project folder to the repository root (so `package.json`, `index.html`, `src/`, `vite.config.js`, and `.github/` are at the root). Do not upload the ZIP as the only repository file.
3. In GitHub, open **Settings → Pages**. Under **Build and deployment**, select **GitHub Actions** as the source.
4. Open the **Actions** tab and wait for **Deploy CareBridge AI to GitHub Pages** to finish successfully.
5. Open **Settings → Pages** to find your live website link. Future pushes to `main` automatically redeploy it.

### Important demo limitations

- This is a browser-only prototype. Changes are saved to the current browser's local storage; they do **not** sync across devices or different users.
- Demo usernames and passwords are included in the frontend bundle. GitHub Pages does not provide a backend, secure authentication, or a shared database. Use only the fictional sample data; never enter real patient information.
- For a genuinely shared multi-user app, a backend API, database, server-side authentication, and access controls must be added before using real health data.

## Run locally


1. Install Node.js (LTS).
2. Extract this folder and open it in VS Code.
3. Open Terminal in the project folder (the folder containing `package.json`).
4. Run:
   ```bash
   npm install
   npm run dev
   ```
5. Open the local URL printed by Vite. You land on the sign-in page.
6. Check a production bundle with `npm run build`.

## Demo accounts (all fictional)

| Role    | Username    | Password     | Name            | Sees                         |
|---------|-------------|--------------|-----------------|------------------------------|
| Patient | `jordan`    | `patient123` | Jordan Davis    | Own plan and records only    |
| Patient | `priya`     | `patient123` | Priya Nair      | Own plan and records only    |
| Patient | `sam`       | `patient123` | Sam Okafor      | Own plan and records only    |
| Doctor  | `dr.patel`  | `doctor123`  | Dr. Maya Patel  | Jordan and Priya             |
| Doctor  | `dr.ortega` | `doctor123`  | Dr. Luis Ortega | Sam                          |

Choose **Patient** or **Doctor** on the sign-in page first. A patient account will not sign in under Doctor, and the other way round. The sign-in page also has "Fill in" buttons for each demo account.

## What each role can do

| | Patient | Doctor |
|---|---|---|
| Screens | Overview, Recovery plan, HealthHistory Lens, SignalCare, Care team | Caseload, Patient plan, Patient history, SignalCare, My profile |
| Whose data | Own only | Only patients assigned to that doctor |
| Tasks | Mark done, acknowledge, add personal tasks | Assign tasks, remove tasks. Cannot mark done or acknowledge for a patient |
| Health records | Add self-reported records (source defaults to "Self-reported") | Add clinical records (source defaults to the doctor's name) |
| Check-ins | See the next one | Schedule check-ins for own patients |
| SignalCare | Own findings, with an Acknowledge button | Findings grouped by patient, read-only |
| Export | Own report | Caseload report |

## Sample database

The database is one JSON file: **`src/data/carebridge-db.json`** (a standalone copy is delivered next to this project as `carebridge_sample_database.json`).

| Table | What it holds |
|---|---|
| `meta` | Name, `version`, privacy notice |
| `users` | Sign-in accounts: `role` (`doctor`/`patient`), `username`, `password`, `name`, `profileId` |
| `doctors` | Name, speciality, clinic |
| `patients` | Name, `doctorId` (who is responsible), care nurse, recovery focus, discharge date |
| `tasks` | Recovery tasks per `patientId`: owner, due date, done, acknowledged, who assigned it, source instruction |
| `history` | Health records per `patientId`: date, type, result, source |
| `appointments` | Check-ins per `patientId` and `doctorId` (drives "Next check-in") |

How it is used:

- On first load the app copies this file into the browser's local storage (`cb_db`). All changes are saved there, so they survive a refresh.
- **Reset sample data** (sidebar icon, or the link on the sign-in page) copies the file in again.
- To change the sample data, edit the JSON file, then either increase `meta.version` or press Reset. The app reloads the file whenever the saved copy has a different version.
- All records are linked by id (for example `tasks[].patientId` matches `patients[].id`). Keep those links valid when you add rows.

## What is implemented

- Sign-in page with a Patient / Doctor choice, error messages, and a session that lasts for the browser tab.
- Role-based screens, plus a permission layer (`src/lib/access.js`) that every add, remove, complete and schedule action goes through.
- SignalCare rule findings for missing owner, missing confirmed calendar due date, unacknowledged open task and an open task with a recorded past date. Each finding exposes a rule ID and explanation.
- Acknowledging a task is separate from marking it completed.
- HealthHistory Lens groups records with matching labels as *candidate* comparisons and flags differing sources for human review.
- Add tasks and records, JSON report export, reset, responsive layout.

## Project layout

```
src/
  data/carebridge-db.json   sample database
  lib/store.js              load/save database and session
  lib/access.js             sign-in check and role permissions
  lib/rules.js              SignalCare rules, comparison gate
  lib/dates.js              local-date helpers
  components/               Login, forms, shared UI
  views/                    shared, patient and doctor screens
  App.jsx                   session, role-based shell, actions
```

## Important limitations

- **Sign-in is a demonstration, not security.** Passwords are plain text in the JSON file and the checks run in the browser, so anyone can read or edit them. A real system needs a server that checks hashed passwords and enforces these rules on every request, plus audit logging.
- SignalCare is deterministic local rule logic, **not a trained AI model**, clinical risk predictor or validated safety system.
- History comparison does not verify units, test methodology, specimen, reference intervals or clinical context. Candidate grouping is not a clinical conclusion.
- Data is stored only in this browser. There is no backend, shared messaging, audit service or hospital integration.
- Use fictional data only. Do not enter real patient information or real credentials. The app does not diagnose, prescribe or replace clinician instructions.

## Suggested verification

See `TEST_PLAN.md`. Run `npm install` and `npm run build`, then work through the plan in a browser. No end-to-end browser suite is bundled.
