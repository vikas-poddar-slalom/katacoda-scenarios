# Install the Flux Operator

Following the documentation at `https://docs.fluxcd.io/en/latest/references/fluxctl/`, install Flux using `snap`

`snap install fluxctl --classic`{{execute}}

```
2020-08-18T19:12:53Z INFO Waiting for restart...
fluxctl 1.20.1 from Flux CD developers (weaveflux) installed
```

---

Create a `flux` namespace

`kubectl create ns flux`{{execute}}

---

Install Flux in the cluster; replace `YOURUSER` with your GitHub username in the format `first-last-slalom`

`export GHUSER="YOURUSER"`{{copy}}

```
fluxctl install \
--git-user=${GHUSER} \
--git-email=${GHUSER}@users.noreply.github.com \
--git-url=git@github.com:${GHUSER}/flux-get-started \
--git-path=namespaces,workloads \
--namespace=flux | kubectl apply -f -
```

Expect to see

```
serviceaccount/flux created
clusterrole.rbac.authorization.k8s.io/flux created
clusterrolebinding.rbac.authorization.k8s.io/flux created
deployment.apps/flux created
secret/flux-git-deploy created
deployment.apps/memcached created
service/memcached created
```

The `--git-path=namespaces,workloads` tells Flux to only watch these filepaths in the repository

---

Wait for Flux to start

`kubectl -n flux rollout status deployment/flux`{{execute}}

Expect to see a successful roll out

```
$ kubectl -n flux rollout status deployment/flux --watch
deployment "flux" successfully rolled out
```
