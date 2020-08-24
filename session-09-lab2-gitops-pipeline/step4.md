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

Install Flux in the cluster

```
fluxctl install \
--git-user=${GLUSER} \
--git-email=${GLUSER}@users.noreply.gitlab.com \
--git-url=git@gitlab.com:${GLUSER}/my-nodejs-app-config \
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
$ kubectl -n flux rollout status deployment/flux
Waiting for deployment "flux" rollout to finish: 0 of 1 updated replicas are available...
deployment "flux" successfully rolled out
```
