# Install the Flux Operator

Using the documentation at `https://docs.fluxcd.io/en/latest/references/fluxctl/`, install Flux using `snap`

`snap install fluxctl --classic`{{execute}}

```
2020-08-18T19:12:53Z INFO Waiting for restart...
fluxctl 1.20.1 from Flux CD developers (weaveflux) installed
```

---

Create a `flux` namespace

`kubectl create ns flux`{{execute}}

Install Flux in the cluster; replace `YOURUSER` with your username
```
export GHUSER="YOURUSER"
fluxctl install \
--git-user=${GHUSER} \
--git-email=${GHUSER}@users.noreply.github.com \
--git-url=git@github.com:${GHUSER}/flux-get-started \
--git-path=namespaces,workloads \
--namespace=flux | kubectl apply -f -
```

```
export GHUSER="vikas.poddar@slalom.com"
fluxctl install \
--git-user=${GHUSER} \
--git-email=${GHUSER}@users.noreply.github.com \
--git-url=git@github.com:${GHUSER}/flux-get-started \
--git-path=namespaces,workloads \
--namespace=flux | kubectl apply -f -
```

---

Wait for Flux to start

`kubectl -n flux rollout status deployment/flux --watch`{{execute}}

Use `ctrl+c` to end the watch
