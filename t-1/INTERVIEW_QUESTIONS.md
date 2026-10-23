# Interview Questions — Task 1

**1. What is CI/CD?**
CI (Continuous Integration) means developers merge code frequently and every change is automatically built and tested. CD (Continuous Delivery/Deployment) means every change that passes is automatically packaged and released (delivery = ready to deploy with a manual approval, deployment = released automatically). Result: faster, safer, repeatable releases.

**2. How do GitHub Actions work?**
A *workflow* (YAML file in `.github/workflows/`) is triggered by an *event* (push, pull request, schedule, manual). It contains one or more *jobs*, each running on a *runner* and made of *steps* that run shell commands or reusable *actions* (e.g. `actions/checkout`).

**3. What are runners?**
Machines that execute jobs. *GitHub-hosted* runners (`ubuntu-latest`, `windows-latest`, `macos-latest`) are fresh VMs per job. *Self-hosted* runners are your own servers, useful for custom hardware, private network access, or cost control.

**4. Difference between jobs and steps?**
A job is a group of steps that runs on one runner; jobs run in parallel by default and can be ordered with `needs`. Steps run sequentially inside a job and share the same filesystem. Different jobs do not share files unless you use artifacts or cache.

**5. How to secure secrets in GitHub Actions?**
- Store them in *Settings → Secrets and variables → Actions* (never in code) and use `${{ secrets.NAME }}`.
- Use environment secrets with required reviewers for production.
- Give the workflow minimal `permissions:` and use scoped tokens (e.g. Docker Hub access token, not password).
- Be careful with third-party actions (pin versions/SHAs) and don't `echo` secrets; secrets are not passed to workflows from forks.
- Prefer OIDC short-lived credentials for cloud providers.

**6. How to handle deployment errors?**
Fail fast with `needs` so bad builds never deploy; add smoke tests/health checks before pushing (as in this pipeline); read logs and re-run failed jobs; use `if: failure()` steps for notifications; keep versioned image tags (`sha-xxxx`) so you can roll back; use environments with approvals for production.

**7. Explain the Docker build-push workflow.**
Checkout code → set up Buildx → log in to the registry (`docker/login-action`) → build the image from the `Dockerfile` and tag it (`docker build -t user/app:tag .`) → push it (`docker push user/app:tag`). In this repo, `docker/build-push-action` does build and push, tags come from `docker/metadata-action`, and layer caching speeds up repeat builds.

**8. How can you test a CI/CD pipeline locally?**
Use [`act`](https://github.com/nektos/act) to run workflows in Docker on your machine; run the same commands manually (`npm ci && npm test`, `docker build`, `docker run`); lint the YAML (e.g. `actionlint`); and test changes on a feature branch or with `workflow_dispatch` before merging to `main`.
