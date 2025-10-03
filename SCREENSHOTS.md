# Screenshots to Capture (Submission Checklist)

1) **CI (PR to main)**
   - Frontend CI workflow run: green runs for `lint`, `test`, and `build`.
   - Backend  CI workflow run: green runs for `lint`, `test`, and `build`.
   - A failed test on a PR showing the workflow **fails** (to satisfy rubric).

2) **CD (merge to main)**
   - Frontend CD: `lint` → `test` → `build_and_push` (ECR image) → `deploy` (kubectl).
   - Backend  CD: `lint` → `test` → `build_and_push` (ECR image) → `deploy` (kubectl).

3) **AWS ECR**
   - Repositories list showing `movie-frontend` and `movie-backend`.
   - Pushed image tags (matching your commit SHA short tag).

4) **EKS / kubectl**
   - `kubectl get deploy,svc -n <namespace>` (frontend & backend visible).
   - `kubectl describe svc backend-svc` and `kubectl describe deployment backend-deployment`.
   - `kubectl describe svc frontend-svc` and `kubectl describe deployment frontend-deployment`.
   - `kubectl get pods -n <namespace>` (all Running).

5) **Application Working**
   - **Backend API** in browser or curl/PowerShell: `GET /movies` shows list.
   - **Frontend** page open in browser, with Movies list visible (proves env URL is correct).

6) **Secrets**
   - Redacted screenshot of Settings → Secrets & variables → Actions (names only, values hidden).

7) **Optional**
   - OIDC role trust relationship in IAM (GitHub OIDC provider).
