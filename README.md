# k8s-apps

GitOps repo for the RKE2 cluster on Proxmox. ArgoCD (bootstrapped by
[tf-proxmox](https://github.com/poohnet-org/tf-proxmox)) watches this repo:
a root "app-of-apps" `Application` syncs the [`apps/`](apps/) directory, where
each manifest is itself an ArgoCD `Application` pointing at a workload
directory. Deploying, updating, or removing an app is a commit here — the
infra repo is never involved.

## Repository layout

```
apps/                   # one Application manifest per workload (synced by the root app)
workloads/<name>/       # the workload's Kubernetes manifests
```

## Adding an app

1. Put the manifests (plain YAML, Kustomize, or a Helm chart) under `workloads/<name>/`.
2. Add `apps/<name>.yaml` with an `Application` pointing at that path
   (copy `apps/guestbook.yaml` as a starting point). Keep the
   `resources-finalizer.argocd.argoproj.io` finalizer so removing the app
   from this repo also cleans up its workloads.
3. Commit to `main` — the root app picks it up on the next sync.

## Apps

| App | Path | URL |
|---|---|---|
| guestbook | [`workloads/guestbook`](workloads/guestbook/) | http://guestbook.apps.k8s.poohnet.de |

Hostnames use the `*.apps.k8s.poohnet.de` wildcard, which must resolve to
Traefik's LoadBalancer IP (`192.168.155.220`).
