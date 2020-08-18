# Deploy using Flux

## 1. Commit a small change

Now that the operator is set up to react to changes in your repository's `workloads` and `namespaces` directory, we need to provide it with some configuration for it to synchronise.

In this example we are using a simple example of a webservice and change its configuration to use a different message.

Replace YOURUSER in https://github.com/YOURUSER/flux-get-started/blob/master/workloads/podinfo-dep.yaml with your GitHub ID), open the URL in your browser, edit the file (using the pencil icon), add `--ui-message='Welcome to Flux'` to the container command and commit the file directly to master.

Add the command to the list of commands as below
```
command:
- ./podinfo
- --port=9898
- --port-metrics=9797
- --grpc-port=9999
- --grpc-service-name=podinfo
- --level=info
- --random-delay=false
- --random-error=false
- --ui-message='Welcome to Flux'
```

By default, Flux git pull frequency is set to 5 minutes. You can tell Flux to sync the changes immediately with:

`fluxctl sync --k8s-fwd-ns flux`{{execute}}

```
$ fluxctl sync --k8s-fwd-ns flux
Synchronizing with ssh://git@github.com/vikas-poddar-slalom/flux-get-started
Revision of master to apply is 7ff201d
Waiting for 7ff201d to be applied ...
Done.
```

If everything has been configured correctly so far, you should be able to watch for changes in your cluster and see the new `podinfo` pods running after a little while.

`watch kubectl get pods -n demo`{{execute}}
```
NAME                      READY   STATUS    RESTARTS   AGE
podinfo-54d69dd76-q2pvv   1/1     Running   0          92s
podinfo-54d69dd76-zrs2b   1/1     Running   0          81s
```

---

Confirm the change

`kubectl -n demo port-forward deployment/podinfo 9898:9898 &`{{execute}}

Press `enter` to get the prompt back

`curl localhost:9898`{{execute}}

Notice the updated `message` value in the JSON reply.

Use `kill %1`{{execute}} to kill the port forwarding

# Congrats

We've now seen Flux react to changes in the repository. Any new kubernetes configuration yaml placed in the `workloads` or `namespaces` folder and any future committed changes will now be automatically be applied to our cluster!
