# Harden the Release

1. Copy the **myspringapp3** files from **~/** into this empty directory

  `cp ~/charts/Chart3.yaml myspringapp/Chart.yaml`{{execute}}

  `cp ~/charts/values3.yaml myspringapp/values.yaml`{{execute}}

  `cp ~/charts/deployment3.yaml myspringapp/templates/deployment.yaml`{{execute}}

  Using `ls -lR myspringapp`{{execute}} we can see the structure of the directorys

## Let't take a look around and see what has changed

You can view the contents of any file using `cat` e.g. `cat myspringapp/Chart.yaml`

1. myspringapp/Chart.yaml

  The version has been changed to 0.3.0

  ```yaml
  apiVersion: v1
  appVersion: "1.0"
  description: A Helm chart for myspringapp
  name: myspringapp
  version: 0.3.0
  ```

1. myspringapp/values.yaml

  A few more values have been added to improve the Deployment

  The **resources** section defines resource requests that the deployment makes when pods are being started on the cluster. The cluster will try to schedule pods on nodes where it can fulfill the CPU and Memory requirements defined here. For example, this deployment is specifying that the pod can start with .1 cores and 128Mb of RAM, but can consume upto .2 cores and 256Mb of RAM, so the control plane in the cluster will only spin up pods of this deployment on nodes where it can fulfill those resource requirements.

  ```yaml
  resources:
    # We usually recommend not to specify default resources and to leave this as a conscious
    # choice for the user. This also increases chances charts run on environments with little
    # resources, such as Minikube. If you do want to specify resources, uncomment the following
    # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
    limits:
     cpu: 200m
     memory: 256Mi
    requests:
     cpu: 100m
     memory: 128Mi
  ```

  Liveness and Readiness probes are used to define the requirements by which a pod can say it is live and then ready to receive traffic. When a pod is live, it will indicate this status to the cluster, but until it is ready, it will not receive any traffic.

  Kubernetes uses the liveness probe to determine when to restart a container, and the readiness probe to determine when the pod is ready to accept traffic.

  We have setup some rules for polling on both liveness and readiness:
  1. Give the service a few seconds to start up
  1. Set a time interval between polls
  1. For liveness, the number of polls that need to fail to consider the pod startup as failed; this will signal the cluster to remove the pod and replace it with another pod
  1. For readiness, the number of successful responses needed to consider the pod ready to receive traffic

  ```yaml
  livenessProbe:
    path: /demo/demo1
    port: 8181
    initialDelaySeconds: 60
    periodSeconds: 60
    failureThreshold: 3

  readinessProbe:
    path:  /demo/demo1
    port: 8181
    initialDelaySeconds: 60
    periodSeconds: 60
    successThreshold: 1
  ```

1. myspringapp/templates/deployment.yaml

  Using the templatized configuration afforded by Golang, we make use of the new values in our containers specification (near the bottom of the file: resources, livenessProbe, readinessProbe)

  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: {{ .Chart.Name }}-{{ .Chart.Version }}-deployment
    labels:
      run: {{quote .Values.labels.AppName }}
      app.kubernetes.io/instance: {{ .Release.Name }}
      app.kubernetes.io/managed-by: {{ .Release.Service }}
      AppName: {{quote .Values.labels.AppName }}
      AppVersion: {{quote .Values.labels.AppVersion }}
  spec:
    selector:
      matchLabels:
        run: {{quote .Values.labels.AppName }}
    replicas: {{ .Values.replicaCount }}
    template:
      metadata:
        labels:
          run: {{quote .Values.labels.AppName }}
          AppName: {{quote .Values.labels.AppName }}
          container: app
      spec:
        containers:
          - name: {{quote .Values.labels.AppName }}
            image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
            ports:
              - containerPort:  {{ .Values.image.containerPort }}
            resources:
              {{- toYaml .Values.resources | nindent 12 }}
            livenessProbe:
              httpGet:
                path: {{ .Values.livenessProbe.path }}
                port: {{ .Values.livenessProbe.port }}
              initialDelaySeconds: {{ .Values.livenessProbe.initialDelaySeconds }}
              periodSeconds: {{ .Values.livenessProbe.periodSeconds }}
              failureThreshold: {{ .Values.livenessProbe.failureThreshold }}
            readinessProbe:
              httpGet:
                path:  {{ .Values.readinessProbe.path }}
                port:  {{ .Values.readinessProbe.port }}
              initialDelaySeconds: {{ .Values.readinessProbe.initialDelaySeconds }}
              periodSeconds: {{ .Values.readinessProbe.periodSeconds }}
              successThreshold: {{ .Values.readinessProbe.successThreshold }}
  ```

  With these changes, we have improved the overall reliability of our deployment; now, a Pod will only be scheduled to start on nodes that can support its resource requirements and the cluster will validate readiness and liveness of the underlying service before directing traffic to the Pod. If a Pod fails to meet the probe criteria, it will automatically be removed and replaced with another pod.

## Package and Install

Package the chart using **helm package**
`helm package myspringapp`{{execute}}

Check that your chart was packaged correctly
`ls -l`{{execute}}
You should see a file called **myspringapp-0.3.0.tgz**

---

Prior to upgrading, because we made multiple changes to deployment.yaml, it would be nice to validate that our changes are correct and have the desired effect.

For this, we can leverage the **dry-run** and **debug** flags

`helm upgrade myspringapp myspringapp-0.3.0.tgz --dry-run --debug`{{execute}}

If the command returns output without any errors, then your template is valid in terms of formatting.

Expect to see something like
```
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

You can view the output to ensure it contains your desired changes (resources, livenessProbe, readinessProbe).

```
# Source: myspringapp/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myspringapp-0.3.0-deployment
  labels:
    run: "myspringapp"
    app.kubernetes.io/instance: myspringapp
    app.kubernetes.io/managed-by: Helm
    AppName: "myspringapp"
    AppVersion: "1.0"
spec:
  selector:
    matchLabels:
      run: "myspringapp"
  replicas: 1
  template:
    metadata:
      labels:
        run: "myspringapp"
        app.kubernetes.io/instance: myspringapp
        app.kubernetes.io/managed-by: Helm
        AppName: "myspringapp"
        AppVersion: "1.0"
        container: app
    spec:
      containers:
        - name: "myspringapp"
          image: "dockerworkshopdallas/java:release"
          ports:
            - containerPort:  8181
          resources:
            limits:
              cpu: 200m
              memory: 256Mi
            requests:
              cpu: 100m
              memory: 128Mi
          livenessProbe:
            httpGet:
              path: /demo/demo1
              port: 8181
            initialDelaySeconds: 60
            periodSeconds: 60
            failureThreshold: 3
          readinessProbe:
            httpGet:
              path:  /demo/demo1
              port:  8181
            initialDelaySeconds: 60
            periodSeconds: 60
            successThreshold: 1
```

---

Lets also get a view of the deployment configuration prior to upgrading the release

`export DEPLOYMENT_NAME=$(kubectl get deployments --namespace default -l "AppName=myspringapp" -o jsonpath="{.items[0].metadata.name}")`{{execute}}

`kubectl describe deployment $DEPLOYMENT_NAME`{{execute}}

As you can see, it does not currently contain our resource or liveness/readiness configurations
```
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
```
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

Using some `kubectl` commands, we can verify the release was successful

Use the following commands to see the events happening on the pod and to see its status

`export POD_NAME=$(kubectl get pods --namespace default -l "AppName=myspringapp" -o jsonpath="{.items[0].metadata.name}")`{{execute}}`

`kubectl describe pod $POD_NAME`{{execute}} You may see some 'failures' but that is likely due to the timing of our probes and the startup time of the service.

`kubectl get pod $POD_NAME --watch`{{execute}} If you see the Ready status as 0/1: Because of the probe definitions and service startup time, it may take a few minutes before we see the pod as Ready. The deployment won't report that the pod is ready until all of the liveness/readiness probe criteria are met.

Eventually you should see that the Pod's Ready status is 1/1. This means that 1 out of the 1 defined Containers is ready in the Pod
```
NAME                                           READY   STATUS    RESTARTS   AGE
myspringapp-0.5.0-deployment-d7d8d9978-q2c58   1/1     Running   0          4m43s
```

Use `ctrl + c` to exit the watch

## Interact with the deployed chart

Using **kubectl**, get the running pod name

`export POD_NAME=$(kubectl get pods --namespace default -l "AppName=myspringapp" -o jsonpath="{.items[0].metadata.name}")`{{execute}}

Using **kubectl**, forward the pod's traffic on port 8181 to your machine's port 8181

`kubectl --namespace default port-forward $POD_NAME 8181:8181 &`{{execute}}

*You may need to press 'Enter' after this command to get back to the prompt*

Using **curl**, call the service on port 8181 (the forwarded port)

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
