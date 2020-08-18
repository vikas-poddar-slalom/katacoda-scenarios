# Deploy using Flux

## 1. Commit a small change
Now that the operator is set up to react to changes in your repository's `workloads` and `namespaces` directory, we need to provide it with some configuration for it to synchronise.

In this example we are using a simple example of a webservice and change its configuration to use a different message.

Replace YOURUSER in https://github.com/YOURUSER/flux-get-started/blob/master/workloads/podinfo-dep.yaml with your GitHub ID), open the URL in your browser, edit the file, add --ui-message='Welcome to Flux' to the container command and commit the file.

By default, Flux git pull frequency is set to 5 minutes. You can tell Flux to sync the changes immediately with:

`fluxctl sync --k8s-fwd-ns flux`{{execute}}

If everything has been configured correctly so far, you should be able to watch for changes in your cluster and see the `podinfo` deployment running after a little while.

`watch kubectl get pods`{{execute}}
```
NAME                        READY     STATUS    RESTARTS   AGE
flux-6f9798f7f-6hcc9        1/1       Running   0          12m
memcached-dbb59cb58-gn8pf   1/1       Running   0          16m
```

Then after a few minutes

```
NAME                        READY     STATUS    RESTARTS   AGE
flux-6f9798f7f-6hcc9        1/1       Running   0          12m
memcached-dbb59cb58-gn8pf   1/1       Running   0          16m
podinfo-6466ffddb-l9nq2     1/1       Running   0          3m
```

---

Confirm the change

`kubectl -n demo port-forward deployment/podinfo 9898:9898 & curl localhost:9898`{{execute}}

Notice the updated `message` value in the JSON reply.

---

We've now seen Flux react to changes in the repository. Any new kubernetes configuration yaml placed in the `deploy/kubernetes` folder and any future committed changes will now be automatically be applied to our cluster. Goodbye `kubectl apply`!
