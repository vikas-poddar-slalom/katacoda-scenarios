# Deploy using Flux

## 1. Add a yaml file
Now that the operator is set up to react to changes in your repository's `deploy/kubernetes` directory, we need to provide it with some configuration for it to synchronise.

We have a template deployment in the `examples` directory. Copy this example to the `deploy/kubernetes` directory that Flux is watching, then commit and push your changes to your remote repository.

`cp examples/podinfo-dep.yaml deploy/kubernetes`{{execute}}

`git add deploy`{{execute}}

`git commit -m "Add new deployment to cluster"`{{execute}}

`git push origin master`{{execute}}

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

## 2. Manually delete a deployment
Now that we know that the gitops operator is running in your cluster, we should expect it to return the system to a know good state if we make an operator error. Let's try this by manually deleting the `podinfo` deployment.

`kubectl delete deploy/podinfo`{{execute}}

This will delete the deployment from the cluster. We can see this by looking at the deployments, where the `podinfo` deployment should be missing.

`kubectl get deployments`{{execute}}

We haven't changed the state of the repository, however, so the Flux operator still expects the deployment to be running. Since it's missing from our cluster, Flux will attempt to recreate it. We can see this if we monitor the deployments:

`kubectl get deployments --watch`{{execute}}

After a few minutes (be patient, it will get there), a new deployment will be created to replace the missing `podinfo` one.

This is an example of how the Flux operator is acting as a control loop for your cluster, always bringing it back to the desired state if possible.

## 3. Modify the configuration and commit your changes
Now we know that the flux operator is working, let's see it act on changes to our repository.

We'll scale up our podinfo deployment to multiple replicas in our configuration repository, and we will see how Flux automatically applies this new configuration.

First, let's take a look at the podinfo deployment

`kubectl get deploy/podinfo`{{execute}}
```
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
podinfo   1         1         1            1           11m
```

As we can see, the deployment currently only has one pod. Let's change this in the deployment yaml (`deploy/kubernetes/podinfo-dep.yaml`) to 2 replicas

```diff
diff --git a/deploy/kubernetes/podinfo-dep.yaml b/deploy/kubernetes/podinfo-dep.yaml
index e79ce86..8cd6c13 100644
--- a/deploy/kubernetes/podinfo-dep.yaml
+++ b/deploy/kubernetes/podinfo-dep.yaml
@@ -4,7 +4,7 @@ kind: Deployment
 metadata:
   name: podinfo
 spec:
-  replicas: 1
+  replicas: 2
   template:
     metadata:
       labels:
```

Now save and commit the changes.

We can watch Flux automatically update the deployment. Be patient as this can take a few minutes.

`kubectl get deploy/podinfo --watch`{{execute}}

We've now seen Flux react to changes in the repository. Any new kubernetes configuration yaml placed in the `deploy/kubernetes` folder and any future committed changes will now be automatically be applied to our cluster. Goodbye `kubectl apply`!
