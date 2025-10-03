# Movie Picture Pipeline — CI/CD Starter

This pack gives you **4 GitHub Actions workflows** (frontend/backend CI & CD), a **DRY composite action**, and **Kubernetes manifests** to satisfy your rubric.

> Put this entire folder at the root of your repo, adjust folder names (`./frontend`, `./backend`) if different, add required GitHub Secrets, and push.

---

## Folder Map
```
.github/
  actions/
    node-setup-cache/action.yaml     # reusable action (Node + cache + install)
  workflows/
    frontend-ci.yaml                 # Frontend Continuous Integration
    backend-ci.yaml                  # Backend Continuous Integration
    frontend-cd.yaml                 # Frontend Continuous Deployment
    backend-cd.yaml                  # Backend Continuous Deployment
k8s/
  backend-deployment.yaml
  backend-service.yaml
  frontend-deployment.yaml
  frontend-service.yaml
```

---

## Required GitHub **Secrets**
Set these in **Repo → Settings → Secrets and variables → Actions**

**Common**
- `AWS_REGION` — e.g., `us-east-1`
- `AWS_ACCOUNT_ID` — your 12-digit AWS account ID
- `AWS_ROLE_TO_ASSUME` — IAM role ARN with ECR push & EKS access (for OIDC)
- `EKS_CLUSTER_NAME` — your EKS cluster name
- `K8S_NAMESPACE` — e.g., `default`

**Frontend**
- `ECR_REPO_FRONTEND` — ECR repo name for the frontend (e.g., `movie-frontend`)
- `REACT_APP_MOVIE_API_URL` — **public backend URL** the frontend calls (e.g., `http://backend-svc.default.svc.cluster.local:8080/api/movies` or your external URL)

**Backend**
- `ECR_REPO_BACKEND` — ECR repo name for the backend (e.g., `movie-backend`)

---

## Triggers & Rules
- **CI** workflows run on `pull_request` to `main` **and** can be run manually.
  - Lint + Test run **in parallel**.
  - Build runs **only after** (`needs`) Lint & Test succeed.
- **CD** workflows run on **push (merge) to main** and can be run manually.
  - Lint & Test → Build & Push to **ECR** → Deploy with **kubectl** to **EKS**.
  - Frontend build uses **`--build-arg REACT_APP_MOVIE_API_URL`** to inject env.

---

## Kubernetes Notes
- Manifests in `k8s/` are minimal. `kubectl set image` updates Deployment to the new ECR image tag.
- Frontend ConfigMap provides `REACT_APP_MOVIE_API_URL` for runtime access (good for NGINX served SPAs). Your app might consume at build-time as well.

---

## Local Test (Optional)
```bash
# Frontend
pushd frontend
npm ci
npm run lint
npm test -- --ci --watchAll=false
docker build -t local-frontend .
popd

# Backend
pushd backend
npm ci
npm run lint
npm test -- --ci --watchAll=false
docker build -t local-backend .
popd
```

---

## Simulate Test Failure (Rubric)
Break one test and open a PR:
- **Expected:** CI should **fail** and block merge.
- Fix test → push → CI passes.
CD should only run on main after merge.

---

## ECR & EKS Pre-reqs
- ECR repos exist:
  - `aws ecr create-repository --repository-name movie-frontend`
  - `aws ecr create-repository --repository-name movie-backend`
- OIDC trust for GitHub + IAM role (`AWS_ROLE_TO_ASSUME`) with:
  - `ecr:*` (push) on your repos
  - `eks:DescribeCluster` + `eks:AccessKubernetesApi`
- EKS cluster and namespace ready; nodes can pull from ECR.

---

## What to Change
- If your code directories differ, update `working-directory` and Docker build context paths.
- If React build needs extra args, add them in `frontend-cd.yaml` under `docker build`.
- If backend needs env, add `env:` to `backend-deployment.yaml`.

---

## Bonus Ideas (Stand-Out)
- Add a PR comment job that posts image tag & links.
- Cache Docker layers with `docker/build-push-action@v6` and `cache-from` (optional).
- Add Snyk or npm audit scan job.
