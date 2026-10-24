# nodejs-demo-app — CI/CD with GitHub Actions & Docker Hub

A small Node.js web app with a CI/CD pipeline that automatically **tests → builds → pushes** a Docker image to Docker Hub on every push to `main`.

## Tech stack
GitHub · GitHub Actions · Node.js 22 · Docker · Docker Hub

## Project structure
```
.
├── .github/workflows/main.yml   # CI/CD pipeline
├── src/
│   ├── app.js                   # HTTP routes (/, /health)
│   └── server.js                # Starts the server
├── test/app.test.js             # Unit tests (Node's built-in test runner)
├── Dockerfile
├── .dockerignore
├── package.json
└── INTERVIEW_QUESTIONS.md       # Answers to the task's interview questions
```

## Pipeline overview (`.github/workflows/main.yml`)
Triggered on **push to `main`** (or manually via *Run workflow*).

| Job | What it does |
|-----|--------------|
| `test` | Checkout → set up Node 22 (with npm cache) → `npm ci` → `npm test` |
| `build-and-push` | Runs only if `test` passes (`needs: test`). Builds the image → smoke-tests the container's `/health` endpoint → logs in to Docker Hub → pushes tags `latest` and `sha-<short-commit>` |

If tests or the smoke test fail, nothing is pushed.

## Setup
1. Create a Docker Hub **access token**: Docker Hub → Account Settings → Personal access tokens (Read & Write).
2. In this GitHub repo: **Settings → Secrets and variables → Actions → New repository secret**, add:
   - `DOCKERHUB_USERNAME` — your Docker Hub username
   - `DOCKERHUB_TOKEN` — the access token from step 1
3. Push to `main` and watch the **Actions** tab.

## Run locally
```bash
npm ci
npm test
npm start                 # http://localhost:3000

# with Docker
docker build -t nodejs-demo-app .
docker run -p 3000:3000 nodejs-demo-app
curl localhost:3000/health
```

Pull the published image:
```bash
docker pull <your-dockerhub-username>/nodejs-demo-app:latest
```

## Test the pipeline locally (optional)
Use [`act`](https://github.com/nektos/act) to run the workflow in Docker:
```bash
act push -j test
```

## Screenshots
Add screenshots of the successful Actions run and the Docker Hub repo in a `screenshots/` folder.
![App running](apprunningimage)
![GitHub Actions run](githubworkflow)
![GitHub Docker image](dockerimage)
