# Postman API Automation — Restful Booker

![Newman API Tests](https://github.com/danieloyebode/postman-restfulbooker-api/actions/workflows/newman.yml/badge.svg)
![Node](https://img.shields.io/badge/node-%3E%3D20-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)

API automation test suite for [Restful-Booker](https://restful-booker.herokuapp.com) — a CRUD hotel booking API designed for API testing practice. Built with **Postman** collections and executed via **Newman CLI**, with rich HTML reporting and GitHub Actions CI.

> Built by **Daniel Oyebode** — Senior QA Automation Engineer | ISTQB Certified | Fintech Specialist

---

## Test Coverage

**16 requests · 50+ test assertions** across 5 folders:

| Folder | Requests | What's Tested |
|---|---|---|
| Health Check | 1 | Ping endpoint returns 201, response time SLA |
| Auth | 2 | Create token with valid creds, bad credentials returns error message |
| Booking — Read | 4 | Get all IDs, filter by name, get by valid ID (schema validation), get non-existent ID (404) |
| Booking — Create | 2 | Create with valid payload (schema + data match), missing required field |
| Booking — Update | 4 | PUT full update, PUT without auth (403), PATCH partial update, PATCH without auth (403) |
| Booking — Delete | 3 | DELETE with valid token, GET deleted booking confirms 404, DELETE without auth (403) |

---

## Tech Stack

| Tool | Purpose |
|---|---|
| [Postman](https://postman.com) | Collection authoring & manual execution |
| [Newman](https://github.com/postmanlabs/newman) | CLI test runner |
| [newman-reporter-htmlextra](https://github.com/DannyDainton/newman-reporter-htmlextra) | Dark-themed rich HTML report |
| GitHub Actions | CI/CD — push, PR, daily 07:00 UTC |

---

## Project Structure

```
postman-restfulbooker-api/
├── .github/
│   └── workflows/
│       └── newman.yml                          # CI/CD pipeline
├── reports/                                    # Generated — gitignored
├── RestfulBooker_API_Suite.postman_collection.json
├── RestfulBooker.postman_environment.json
├── package.json
└── README.md
```

---

## Getting Started

### Prerequisites
- Node.js v20+
- npm

### Install

```bash
git clone https://github.com/danieloyebode/postman-restfulbooker-api.git
cd postman-restfulbooker-api
npm install
```

### Run with HTML report

```bash
npm test
```

Report generated at `reports/api-test-report.html`.

### Run in CI mode (HTML + JUnit XML)

```bash
npm run test:ci
```

### Run verbose (detailed console output)

```bash
npm run test:verbose
```

### Import into Postman

1. Open Postman → **Import** → select `RestfulBooker_API_Suite.postman_collection.json`
2. **Import** → select `RestfulBooker.postman_environment.json`
3. Select the **Restful Booker — Environment** from the dropdown
4. Run with **Collection Runner** — ensure folders run in order (Auth must run before Update/Delete)

---

## API Endpoints Covered

| Method | Endpoint | Auth Required |
|---|---|---|
| GET | `/ping` | No |
| POST | `/auth` | No |
| GET | `/booking` | No |
| GET | `/booking?firstname=John` | No |
| GET | `/booking/:id` | No |
| POST | `/booking` | No |
| PUT | `/booking/:id` | Yes — Cookie token |
| PATCH | `/booking/:id` | Yes — Cookie token |
| DELETE | `/booking/:id` | Yes — Cookie token |

---

## Design Decisions

**Auth-first execution order** — The collection is structured so the Auth folder always runs first. The `Create Token` request extracts the token from the response and saves it to `{{authToken}}` environment variable. All PUT, PATCH, and DELETE requests then inject it via the `Cookie: token=...` header. No manual token copying.

**Chained booking ID** — `Create Booking` stores the newly created `{{bookingId}}` in the environment. All subsequent Update and Delete requests use this same ID, forming a complete lifecycle chain: Create → Read → Update → Patch → Delete → Verify 404.

**Known API quirks documented in tests** — Restful Booker has intentional quirks: DELETE returns `201 Created` instead of `204 No Content`. Tests are written to assert what the API actually returns, with comments explaining the deviation from REST conventions. This demonstrates real-world API testing maturity — knowing when a "bug" is a design decision.

**Negative coverage on every protected endpoint** — Every endpoint requiring auth has a corresponding test without the token. This is core QA practice for any API with access control.

**Response time SLA assertion** — The health check and create booking requests include `responseTime < 5000ms` assertions. Restful Booker runs on a free Heroku dyno that cold-starts, so the threshold is generous — in a production suite this would tighten to 1000–2000ms.

---

## CI/CD

Tests run automatically on:
- Every **push** to `main` or `develop`
- Every **pull request** targeting `main`
- **Daily at 07:00 UTC** (scheduled regression)

---

## Author

**Daniel Oyebode**  
Senior QA Automation Engineer | ISTQB Certified Tester (No. 00542143)  
[LinkedIn](https://www.linkedin.com/in/daniel-inioluwa-oyebode-ambcs-736b9115a/)· [GitHub](https://github.com/OyebodeDaniel)
