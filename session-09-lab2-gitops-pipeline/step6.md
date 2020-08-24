# Deploy using Flux

## 1. Commit a small change

Now that the operator is set up to react to changes in your repository's `workloads` and `namespaces` directory, we need to provide it with some configuration for it to synchronise.

In this example we are using a simple example of a webservice and will improve its configuration by adding a `Service` which will allow you to reach your application without having to port-forward.

Copy the `nodeapp-svc.yml` file to your k8s config directory and push to the repository

`cd ~/workdir/config && cp path/nodeapp-svc.yml ./workloads/.`{{execute}}

`git add .`{{execute}}

`git commit -m "Adding Service"`{{execute}}

`git push`{{execute}}

---

By default, Flux git pull frequency is set to 5 minutes. You can tell Flux to sync the changes immediately with:

`fluxctl sync --k8s-fwd-ns flux`{{execute}}

```
$ fluxctl sync --k8s-fwd-ns flux
Synchronizing with ssh://git@github.com/vikas-poddar-slalom/flux-get-started
Revision of master to apply is 7ff201d
Waiting for 7ff201d to be applied ...
Done.
```

## 2. Verify that Flux updated the deployment

If everything has been configured correctly so far, you should be able to watch for changes in your cluster and see the new `nodeapp` service running after a little while.

`kubectl get services -n demo`{{execute}}
```
TBD
```

Confirm the change by hitting the service directly without port-forward

`curl http://nodeapp:8080/listUsers`{{execute}}