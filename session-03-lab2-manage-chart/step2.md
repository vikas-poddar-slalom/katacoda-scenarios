# Create chart from template

Helm comes pre-installed with a command to create a chart from a pre-configured nginx template with the required files and directory structure.

In this section, we will use this template to explore creating, packaging, and installing a chart

## Create chart

Run the create command to create an empty chart called **mychart**

`helm create mychart`{{execute}}

Check your chart is created correctly

`tree ./mychart`{{execute}}

You should see a directory called **mychart** with subdirectories as below
```bash
$ tree mychart/
mychart/
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── ingress.yaml
│   ├── NOTES.txt
│   └── service.yaml
└── values.yaml

2 directories, 7 files
```

We will target this directory throughout the remainder of this lab

## Package Chart

Lets package and install your chart

Package the chart using

`helm package ./mychart`{{execute}}

Check that your chart was packaged correctly

`ls -l`{{execute}}

You should see a file called **mychart-0.1.0.tgz**

## Install Chart

Now you can install your chart

`helm install mychart mychart-0.1.0.tgz`{{execute}}

Expect to see some output like
```bash
$ helm install mychart mychart-0.1.0.tgz
NAME: mychart
LAST DEPLOYED: Mon Jun 29 21:00:01 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=mychart" -o jsonpath="{.items[0].metadata.name}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:80
```

## Verify the Install

Verify your chart installed

`helm list`{{execute}}

Expect to see
```shell
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
mychart default         1               2020-06-29 21:00:01.667759937 +0000 UTC deployed        mychart-0.1.0   1.16.0
```

## Interact with the deployed chart

Using **kubectl**, get the running pod name

`export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=mychart" -o jsonpath="{.items[0].metadata.name}")`{{execute}}

Using **kubectl**, forward the pod's traffic on port 80 to your machine's port 8080

`kubectl --namespace default port-forward $POD_NAME 8080:80 &`{{execute}}

*You may need to press 'Enter' after this command to get back to the prompt*

Using **curl**, call the service on port 8080 (the forwarded port)

`curl http://127.0.0.1:8080`{{execute}}

![Expected response](running-service-1.png)

## Clean up

Stop the port forward process

`kill %1`{{execute}}

*Press 'Enter' after this command*. Expect to see an output like
```bash
[1]+  Terminated              kubectl --namespace default port-forward $POD_NAME 8080:80
```

# Congrats!

Congrats, you have created and installed your first chart

Move on to the next step to learn how to modify your chart and upgrade the installation
