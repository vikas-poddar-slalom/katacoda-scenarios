# Harden the Release

In this step we will harden our release by introducing a few rules around our deployment. We will do this by modifying the `values.yaml` to support our changes and adding a new set of configurations to the `deployment.yaml`

1. Copy the **myspringapp3** files from **~/** into this empty directory

  `cp ~/charts/Chart3.yaml myspringapp/Chart.yaml`{{execute}}

  `cp ~/charts/values3.yaml myspringapp/values.yaml`{{execute}}

  `cp ~/charts/deployment3.yaml myspringapp/templates/deployment.yaml`{{execute}}

  We can see the structure of the chart either via the editor, or by running `helm tree myspringapp`{{execute}}

## Let't take a look around and see what has changed

**You may need to close the file and re-open it to see the changes**

### Chart.yaml

  myspringapp/Chart.yaml{{open}}

  As you can see, the version has again been changed to 0.3.0

### values.yaml

  myspringapp/values.yaml{{open}}

  A few more values have been added to improve the Deployment:
  - The **resources** section defines resource requests that the deployment makes when pods are being started on the cluster. The cluster will try to schedule pods on nodes where it can fulfill the CPU and Memory requirements defined here. For example, this deployment is specifying that the pod can start with .1 cores and 128Mb of RAM, but can consume up to .2 cores and 256Mb of RAM, so the control plane in the cluster will only spin up pods of this deployment on nodes where it can fulfill those resource requirements.
  - Liveness and Readiness probes are used to define the requirements by which a pod can say it is live and then ready to receive traffic. When a pod is live, it will indicate this status to the cluster, but until it is ready, it will not receive any traffic.
    - Kubernetes uses the liveness probe to determine when to restart a container, and the readiness probe to determine when the pod is ready to accept traffic.
    - We have setup some rules for polling on both liveness and readiness:
      1. Give the service a few seconds to start up
      1. Set a time interval between polls
      1. For liveness, the number of polls that need to fail to consider the pod startup as failed; this will signal the cluster to remove the pod and replace it with another pod
      1. For readiness, the number of successful responses needed to consider the pod ready to receive traffic

### deployment.yaml

  myspringapp/templates/deployment.yaml{{open}}

  As you look at this file, take a look at these changes:
  - In the `containers` section, `resources`, `livenessProbe`, `readinessProbe` have been added
  - The `resources` section combines a couple of Helm functions with a pipe `|`; these will essentially output the `.Values.resources` block with the proper left-indentation to create a proper yaml file when the template is rendered.

  With these changes, we have improved the overall reliability of our deployment; now, a Pod will only be scheduled to start on nodes that can support its resource requirements and the cluster will validate readiness and liveness of the underlying service before directing traffic to the Pod. If a Pod fails to meet the probe criteria, it will automatically be removed and replaced with another pod.

## Package and Install

Prior to packaging, because we made multiple changes to the deployment.yaml, it would be nice to validate that our changes are correct and have the desired effect.

Helm Lint will check the formatting of the yaml files and validate that they are correct.

`helm lint myspringapp`{{execute}}

Expect to see
```
==> Linting myspringapp
[INFO] Chart.yaml: icon is recommended

1 chart(s) linted, 0 chart(s) failed
```

---

Now that we know the formatting is good, we can package the chart using **helm package**

`helm package myspringapp`{{execute}}

Check that your chart was packaged correctly

`ls -l`{{execute}}

You should see a file called **myspringapp-0.3.0.tgz**

---

Prior to upgrading, because we made multiple changes to deployment.yaml, it would be nice to validate that our changes are correct and have the desired effect.

Helm Upgrade with `--dry-run --debug` will do a dry upgrade that will output what the state will be if the upgrade is performed

`helm upgrade myspringapp myspringapp-0.3.0.tgz --dry-run --debug`{{execute}}

Expect to see something like
```shell
upgrade.go:120: [debug] preparing upgrade for myspringapp
upgrade.go:128: [debug] performing update for myspringapp
upgrade.go:283: [debug] dry run for myspringapp
Release "myspringapp" has been upgraded. Happy Helming!
NAME: myspringapp
LAST DEPLOYED: Fri Jun 26 23:33:15 2020
NAMESPACE: default
STATUS: pending-upgrade
REVISION: 3
TEST SUITE: None
USER-SUPPLIED VALUES:
{}

COMPUTED VALUES:
image:
  containerPort: 8181
.
.
.
```

You can view the output to ensure it contains your desired changes (resources, livenessProbe, readinessProbe) on the `Deployment`.

---

Lets also get a view of the deployment configuration prior to upgrading the release

`export DEPLOYMENT_NAME=$(kubectl get deployments --namespace default -l "AppName=myspringapp" -o jsonpath="{.items[0].metadata.name}")`{{execute}}

`kubectl describe deployment $DEPLOYMENT_NAME`{{execute}}

As you can see, it does not currently contain our resource or liveness/readiness configurations
```yaml
  Containers:
   myspringapp:
    Image:      dockerworkshopdallas/java:release
    Port:       8181/TCP
    Host Port:  0/TCP
    Environment:  <none>
    Mounts:       <none>
```

---

Now you can upgrade your chart

`helm upgrade myspringapp myspringapp-0.3.0.tgz`{{execute}}

---

Verify your chart installed

`helm list`{{execute}}

---

Lets view the deployment configuration again after upgrading the release

`export DEPLOYMENT_NAME=$(kubectl get deployments --namespace default -l "app.kubernetes.io/instance=myspringapp" -o jsonpath="{.items[0].metadata.name}")`{{execute}}

`kubectl describe deployment $DEPLOYMENT_NAME`{{execute}}

As you can see, it now contains our resource and liveness/readiness configurations
```yaml
  Containers:
   myspringapp:
    Image:      dockerworkshopdallas/java:release
    Port:       8181/TCP
    Host Port:  0/TCP
    Limits:
      cpu:     200m
      memory:  256Mi
    Requests:
      cpu:        100m
      memory:     128Mi
    Liveness:     http-get http://:8181/actuator/health delay=60s timeout=1s period=60s #success=1 #failure=3
    Readiness:    http-get http://:8181/actuator/health delay=60s timeout=1s period=60s #success=1 #failure=3
    Environment:  <none>
    Mounts:       <none>
```

## Verify Release

Using `kubectl` commands, we can verify the release was successful

Use the following commands to see the events happening on the pod and to see its status

`export POD_NAME=$(kubectl get pods --namespace default -l "AppName=myspringapp" -o jsonpath="{.items[0].metadata.name}")`{{execute}}`

`kubectl describe pod $POD_NAME`{{execute}} You may see some 'failures' but that is likely due to the timing of our probes and the startup time of the service.

`kubectl get pod $POD_NAME --watch`{{execute}}

You may see the Ready status as `0/1`; this is because of the probe definitions and service startup time; it may take a few minutes before we see the pod as Ready. The deployment won't report that the pod is ready until all of the liveness/readiness probe criteria are met.

Wait a few minutes to and you should see that the Pod's Ready status is `1/1`. This means that 1 out of the 1 defined Containers is ready in the Pod
```shell
NAME                                           READY   STATUS    RESTARTS   AGE
myspringapp-0.3.0-deployment-d7d8d9978-q2c58   1/1     Running   0          4m43s
```

Use `ctrl + c` to exit the `watch` command

## Interact with the deployed chart

Using `kubectl`, get the running pod name

`export POD_NAME=$(kubectl get pods --namespace default -l "AppName=myspringapp" -o jsonpath="{.items[0].metadata.name}")`{{execute}}

Using `kubectl`, forward the pod's traffic on port 8181 to your machine's port 8181

`kubectl --namespace default port-forward $POD_NAME 8181:8181 &`{{execute}}

*You may need to press 'Enter' after this command to get back to the prompt*

Using `curl`, call the service on port 8181 (the forwarded port)

`curl -vvv http://127.0.0.1:8181/demo/demo1`{{execute}}

Expect to see a successful response
```
< HTTP/1.1 200
< Content-Type: text/plain;charset=UTF-8
< Content-Length: 6
< Date: Fri, 26 Jun 2020 23:30:59 GMT
<
Demo1
```

## Clean up

Stop the port forward process

`kill %1`{{execute}}

## Congrats!

Congrats, you have now successfully built your own chart from scratch, updated it, and improved it.

Move on to the next step to roll your release back.
