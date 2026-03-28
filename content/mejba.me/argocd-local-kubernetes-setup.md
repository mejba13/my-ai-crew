**BRAND:** mejba.me
**TITLE:** I Set Up Argo CD on Local Kubernetes — Here's How
**SLUG:** argocd-local-kubernetes-setup
**PRIMARY KEYWORD:** Argo CD local Kubernetes
**SECONDARY KEYWORDS:** GitOps Docker Desktop, Argo CD setup guide, Kubernetes continuous delivery
**META DESCRIPTION:** Step-by-step guide to setting up Argo CD v3.3 on Docker Desktop Kubernetes. Real setup, real screenshots, and a self-healing test from my infrawhisper project.
**TAGS:** Cloud & DevOps, GitOps, Argo CD, Kubernetes, Tutorial
**CONTENT TYPE:** Case Study / Build Log
**CONTENT CLUSTER:** Cloud & DevOps
**TRANSFORMATION GOAL:** After reading, the reader will be able to install Argo CD on local Kubernetes, deploy an app via GitOps, and understand how self-healing and drift detection work in practice.

---

The pod had been running for eleven days. Stable. Healthy. Quietly doing its job inside my local Kubernetes cluster. I deleted it on purpose.

Six seconds later, a brand new pod appeared in its place. Same spec, same config, zero downtime. I didn't touch `kubectl apply`. I didn't trigger a pipeline. I didn't do anything. Argo CD watched my Git repo, saw the desired state said "this pod should exist," and brought it back to life before I could switch browser tabs.

That moment — watching a deleted pod resurrect itself on a local Docker Desktop cluster running on my MacBook — is when GitOps stopped being an abstract concept I'd read about in CNCF blog posts and became something I could feel. Something visceral. Your Git repo becomes the single source of truth, and Kubernetes bends to match it. Not eventually. Not after a pipeline runs. Right now.

I'd been meaning to set up Argo CD for months. Kept pushing it to "next weekend." The assumption was that it'd be complicated — production-grade tooling usually is. I figured I'd need a cloud cluster, a CI/CD pipeline already in place, maybe a dedicated afternoon of wrestling with Helm charts and RBAC configurations.

The actual setup took under ten minutes. And the project I used to test it — infrawhisper, a data and AI platform I've been building with components like an API server, AI engine, stream processor, and a real-time dashboard — gave me a perfect stress test for whether Argo CD could handle a genuinely complex local deployment.

Here's everything I did, everything I learned, and the one Docker Desktop gotcha that almost derailed the whole thing.

## Why I Finally Stopped Running kubectl apply by Hand

I have a confession. For an embarrassingly long time, my deployment workflow for local Kubernetes looked like this:

1. Change code
2. Build Docker image
3. Run `kubectl apply -f deployment.yaml`
4. Realize I forgot to update the image tag
5. Edit the YAML, apply again
6. Wonder why the old pods are still running
7. Delete the deployment, apply fresh
8. Repeat tomorrow with the same frustration

If you've worked with Kubernetes for any length of time, you've felt this pain. It's not that `kubectl apply` is broken — it works fine. The problem is that it puts you, the human, in the critical path of every single deployment. You become the pipeline. And humans are terrible pipelines.

The deeper problem is drift. You apply a manifest. A teammate (or a 2 AM version of yourself) makes a quick manual change with `kubectl edit`. Now your cluster state has silently diverged from what's in your Git repo. Nobody notices until something breaks. And when it does break, good luck figuring out whether the "correct" state lives in Git, in the cluster, or in someone's terminal history.

GitOps solves this by making one rule non-negotiable: Git is truth. The cluster matches Git. Always. If they diverge, the cluster changes — not Git.

According to a CNCF End User Survey from mid-2025, nearly 60% of Kubernetes clusters now rely on Argo CD for application delivery. That number climbs every quarter. And 97% of respondents using Argo CD run it in production — up from 93% in 2023. This isn't an experimental tool anymore. It's the default way serious teams deploy to Kubernetes.

But most of the guides I found assumed you're running EKS or GKE. A production cluster with proper networking, load balancers, and ingress controllers already configured. I wanted to know: does this work on Docker Desktop? On a local cluster where you're experimenting, breaking things, and learning? The answer is yes — with one important workaround I'll show you in step two.

## What Argo CD Actually Does (The Mental Model That Matters)

Before I walk through the installation, I want to give you the mental model that made everything click for me. Because the commands are simple. Understanding *why* they work is what separates someone who installs Argo CD from someone who actually uses it well.

Argo CD is a Kubernetes controller. It runs inside your cluster as a set of pods — a server, a repo server, an application controller, and a few supporting services. Once installed, you point it at a Git repository and tell it: "This directory contains the desired state of my application. Watch it."

From that moment on, Argo CD operates on a continuous reconciliation loop:

1. **Observe** — Poll the Git repo for changes (or receive webhook notifications)
2. **Compare** — Diff the desired state in Git against the live state in the cluster
3. **Act** — If they differ, either alert you (manual sync) or automatically apply the changes (auto sync)

That third step is where the magic lives. With auto-sync enabled, the workflow becomes:

```
Push to Git → Argo CD detects change → Automatically deploys → Self-heals if drift occurs
```

No CI/CD pipeline triggering a deploy step. No human running commands. You push code, and the cluster converges to match. The feedback loop is so tight that it changed how I think about deployments entirely.

Here's what made this concrete for me: I pushed a Helm chart update to my infrawhisper repo at 11:15 PM. By 11:15 PM and about eight seconds, Argo CD had already synced the change and the new pods were rolling out. I didn't even leave my code editor. The commit message showed up in the Argo CD dashboard — `feat(deploy): add Argo CD application manifest for GitOps deployment` — and the sync status read "Sync OK." That level of immediacy makes manual deploys feel like sending deployment instructions by carrier pigeon.

One more thing about the mental model. Argo CD doesn't just deploy. It actively watches for drift. If someone (or something) changes the cluster state outside of Git — a manual `kubectl edit`, a rogue script, an accidental deletion — Argo CD detects the difference and corrects it. The cluster heals itself. I'll prove this works with a live test later in this post.

But first, let's get it running.

## The Complete Setup: Argo CD on Docker Desktop Kubernetes

### Prerequisites — What You Need Before Starting

You need exactly two things:

- **Docker Desktop** with Kubernetes enabled (Settings → Kubernetes → Enable Kubernetes → Apply & Restart). I'm running Docker Desktop on macOS, but the steps are identical on Windows and Linux.
- **kubectl** configured and pointing at your Docker Desktop cluster. Verify with:

```bash
kubectl config current-context
# Should output: docker-desktop
```

If you see a different context, switch with `kubectl config use-context docker-desktop`. You don't want to accidentally install Argo CD on a production cluster. Ask me how I know that's worth checking.

That's it. No Helm required for the basic install. No cloud provider account. No load balancer. Docker Desktop's built-in Kubernetes is enough.

### Step 1 — Install Argo CD Into Your Cluster

Two commands. The first creates a dedicated namespace. The second pulls the official Argo CD manifests and installs everything.

```bash
kubectl create namespace argocd

kubectl apply -n argocd -f \
  https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

This installs Argo CD v3.3.4 (the stable release as of March 2026), which includes PreDelete hooks, OIDC background token refresh, enhanced RBAC controls, and KEDA integration. The manifest deploys several components into the `argocd` namespace:

- **argocd-server** — The API server and web UI
- **argocd-repo-server** — Clones and processes your Git repos
- **argocd-application-controller** — The reconciliation engine that watches for drift
- **argocd-redis** — Caching layer
- **argocd-dex-server** — SSO authentication (optional for local setups)

Wait about 60-90 seconds for all pods to reach the `Running` state. You can watch them come up:

```bash
kubectl get pods -n argocd -w
```

When you see five or six pods all showing `1/1 Running`, you're ready for the next step. And the next step is the one that tripped me up for a solid twenty minutes.

### Step 2 — Remove Network Policies (The Docker Desktop Gotcha)

This is the step that no "quick start" guide mentioned. I installed Argo CD, tried to access the UI, and got nothing. Connection refused. Timeout. The pods were running, the service existed, but port-forwarding just... didn't work.

After twenty minutes of checking service selectors and pod logs, I found the issue: Argo CD ships with NetworkPolicy resources that restrict traffic between its components. On a real cluster with a CNI plugin that supports NetworkPolicies (Calico, Cilium, etc.), these work perfectly. On Docker Desktop's built-in Kubernetes networking? They silently break everything.

Docker Desktop's default CNI doesn't enforce NetworkPolicies, but the policies still get created and can interfere with service discovery in subtle ways. The fix is simple:

```bash
kubectl delete networkpolicies --all -n argocd
```

That's it. One command. Twenty minutes of my life I won't get back. I'm writing this down so you don't lose the same twenty minutes.

<!-- IMAGE: Terminal output showing the kubectl delete networkpolicies command executing successfully in the argocd namespace. Alt text: "Argo CD local Kubernetes network policy deletion in terminal". Caption: "Removing NetworkPolicies — the fix for Docker Desktop's connectivity issues." -->

After running this, port-forwarding works immediately. If you're running a real cluster with Calico or Cilium, skip this step — you want those policies.

### Step 3 — Access the Argo CD Dashboard

Now expose the Argo CD server to your local machine:

```bash
kubectl port-forward svc/argocd-server -n argocd 8443:443
```

Open your browser and navigate to `https://localhost:8443`. You'll get a certificate warning — that's expected for a self-signed cert on localhost. Click through it.

You should see the Argo CD login screen. Clean. Minimal. That dark-themed UI that signals "this is a serious tool."

### Step 4 — Get Your Login Credentials

The default username is `admin`. The initial password is auto-generated and stored as a Kubernetes secret. Extract it with:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```

Copy that output — it's your temporary password. Log in with `admin` and the decoded password.

**Pro tip:** Change this password immediately after first login, especially if anyone else has access to your network. In a local dev environment it's less critical, but building good habits matters. You can change it via the Argo CD CLI or through the UI under User Info → Update Password.

At this point, you have a fully functional Argo CD installation running on your local Kubernetes cluster. The dashboard is empty — no applications yet. That changes now.

### Step 5 — Create Your Application Manifest

This is where Argo CD connects to your Git repository and starts managing your deployments. You need an Application manifest — a Kubernetes custom resource that tells Argo CD what to watch and where to deploy.

Here's the manifest I used for my infrawhisper project:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/your-repo.git
    targetRevision: main
    path: deploy/helm/my-app
    helm:
      valueFiles:
        - values.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

Let me break down what each section does, because understanding these fields saves you hours of debugging later:

**`source`** — Where Argo CD should look for your manifests:
- `repoURL`: Your Git repository (public repos work out of the box; private repos need credentials configured in Argo CD settings)
- `targetRevision`: Which branch to track. I use `main`, but you can point this at any branch, tag, or commit SHA
- `path`: The directory within the repo that contains your Kubernetes manifests or Helm chart
- `helm.valueFiles`: If you're using Helm (I was), this specifies which values file to apply

**`destination`** — Where Argo CD should deploy:
- `server`: The Kubernetes API server URL. `https://kubernetes.default.svc` means "deploy to the same cluster Argo CD is running in." For local setups, this is always what you want.
- `namespace`: The target namespace for your application's resources

**`syncPolicy`** — How Argo CD should behave:
- `automated`: Enables auto-sync. Without this, you'd need to manually click "Sync" in the UI every time
- `prune: true`: If you remove a resource from Git, Argo CD removes it from the cluster. Without this, deleted manifests leave orphaned resources
- `selfHeal: true`: This is the big one. If someone or something changes the cluster state outside of Git, Argo CD reverts it. Your cluster always matches Git.
- `CreateNamespace=true`: Argo CD will create the target namespace if it doesn't exist

Save this as `application.yaml` in your project directory. Adjust the `repoURL`, `path`, and `namespace` to match your actual setup.

### Step 6 — Deploy and Watch the Magic

Apply the manifest:

```bash
kubectl apply -f application.yaml
```

Now switch to your Argo CD dashboard at `https://localhost:8443`. Within seconds, you should see your application appear. Argo CD immediately starts syncing — cloning your repo, rendering the Helm chart (or plain manifests), and applying the resources to your cluster.

For my infrawhisper project, the dashboard lit up with the entire application topology: api-server running 3 pods, dashboard with 2 pods, ai-engine with 2 pods, stream-processor with 2 pods, collector with 2 pods, plus the infrawhisper-agent and ingress controller. All visible in one network view. The commit hash `776a48f` from my latest push was right there in the UI, with the commit message I'd written minutes earlier.

<!-- IMAGE: Argo CD dashboard showing the infrawhisper application with network topology view, displaying api-server, dashboard, ai-engine, stream-processor, collector pods and their health status. Alt text: "Argo CD local Kubernetes dashboard showing infrawhisper application topology with pod health status". Caption: "The infrawhisper project fully deployed and managed by Argo CD — every component visible in one view." -->

The Docker Desktop container list told the same story from a different angle — 38 running containers, with the argocd components sitting alongside my application pods: ai-engine, api-server, clickhouse, collector, dashboard, kafka, postgres, redis, stream-processor. A full data platform running locally, managed entirely through Git.

<!-- IMAGE: Docker Desktop showing 38 running containers including both Argo CD components and infrawhisper application pods like ai-engine, api-server, clickhouse, kafka, postgres, redis, and stream-processor. Alt text: "Docker Desktop Kubernetes containers running Argo CD and infrawhisper application pods". Caption: "38 containers managed through a single Git push — Docker Desktop handling the full stack." -->

That's the setup. Six steps, under ten minutes. But the setup is just the foundation. The real question is: does it actually work the way the documentation promises?

I ran three tests to find out.

## The Self-Healing Test: Deleting a Pod on Purpose

Here's the test I ran to prove self-healing works on a local cluster. I had my infrawhisper dashboard component running with 2 replica pods. One specific pod — `dashboard-75fd8b8ddb-55x9h` — had been running healthy for 11 days straight. Stable, serving traffic, doing its job.

I killed it.

```bash
kubectl delete pod dashboard-75fd8b8ddb-55x9h -n my-app
```

Then I watched. Not Argo CD's dashboard — I watched the terminal. Within six seconds:

- **BEFORE:** `dashboard-75fd8b8ddb-55x9h` — Running, Age: 11 days
- **DELETED:** Pod terminated
- **AFTER:** `dashboard-75fd8b8ddb-lt5bn` — Running, Age: 6 seconds

A brand new pod, different name, same spec. Kubernetes' ReplicaSet controller handled the immediate replacement (that's standard Kubernetes behavior). But here's the part that matters: Argo CD's reconciliation loop confirmed the cluster state still matched the desired state in Git. If the pod deletion had caused the overall deployment to drift from Git's definition — say, if someone had scaled the replicas down manually — Argo CD would have caught that too and corrected it.

This isn't hypothetical resilience. It's measurable. I watched it happen on my laptop, on a local cluster, with tools you can install in ten minutes.

The deeper implication took a day to sink in. With self-healing enabled, your deployment manifests in Git aren't just configuration files. They're a contract. A promise about what the cluster should look like at all times. Break the contract manually, and the system enforces it automatically. That shift in thinking — from "I deploy things" to "I declare things and the system maintains them" — is the core of GitOps. And it feels completely different once you experience it firsthand.

## Drift Detection: The Silent Guardian

The self-healing test covers accidental deletions. But what about intentional manual changes? The kind that happen when someone runs `kubectl edit deployment` to "quickly fix" something in production at 3 AM?

I tested this by manually scaling my api-server deployment from 3 replicas to 1:

```bash
kubectl scale deployment api-server --replicas=1 -n my-app
```

For about fifteen seconds, the cluster had 1 api-server pod instead of the 3 defined in my Git repo. Then Argo CD's reconciliation loop detected the drift, compared the live state against Git, and scaled it back to 3.

No alarm. No manual intervention. The Argo CD dashboard briefly showed the app health as "Degraded" (which is accurate — running fewer replicas than desired is degraded), then flipped back to "Healthy" once the reconciliation completed.

This is the behavior that makes GitOps transformative for teams. It doesn't just prevent problems — it actively corrects them. And it creates an audit trail. Every sync operation shows up in Argo CD's history. You can see exactly when drift occurred and when it was corrected. Try getting that from `kubectl apply`.

## Auto-Sync: Push to Git, Watch It Deploy

The third test was the simplest and the most satisfying. I changed a value in my Helm chart's `values.yaml` — bumped the dashboard replica count from 2 to 3 — committed, and pushed to `main`.

I had the Argo CD dashboard open in a browser and my terminal running `kubectl get pods -n my-app -w` side by side.

Within about fifteen seconds of the push, Argo CD detected the new commit, pulled the updated chart, rendered the manifests, and applied the change. A third dashboard pod started spinning up while I watched. The sync status in the dashboard updated to show the new commit hash. The commit message I'd just written was right there in the UI.

No pipeline. No webhook configuration. No deploy script. I pushed code and the cluster changed. That's the promise of GitOps, and on a local Docker Desktop cluster running Argo CD v3.3.4, it delivered exactly as advertised.

## What Argo CD Gets Right — And Where It Gets Honest

After running this setup for several days on my infrawhisper project, here's my honest assessment.

**What's genuinely impressive:**

- **The dashboard is worth the install alone.** Seeing your entire application topology — every deployment, service, pod, and ingress — in one visual map is something `kubectl` can never give you. For my infrawhisper project with its dozen-plus components, this went from "nice to have" to "how did I work without this" in about three hours.

- **The sync speed is real.** I expected minutes. I got seconds. The lag between pushing to Git and seeing the change live in the cluster was consistently under 20 seconds on my local setup.

- **Rollback is one click.** Argo CD keeps a history of every sync. If a deployment goes wrong, you click "Rollback" and select a previous commit. The cluster reverts. Try doing that with `kubectl apply` and a prayer.

- **Helm chart rendering is handled for you.** I didn't need to run `helm template` or `helm install` locally. Argo CD's repo-server renders the chart and applies the output. This means your Git repo only needs the chart source — Argo CD handles the rest.

**What I wish someone had told me:**

- **The resource overhead isn't trivial.** On Docker Desktop, the Argo CD components added roughly 500MB-700MB of RAM usage and several CPU cores worth of background activity. On a modern machine, that's fine. On an older laptop, you'll feel it. My Docker Desktop was already running 38 containers for the infrawhisper stack — adding Argo CD's pods on top pushed total memory usage past 8GB.

- **Private repos need extra configuration.** If your Git repository is private (most are), you need to configure repository credentials in Argo CD's settings. This involves either an SSH key, a personal access token, or a GitHub App. The official docs cover this well, but it's an extra step that the "two-command install" guides conveniently skip.

- **The initial admin password workflow is clunky.** Base64-decoding a Kubernetes secret to get your first password works, but it's not a great developer experience. I'd love to see an interactive setup wizard for local installations.

- **NetworkPolicies on Docker Desktop are a real time-waster.** I've already covered this in the setup steps, but it bears repeating: if you're running on Docker Desktop and things aren't connecting, delete the NetworkPolicies first. This single issue probably causes more abandoned Argo CD local setups than any actual bug.

If you'd rather have someone build this kind of infrastructure setup from scratch — Kubernetes clusters, GitOps pipelines, the whole platform engineering stack — I take on DevOps and cloud engineering projects. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## Where This Fits in a Real Workflow

Running Argo CD locally isn't just a learning exercise. It's the first stage of a workflow that scales directly to production. Here's the progression I'm planning for my infrawhisper project:

**Stage 1 (where I am now):** Local Docker Desktop cluster with Argo CD managing Helm deployments. Perfect for development, testing sync behaviors, and validating manifests before they touch a real cluster.

**Stage 2:** Move the same manifests — unchanged — to a staging cluster on AWS EKS or a self-managed cluster. Argo CD's Application manifest stays identical. Only the `destination.server` changes.

**Stage 3:** Production deployment with Argo CD managing multiple environments through ApplicationSets. Same Git repo, different value files per environment, Argo CD handling the rollout to each.

The key insight: the Application manifest I wrote in step 5 of this guide doesn't change between stages. The Helm chart doesn't change. The sync policy doesn't change. I'm building the production workflow now, on my laptop, with the same tools I'll use when infrawhisper goes live. That's not a side benefit of local Argo CD — it's the primary benefit.

Platform engineers now represent 37% of Argo CD users according to the CNCF survey, and this is exactly why. The patterns you establish locally carry straight through to production. There's no "rewrite the deployment pipeline" step when you're ready to ship.

## The Five Things Argo CD Gives You That kubectl apply Never Will

After living with this setup, I can articulate exactly what changed. Not in abstract terms — in concrete, daily-workflow terms:

**1. Auto Sync eliminates the deploy step entirely.** Push to Git. Walk away. The cluster updates. This sounds minor until you realize how many times per day you run `kubectl apply`. For me, it was 15-20 times during active development. Each one is a context switch, a terminal window, a chance to apply the wrong manifest to the wrong namespace. All gone.

**2. Self-healing catches mistakes you'd never notice.** Accidental pod deletions. Rogue scripts. Misconfigured autoscalers. Argo CD detects drift and corrects it. On my infrawhisper stack with 10+ microservices, this isn't a luxury — it's the difference between a stable local environment and one that slowly rots.

**3. Drift detection creates accountability.** Every manual change gets reverted and logged. This is security and operational hygiene rolled into one. You can see who (or what) changed the cluster, when, and how Argo CD corrected it.

**4. The visual dashboard replaces a dozen kubectl commands.** Pod status, replica counts, health checks, sync history, application topology — all in one browser tab. I used to run `kubectl get pods`, `kubectl get svc`, `kubectl describe deployment`, and `kubectl logs` in rapid succession just to understand my cluster's state. Now I glance at the Argo CD dashboard.

**5. Rollback becomes a single click.** Not "find the last working commit, check out the manifest, apply it, verify it worked." One click. Select the commit. Done. For a project like infrawhisper with interdependent services, this alone justifies the setup.

## What I'd Do Differently Next Time

If I were setting this up again from scratch, three things would change:

**I'd install the Argo CD CLI from the start.** The web UI is great for visualization, but the CLI (`argocd`) is faster for managing applications, syncing, and checking status from the terminal. I didn't install it initially because I wanted to keep the setup minimal, and I regretted it within a day. Install it with:

```bash
brew install argocd
```

**I'd use ApplicationSets from day one.** My infrawhisper project has a single Application manifest, but as I add more environments (staging, production), I'll need one Application per environment. ApplicationSets let you template Application resources — one definition that generates multiple Applications based on parameters. Starting with this pattern early saves a migration later.

**I'd configure repository credentials immediately.** I started with a public repo fork for testing, then moved to my actual private repo. The credential configuration step interrupted my flow. Set it up first, even if your repo is currently public. Future-you will appreciate it.

## Getting Started: The Ten-Minute Checklist

Here's the condensed version. If you've read everything above and just want the steps on one screen:

```bash
# 1. Verify your Kubernetes context
kubectl config current-context  # Should be: docker-desktop

# 2. Install Argo CD
kubectl create namespace argocd
kubectl apply -n argocd -f \
  https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# 3. Wait for pods (about 60-90 seconds)
kubectl get pods -n argocd -w

# 4. Remove NetworkPolicies (Docker Desktop only!)
kubectl delete networkpolicies --all -n argocd

# 5. Port-forward the UI
kubectl port-forward svc/argocd-server -n argocd 8443:443

# 6. Get admin password
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d

# 7. Login at https://localhost:8443 (admin + decoded password)

# 8. Apply your Application manifest
kubectl apply -f application.yaml
```

Eight commands. Under ten minutes. A fully functional GitOps pipeline running on your laptop.

Argo CD supports Helm charts, Kustomize, Jsonnet, and plain YAML manifests — so whatever your current deployment format, it plugs in without rewriting anything. For my infrawhisper project, I used Helm because the chart was already structured for parameterized deployments. But if you have a directory of raw YAML manifests, just point the Application's `source.path` at that directory and remove the `helm` section. Argo CD figures out the rest.

## Frequently Asked Questions

### Does Argo CD work with Docker Desktop Kubernetes?

Yes. Argo CD runs on Docker Desktop's built-in Kubernetes with one required workaround: delete the default NetworkPolicies in the argocd namespace, since Docker Desktop's CNI doesn't handle them correctly. After that, all features — auto-sync, self-healing, drift detection, and the web dashboard — work identically to cloud-hosted clusters.

### How much RAM does Argo CD use on a local cluster?

Expect 500MB-700MB of additional RAM for the Argo CD control plane components (server, repo-server, application-controller, redis, dex). On a machine with 16GB+ RAM, this is negligible. On 8GB machines running other heavy workloads, you may need to tune resource limits in the Argo CD manifests.

### Can Argo CD deploy from a private Git repository?

Yes, but you need to configure repository credentials in Argo CD's settings first. Options include SSH keys, personal access tokens, or GitHub Apps. Configure this through the Argo CD UI (Settings → Repositories) or via the `argocd` CLI before creating your Application manifest.

### What happens if I manually change something in the cluster?

With `selfHeal: true` in your Application's sync policy, Argo CD detects the drift and automatically reverts the cluster to match Git. The dashboard briefly shows the application as "OutOfSync" or "Degraded," then corrects it within the next reconciliation cycle (typically under 30 seconds). The drift event is logged in the sync history for auditing.

### Is local Argo CD setup the same as production?

Nearly identical. The Application manifest, sync policies, and Helm chart configuration transfer directly to a production cluster. The only changes are the `destination.server` URL (pointing to your production cluster instead of `kubernetes.default.svc`) and environment-specific values files. This is one of the strongest arguments for starting locally — you build production patterns from day one.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
