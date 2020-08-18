# Install the Flux Operator

Now that you have a repository set up, it's time to install the deployment operators on your cluster. For this we'll use Weavework's [Flux](https://github.com/weaveworks/flux).

The needed deployment manifests are kept in this repository's `flux` directory.

---

If you have `kubectl` configured correctly, you'll be able to install the Flux operator by applying the manifests to your cluster:

`cd gitops-tutorial`{{execute}}
`kubectl apply -f ./flux/`{{execute}}

You should see that this will create several objects in your cluster

```
serviceaccount/flux created
clusterrole.rbac.authorization.k8s.io/flux created
clusterrolebinding.rbac.authorization.k8s.io/flux created
deployment.apps/flux created
secret/flux-git-deploy created
deployment.apps/memcached created
service/memcached created
```

---

Alongside flux, we have also installed a memcache service for flux to use, and appropriate permissions for the flux operator to act on the cluster itself through a role and rolebinding.

You should now be able to see the flux pod running in the cluster:

`kubectl get pods --watch`{{execute}}
```
NAME                        READY     STATUS    RESTARTS   AGE
flux-9d69f6fc4-5t5w6        1/1       Running   0          18m
memcached-dbb59cb58-phk9s   1/1       Running   0          18m
```

Use `ctrl+c` to end the watch
