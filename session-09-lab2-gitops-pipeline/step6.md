# Deploy using Flux

In this step we will explore adding a new k8s manifest for a Service to the Config Repository and will see Flux synchronize the state of the cluster with the configuration.

## 1. Commit a small change

Now that the operator is set up to react to changes in your repository's `workloads` and `namespaces` directory, we need to provide it with some configuration for it to synchronise.

In this example we are using a simple example of a webservice and will improve its configuration by adding a `Service` which will allow you to reach your application without having to port-forward.

Copy the `nodeapp-svc.yml` file to your k8s config directory and push to the repository

`cd ~/workdir/config && cp ~/assets/nodeapp-svc.yml ./workloads/.`{{execute}}

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

If everything has been configured correctly so far, you should be able see a new `nodeapp` service running.

`kubectl get services -n demo`{{execute}}
```bash
$ kubectl get services -n demo
NAME      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
nodeapp   ClusterIP   10.108.95.142   <none>        8080/TCP   9s
```

Confirm the change by hitting the service directly without port-forward using the IP Address above

`export CIP=$(kubectl get services -n demo -l "app=nodeapp" -o jsonpath="{.items[0].spec.clusterIP}")`{{execute}}

`curl http://${CIP}:8080/listUsers`{{execute}}

Expect to see a JSON response with 4 users.
