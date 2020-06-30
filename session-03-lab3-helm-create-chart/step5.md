# Rollback the Helm Release

As we did in Lab 2, in this step we will perform a rollback on our Spring application

1. First lets see the list of available versions for release name **myspringapp**

  `helm history myspringapp`{{execute}}

1. Rollback to the previous release

  `helm rollback myspringapp 2`{{execute}}

1. Verify your chart was downgraded to version **0.2.0**

  `helm list`{{execute}}

1. Version 0.2.0 of myspringapp did not have some of the Deployment improvements. We can verify this

  `export DEPLOYMENT_NAME=$(kubectl get deployments --namespace default -l "app.kubernetes.io/instance=myspringapp" -o jsonpath="{.items[0].metadata.name}")`{{execute}}

  `kubectl describe deployment $DEPLOYMENT_NAME`{{execute}}

  See that our resources, livenessProbe, and readinessProbe are removed and the Container definition is reset to the state from Step 3 where we created release version **0.2.0**
  ```yaml
  Containers:
   myspringapp:
    Image:        dockerworkshopdallas/java:release
    Port:         8181/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  ```

## Congrats!

Congrats, you have now successfully built your own chart from scratch, updated it, improved it, and performed a rollback on it.
