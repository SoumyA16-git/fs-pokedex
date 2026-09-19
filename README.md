# Full Stack Open — Part 11 CI/CD: Pokedex

This repository contains the completed CI/CD implementation for Part 11 of the University of Helsinki [Full Stack Open](https://fullstackopen.com/) course.

---

## 🔗 Links (Exercise 11.23)

- **Live Deployed Application**: [https://fs-pokedex-4yka.onrender.com](https://fs-pokedex-4yka.onrender.com)
- **Health Check Endpoint**: [https://fs-pokedex-4yka.onrender.com/health](https://fs-pokedex-4yka.onrender.com/health)
- **Version Endpoint**: [https://fs-pokedex-4yka.onrender.com/version](https://fs-pokedex-4yka.onrender.com/version)
- **Own Pipeline Repository (Exercises 11.21 & 11.22)**: [https://github.com/SoumyA16-git/fullstackopen-bloglist-ci](https://github.com/SoumyA16-git/fullstackopen-bloglist-ci)

---

## 🛠️ Pipeline Architecture

The CI/CD pipeline is implemented in [`.github/workflows/pipeline.yml`](./.github/workflows/pipeline.yml) and consists of modular jobs:

1. **`build_test_and_lint`**:
   - Checks out the code (`actions/checkout@v4`).
   - Sets up Node.js 20 (`actions/setup-node@v4`).
   - Installs dependencies (`npm install`).
   - Runs ESLint (`npm run eslint`).
   - Executes Jest unit and component tests (`npm test`).
   - Builds the production bundle (`npm run build`).
   - Installs Playwright Chromium browser and runs end-to-end tests (`npm run test:e2e`).
   - Sends a Discord failure notification if any step fails.

2. **`deploy`**:
   - Depends on `build_test_and_lint`.
   - Runs **only on pushes to the `main` branch** when the commit message does **not** contain `#skip` (Exercise 11.14 & 11.16).
   - Triggers automated cloud deployment via Render deploy hook (`RENDER_DEPLOY_HOOK`).
   - Sends a Discord success notification upon completion (Exercise 11.18).

3. **`tag_release`**:
   - Depends on `deploy`.
   - Automatically bumps semantic version (default: `patch`) and pushes a new git tag using [`anothrNick/github-tag-action@1.75.0`](https://github.com/anothrNick/github-tag-action) (Exercise 11.15).
   - Skipped when `#skip` is included in the commit message (Exercise 11.16).

---

## ⏰ Periodic Health Check (Exercise 11.20)

A separate workflow in [`.github/workflows/periodic-health-check.yml`](./.github/workflows/periodic-health-check.yml) runs daily via cron (`0 0 * * *`) and on manual dispatch to ping the deployed `/health` endpoint and verify application availability.

---

## 💻 Local Development Commands

- `npm install` — Install all dependencies
- `npm start` — Run Webpack dev server (`http://localhost:8080`)
- `npm run eslint` — Run ESLint style checks
- `npm test` — Run Jest unit & component tests
- `npm run build` — Create production Webpack bundle (`dist/`)
- `npm run start-prod` — Run production Express server on port 5001
- `npm run test:e2e` — Run Playwright end-to-end test suite
