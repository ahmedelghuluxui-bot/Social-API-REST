# 🧪 GoRest API – Postman Test Suite (Newman CI)

A complete **Postman collection** for testing the [GoRest REST API](https://gorest.co.in), designed to run locally or automatically via **GitHub Actions** using **Newman**.

---

## 📁 Repository Structure

```
.
├── GoRest_API.postman_collection.json   # Postman collection with all tests
├── .github/
│   └── workflows/
│       └── newman.yml                  # GitHub Actions CI workflow
└── README.md
```

---

## 🔗 API Under Test

**Base URL:** `https://gorest.co.in/public/v2`

| Resource   | Endpoint               |
|------------|------------------------|
| Users      | `/users`               |
| Posts      | `/posts`               |
| Comments   | `/comments`            |
| Todos      | `/todos`               |

---

## ✅ Test Coverage

| Folder          | Tests Included                                          |
|-----------------|---------------------------------------------------------|
| **Users**       | List, Create, Get by ID, Update (PATCH), Delete, 404 check after delete |
| **Posts**       | Create user, Create post, List user posts, List all posts |
| **Comments**    | Create comment on post, List post comments              |
| **Todos**       | Create todo, List user todos                            |
| **Negative**    | 401 Unauthorized, 404 Not Found, 422 Validation Error   |

---

## 🚀 Running Locally

### Prerequisites

```bash
npm install -g newman
npm install -g newman-reporter-htmlextra   # optional: for HTML reports
```

### Run the Collection

```bash
newman run GoRest_API.postman_collection.json \
  --env-var "GOREST_TOKEN=YOUR_ACCESS_TOKEN_HERE"
```

### Run with HTML Report

```bash
newman run GoRest_API.postman_collection.json \
  --env-var "GOREST_TOKEN=YOUR_ACCESS_TOKEN_HERE" \
  -r htmlextra \
  --reporter-htmlextra-export report.html
```

> Open `report.html` in your browser to view full results.

---

## ⚙️ GitHub Actions – Automated CI

### Step 1: Add Your Token as a GitHub Secret

1. Go to your repository → **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Name: `GOREST_TOKEN`
4. Value: your GoRest access token (get one at [gorest.co.in](https://gorest.co.in))

### Step 2: Create the Workflow File

Create `.github/workflows/newman.yml` with the following content:

```yaml
name: GoRest API Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 6 * * *'   # Runs daily at 06:00 UTC

jobs:
  newman-tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install Newman & HTML reporter
        run: |
          npm install -g newman
          npm install -g newman-reporter-htmlextra

      - name: Run Postman Collection
        run: |
          newman run GoRest_API.postman_collection.json \
            --env-var "GOREST_TOKEN=${{ secrets.GOREST_TOKEN }}" \
            -r cli,htmlextra \
            --reporter-htmlextra-export newman-report.html \
            --reporter-htmlextra-title "GoRest API Test Report"

      - name: Upload Test Report
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: newman-html-report
          path: newman-report.html
```

### Workflow Triggers

| Trigger         | When it runs                              |
|-----------------|-------------------------------------------|
| `push`          | Every push to `main`                      |
| `pull_request`  | Every PR targeting `main`                 |
| `schedule`      | Daily at 06:00 UTC                        |

---

## 🔑 Getting a GoRest Access Token

1. Visit [https://gorest.co.in](https://gorest.co.in)
2. Sign in with **GitHub**, **Google**, or **Microsoft**
3. Copy your **Access Token**
4. Use it as the `GOREST_TOKEN` value

> ⚠️ **Never commit your token to the repository.** Always use GitHub Secrets.

---

## 📋 Authentication

All mutating requests (`POST`, `PUT`, `PATCH`, `DELETE`) require a Bearer token:

```
Authorization: Bearer YOUR_TOKEN
```

Read-only `GET` requests also benefit from a token to see your own data.

---

## 📊 HTTP Status Codes Used in Tests

| Code | Meaning                        |
|------|--------------------------------|
| 200  | OK                             |
| 201  | Resource created               |
| 204  | Deleted successfully (no body) |
| 401  | Unauthorized                   |
| 404  | Resource not found             |
| 422  | Validation failed              |

---
## 📝 Team Members
Bashar Abu Hawilah  Users module
Ahmed El Ghul       Posts module
Moamin Musallam     Todos module
Salah AL Din        Comments module 

## 📝 Notes

- Data created via your token is **only visible to you**
- All data is **reset daily** by GoRest
- Maximum **100 results per page** (`per_page` parameter)
- Rate limiting headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`
- Pagination headers: `X-Pagination-Total`, `X-Pagination-Pages`, `X-Pagination-Page`, `X-Pagination-Limit`

---

## 📄 License

MIT – free to use and modify.
