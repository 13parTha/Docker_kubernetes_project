# TaskBoard DevOps Project: 6-Week, 30-Day Plan

Each week has 5 working days (Mon-Fri). Every day builds on the previous one, and the **Friday** of each week finishes and verifies that week's assignment. Plan for roughly 2-3 hours per day.

**Daily routine:** (1) do the task, (2) commit to Git with a clear message, (3) write 3-5 lines in a `NOTES.md` about what you learned or what broke.

---

## Week 1: Docker

**Weekly assignment:** `docker compose up` runs the full TaskBoard app (frontend + backend + Postgres) locally with optimized, secure images.

| Day | Focus | Tasks | Deliverable |
|-----|-------|-------|-------------|
| Mon | Setup + app skeleton | Install Docker, Git, VS Code. Create the repo (GitLab) with the folder structure. Build a minimal backend (FastAPI/Node) with `GET /health` and `GET/POST /tasks` (in-memory for now) | Backend runs locally, code pushed to GitLab |
| Tue | Backend Dockerfile | Write a Dockerfile for the backend. Add `.dockerignore`, pin the base image version, run `docker build` and `docker run -p` | Backend runs in a container |
| Wed | Frontend + multi-stage builds | Create a simple frontend (React or static HTML) that calls the API. Write a multi-stage Dockerfile (build stage, then Nginx). Convert the backend to multi-stage too | Both images build; frontend loads in the browser |
| Thu | Database + Compose | Add Postgres to the backend (replace in-memory storage). Write `docker-compose.yml` with 3 services, a named volume, a custom network, env vars via `.env`, and healthchecks with `depends_on` | `docker compose up` works end to end |
| Fri | Harden + optimize + verify | Run as non-root user, compare slim vs alpine vs distroless image sizes (record in notes), run `docker scout` or Trivy locally, test data persistence after `compose down/up`. Write the README section for local run | **Week 1 assignment complete**, tag `v0.1` |

---

## Week 2: Local Kubernetes (kind)

**Weekly assignment:** the app runs on a local kind cluster, exposed through Ingress, autoscaling under load, and survives pod deletion and rolling updates.

| Day | Focus | Tasks | Deliverable |
|-----|-------|-------|-------------|
| Mon | Cluster + first Deployment | Install `kind` and `kubectl`. Create a cluster (1 control plane + 2 workers, with port mappings). Load images with `kind load docker-image`. Write Namespace and backend Deployment + Service | Backend pods running; `kubectl port-forward` works |
| Tue | Config + database | Add ConfigMap and Secret. Deploy Postgres as a StatefulSet with a PVC and headless Service. Connect the backend to it | Backend talks to Postgres in-cluster |
| Wed | Frontend + Ingress | Deploy the frontend. Install the NGINX ingress controller. Write an Ingress (`/` to frontend, `/api` to backend). Add liveness and readiness probes, plus resource requests/limits for all workloads | App reachable at `http://localhost` |
| Thu | Scaling + resilience | Install metrics-server, add an HPA for the backend. Load test with `hey` or `k6` and watch scaling. Delete pods randomly and watch recovery | HPA scales 1 to N and back |
| Fri | Rollouts + packaging | Practice `kubectl set image`, `rollout status`, `rollout undo`, and `kubectl debug`. Convert manifests into **Helm chart** (or Kustomize base + dev/prod overlays) with values files | **Week 2 assignment complete**, tag `v0.2` |

---

## Week 3: GitLab CI

**Weekly assignment:** every push runs lint, test, build, scan; merges to `main` push a SHA-tagged image to AWS ECR.

| Day | Focus | Tasks | Deliverable |
|-----|-------|-------|-------------|
| Mon | AWS prep + first pipeline | Create an AWS account safety net: IAM admin user (no root use), MFA, **AWS Budget alert**, install AWS CLI. Create an ECR repo manually (Terraform replaces it in Week 4). Write the first `.gitlab-ci.yml` with a `lint` stage (hadolint + eslint/flake8) | Pipeline runs on push |
| Tue | Tests | Write 3-5 unit tests for the backend. Add a `test` stage with dependency caching and JUnit/coverage reports | Failing tests block the pipeline |
| Wed | Build | Add a `build` stage (Kaniko or Docker-in-Docker). Tag images with `$CI_COMMIT_SHORT_SHA` and `latest`. First push to the GitLab registry | Images built in CI |
| Thu | Scan + ECR push | Add a Trivy `scan` stage (fail on CRITICAL). Configure AWS credentials via protected CI variables (or OIDC role if you can). Add a `push` stage to ECR | Image appears in ECR |
| Fri | Branch rules + polish | Rules: merge requests run lint/test/build only, `main` also scans and pushes. Add a protected `main`, a merge-request template, and pipeline status badge. Document the pipeline in the README | **Week 3 assignment complete**, tag `v0.3` |

---

## Week 4: Terraform + AWS

**Weekly assignment:** `terraform apply` builds VPC, ECR, and EKS from code, the app runs on EKS, and `terraform destroy` removes everything cleanly.

| Day | Focus | Tasks | Deliverable |
|-----|-------|-------|-------------|
| Mon | Terraform basics + remote state | Install Terraform. Create the S3 bucket and DynamoDB table (or S3 native locking) for state. Configure the backend. Write the provider and a `variables.tf` | Remote state working |
| Tue | VPC + ECR modules | Build the VPC (2 AZs, public/private subnets, NAT, route tables), either with `terraform-aws-modules/vpc` or your own module. Add an ECR module (replaces the manual repo via `terraform import`) | `terraform apply` creates network + ECR |
| Wed | EKS cluster | Add the EKS module: managed node group (t3.medium, Spot), IAM roles, OIDC provider for IRSA. Update your kubeconfig with `aws eks update-kubeconfig` | `kubectl get nodes` shows EKS nodes |
| Thu | Add-ons + first deploy | Install the AWS Load Balancer Controller (IRSA role + Helm). Deploy the TaskBoard Helm chart using the ECR image. Verify access through an ALB | App live on AWS |
| Fri | Environments + cleanup | Split into `envs/dev` and `envs/prod` (tfvars), run `terraform fmt/validate`, add `tfsec` or `checkov`. **Destroy everything**, then check for leftover load balancers, EBS volumes, and Elastic IPs. Document costs | **Week 4 assignment complete**, tag `v0.4` |

> Tip: apply in the morning, destroy at the end of each session. Don't leave EKS running overnight.

---

## Week 5: Jenkins CD

**Weekly assignment:** a successful GitLab pipeline triggers Jenkins, which deploys to dev, runs smoke tests, waits for approval, deploys to prod, and rolls back on failure.

| Day | Focus | Tasks | Deliverable |
|-----|-------|-------|-------------|
| Mon | Jenkins server | Add a Jenkins EC2 instance to Terraform (security group, IAM instance profile, user-data install of Java + Jenkins + Docker/kubectl/helm/awscli). Complete the setup wizard and install plugins (Pipeline, Git, Credentials, Kubernetes CLI) | Jenkins reachable and secured |
| Tue | Connect everything | Add credentials (GitLab token, AWS role or keys) in Jenkins. Give the Jenkins role access to EKS (aws-auth / access entries). Create a multibranch or pipeline job from your repo. First Jenkinsfile that just runs `kubectl get nodes` | Jenkins can talk to EKS |
| Wed | Deploy to dev | Write the Jenkinsfile stages: checkout, parameterize `IMAGE_TAG`, `helm upgrade --install` to the `dev` namespace, wait for rollout | Jenkins deploys to dev |
| Thu | Smoke tests + approval + prod | Add smoke test stage (`curl /health`). Add the `input` approval step. Deploy to the `prod` namespace with prod values. Add `post { failure { helm rollback } }` | Full dev, approval, prod flow |
| Fri | Trigger + test failure paths | Add a GitLab webhook or a final GitLab job that calls Jenkins with the image tag. Deliberately break a deploy to confirm rollback. Optional: a Jenkins job for Terraform plan with approval, then apply | **Week 5 assignment complete**, tag `v0.5` |

---

## Week 6: Observability + Security + Wrap-up

**Weekly assignment:** monitoring, logging, and basic security hardening are in place, and the project is documented as a portfolio piece.

| Day | Focus | Tasks | Deliverable |
|-----|-------|-------|-------------|
| Mon | Metrics | Install kube-prometheus-stack via Helm. Expose the backend `/metrics` with a ServiceMonitor. Build a Grafana dashboard (requests, latency, pod CPU/memory) | Dashboard showing live data |
| Tue | Alerts + logging | Add a PrometheusRule (e.g., CrashLoopBackOff, high error rate). Trigger it on purpose. Install Loki + Promtail (or enable CloudWatch Container Insights) and query logs | Alert fires; logs searchable |
| Wed | Cluster security | Add NetworkPolicies (default deny, allow frontend to backend to DB only). Create least-privilege RBAC for the Jenkins deployer. Apply Pod Security Standards labels, set `securityContext` (non-root, read-only filesystem) | Policies enforced and tested |
| Thu | Secrets + pipeline security | Move DB credentials to AWS Secrets Manager with the External Secrets Operator. Add `tfsec`/`checkov` to GitLab CI. Review the Trivy findings and fix what you can | No plaintext secrets in Git |
| Fri | Document + final run | Draw the architecture diagram (draw.io or Mermaid). Finish the README (setup, pipeline, costs, lessons learned). Do a **full end-to-end run**: `terraform apply`, push code, pipeline, Jenkins deploy, prod approval, then `terraform destroy`. Record a short demo (optional) | **Project complete**, tag `v1.0` |

---

## Weekly review checklist (every Friday)

- [ ] Assignment's "done when" criterion verified
- [ ] Everything committed and tagged
- [ ] `NOTES.md` updated (what broke, how you fixed it)
- [ ] AWS resources destroyed (Weeks 4-6) and billing checked

## Buffer and catch-up

If a day takes longer than planned, use the weekend as buffer rather than skipping tasks. The days are cumulative, so a missed step will block the next one.

## Stretch goals (after Week 6)

- Replace Jenkins CD with **ArgoCD** (GitOps) and compare
- Canary/blue-green releases with **Argo Rollouts**
- **Karpenter** or Cluster Autoscaler
- Chaos experiments with Litmus
- Service mesh (Linkerd/Istio)
