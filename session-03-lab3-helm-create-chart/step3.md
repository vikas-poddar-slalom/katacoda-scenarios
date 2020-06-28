# Add a Service

1. Copy the **myspringapp2** files from **~/** into this empty directory

  `cp ~/charts/Chart2.yaml myspringapp/Chart.yaml`{{execute}}

  `cp ~/charts/values2.yaml myspringapp/values.yaml`{{execute}}

  `cp ~/charts/service2.yaml myspringapp/templates/service.yaml`{{execute}}

  Using `ls -lR myspringapp`{{execute}} we can see the structure of the directory

## Let't take a look around and see what has changed

You can view the contents of any file using `cat` e.g. `cat myspringapp/Chart.yaml`

1. myspringapp/Chart.yaml

  The version has been changed to 0.2.0

  ```yaml
  apiVersion: v1
  appVersion: "1.0"
  description: A Helm chart for myspringapp
  name: myspringapp
  version: 0.2.0
  ```

1. myspringapp/values.yaml

  A few more values have been added to support the Service

  ```yaml
  spec:
    type: ClusterIP
    port: 8000
    targetPort: 8181
  ```

1. myspringapp/templates/service.yaml

  Templatized Kubernetes Service configuration.

  The *{{ }}* are interpolated with the definitions in the values.yaml and Chart.yaml

  **.Chart** refers to anything defined in the Chart.yaml and **.Values** refers to anything defined in the values.yaml

  Using this templating format, a single chart can be applied to any environment simply by providing a different set of values in the values.yaml or a different values file.

  ```yaml
  kind: Service
  apiVersion: v1
  metadata:
    name: {{quote .Values.labels.AppName }}
    labels:
      service: {{quote .Values.labels.AppName }}
      app.kubernetes.io/instance: {{ .Release.Name }}
      app.kubernetes.io/managed-by: {{ .Release.Service }}
      AppName: {{quote .Values.labels.AppName }}
      AppVersion: {{quote .Values.labels.AppVersion }}
  spec:
    selector:
      AppName: {{quote .Values.labels.AppName }}
    ports:
      - port: {{ .Values.spec.port }}
        targetPort: {{ .Values.spec.targetPort }}
        protocol: TCP
        name: http
  ```

## Package and Install

Package the chart using **helm package**

`helm package myspringapp`{{execute}}

Check that your chart was packaged correctly

`ls -l`{{execute}}

You should see a file called **myspringapp-0.2.0.tgz**

---

Now you can upgrade your chart

`helm upgrade myspringapp myspringapp-0.2.0.tgz`{{execute}}

---

Verify your chart installed

`helm list`{{execute}}

---

And check the new Service component has been deployed

`helm status myspringapp`{{execute}}

`kubectl get services --namespace default -l "AppName=myspringapp"`{{execute}}

Using this command, you can also quickly verify the status of all components of the release
`helm get manifest myspringapp | kubectl get -f -`{{execute}}

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

Congrats, you have added a Service component to your chart and upgraded it

Move on to the next step to improve your chart by defining resource requests and limitations and adding liveness and readiness probes to your release
