# Create chart from scratch

1. Let's initialize an empty directory and setup our directory structure to house our chart files

  `mkdir myspringapp`{{execute}}

  `mkdir myspringapp/templates`{{execute}}

  Using `ls -lR myspringapp`{{execute}} we can see the structure of the directory

1. Copy the **myspringapp1** files from **~/** into your chart directory

  These 3 files will go to the root of the chart directory

  `cp ~/charts/helmignore1 myspringapp/.helmignore`{{execute}}

  `cp ~/charts/Chart1.yaml myspringapp/Chart.yaml`{{execute}}

  `cp ~/charts/values1.yaml myspringapp/values.yaml`{{execute}}

  The deployment.yaml is a Kubernetes template and will be placed in the templates directory

  `cp ~/charts/deployment1.yaml myspringapp/templates/deployment.yaml`{{execute}}

  Using `ls -alR myspringapp`{{execute}} we can see the structure of the directory
  ```
  $ ls -alR myspringapp
  myspringapp:
  total 24
  drwxr-xr-x  3 root root 4096 Jun 28 15:17 .
  drwx------ 15 root root 4096 Jun 28 15:17 ..
  -rw-r--r--  1 root root  108 Jun 28 15:17 Chart.yaml
  -rw-r--r--  1 root root  342 Jun 28 15:17 .helmignore
  drwxr-xr-x  2 root root 4096 Jun 28 15:18 templates
  -rw-r--r--  1 root root  180 Jun 28 15:17 values.yaml

  myspringapp/templates:
  total 12
  drwxr-xr-x 2 root root 4096 Jun 28 15:18 .
  drwxr-xr-x 3 root root 4096 Jun 28 15:17 ..
  -rw-r--r-- 1 root root 1054 Jun 28 15:18 deployment.yaml
  ```

## Let't take a look around and see what this chart consists of

You can view the contents of any file using `cat` e.g. `cat myspringapp/Chart.yaml`

1. myspringapp/Chart.yaml{{editor}}

  Defines a Chart for myspringapp with starting version 0.1.0

  ```yaml
  apiVersion: v1
  appVersion: "1.0"
  description: A Helm chart for myspringapp
  name: myspringapp
  version: 0.1.0
  ```

1. myspringapp/values.yaml{{editor}}

  Basic set of values for a Deployment

  ```yaml
  replicaCount: 1

  labels:
    AppName: myspringapp
    AppVersion: "1.0"

  image:
    repository: dockerworkshopdallas/java
    tag: release
    pullPolicy: IfNotPresent
    containerPort: 8181
  ```

  We will run the dockerworkshopdallas/java:release Spring Boot Application available publicly in Dockerhub

1. myspringapp/templates/deployment.yaml{{editor}}

  Templatized Kubernetes Deployment configuration.

  The *{{ }}* are interpolated with the definitions in the values.yaml and Chart.yaml

  **.Chart** refers to anything defined in the Chart.yaml and **.Values** refers to anything defined in the values.yaml

  Using this templating format, a single chart can be applied to any environment simply by providing a different set of values in the values.yaml or a different values file.

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
  ```

1. myspringapp/.helmignore{{editor}}

  This file defines the files or directories that should be ignored by Helm when packaging up the chart using **helm package**. This file is not required but is a nice to have to ensure a clean helm package is created.

  ```yaml
  # Patterns to ignore when building packages.
  # This supports shell glob matching, relative path matching, and
  # negation (prefixed with !). Only one pattern per line.
  .DS_Store
  # Common VCS dirs
  .git/
  .gitignore
  .bzr/
  .bzrignore
  .hg/
  .hgignore
  .svn/
  # Common backup files
  *.swp
  *.bak
  *.tmp
  *~
  # Various IDEs
  .project
  .idea/
  *.tmproj
  .vscode/
  ```

## Package and Install

Package the chart using **helm package**

`helm package myspringapp`{{execute}}

Check that your chart was packaged correctly

`ls -l`{{execute}}

You should see a file called **myspringapp-0.1.0.tgz**

---

Now you can install your chart

`helm install myspringapp myspringapp-0.1.0.tgz`{{execute}}

---

Verify your chart installed

`helm list`{{execute}}

---

Check that the deployment and pod was created

`kubectl get deployments`{{execute}}

`kubectl get pods`{{execute}}

## Congrats!

Congrats, you have created and installed your first chart

Move on to the next step to learn how to add a Service to your chart and upgrade the release
