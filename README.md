# flux-demo

## Getting Started

### Github

* Fork this repo
* Create your personal access token with the following permissions
  * Repository permission: Administration r/w
  * Repository permission: Contents: r/w
  * Repository permission: Metadata: r/o
* Set the token in FLUX_GITHUB_TOKEN

```bash
export FLUX_GITHUB_TOKEN=github_pat...
```

### Init

* Install Flux (e.g. brew)
* Create a Flux env after replacing the owner, reposigoty, path

```bash
$ echo $FLUX_GITHUB_TOKEN | flux bootstrap github \
  --token-auth \
  --owner=sokoide \
  --repository=flux-demo \
  --branch=main \
  --path=./demo1 \
  --personal
```

* It will initialize Flux, pull from the Github repo

### Checkout

```bash
$ k get ns
NAME                 STATUS   AGE
bar                  Active   15s
default              Active   3m25s
flux-system          Active   63s
foo                  Active   14s
kube-node-lease      Active   3m25s
kube-public          Active   3m25s
kube-system          Active   3m25s
local-path-storage   Active   3m20s
o11y                 Active   14s

$ k get kustomization -A
NAMESPACE     NAME          AGE   READY     STATUS
flux-system   bar           19s   Unknown   Reconciliation in progress
flux-system   flux-system   66s   True      Applied revision: main@sha1:e23b668c65b1a08c84b351a013714b95b261030d
flux-system   foo           19s   Unknown   Reconciliation in progress
flux-system   o11y          19s   Unknown   Reconciliation in progress
```

## Workflow

* Clone your forked repo

```bash
git clone git@github.com:sokoide/flux-demo.git

cd flux-demo
```

* Add/change namespace and the contents (e.g. foo, bar, o11y)
* Push it to github
* Wait for a while, or pull the change by `flux reconcile source git flux-system`

```bash
$ flux reconcile source git flux-system
► annotating GitRepository flux-system in flux-system namespace
✔ GitRepository annotated
◎ waiting for GitRepository reconciliation
✔ fetched revision main@sha1:e23b668c65b1a08c84b351a013714b95b261030d
```

* Wait for a while, or reconcile your ns

```bash
$ flux reconcile kustomization foo -n flux-system
► annotating Kustomization foo in flux-system namespace
✔ Kustomization annotated
◎ waiting for Kustomization reconciliation
✔ applied revision main@sha1:e23b668c65b1a08c84b351a013714b95b261030d
```

## Useful commands

```bash
flux get kustomizations --watch

flux events --for Kustomization/o11y --watch

flux events -A  --watch
```
## How to access Grafana in o11y namespace

```bash
# use this as a password for user `admin`
$ k get secret o11y-grafana -n o11y -o jsonpath="{.data.admin-password}" | base64 -d
SBpNbTOzMPyd1Xd2bjs0IpTeky4cAO5XH4osZ5SL

$ k -n o11y port-forward svc/o11y-grafana 3000:80

# browse 127.0.0.1:3000
# `localhost:3000` may be blocked by your Safari/Edge's security policy. Use 127.0.0.1
# goto "dashboards"
```
