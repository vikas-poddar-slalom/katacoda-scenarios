# Create chart from scratch

In this step, we will create a chart from scratch without using a template or a jumping off point

1. Let's initialize an empty directory and setup our directory structure to house our chart files

  `mkdir -p myspringapp/templates`{{execute}}

1. Copy the **myspringapp1** files from **~/** into your chart directory

  As you are copying these, notice how the directory is changing in the editor above

  These 3 files will go to the root of the chart directory

  `cp ~/charts/helmignore1 myspringapp/.helmignore`{{execute}}

  `cp ~/charts/Chart1.yaml myspringapp/Chart.yaml`{{execute}}

  `cp ~/charts/values1.yaml myspringapp/values.yaml`{{execute}}

  The `deployment.yaml` is a Kubernetes template and will be placed in the templates directory

  `cp ~/charts/deployment1.yaml myspringapp/templates/deployment.yaml`{{execute}}

  We can see the structure of the chart either via the editor, or by running `tree -a myspringapp`{{execute}}

## Let't take a look around and see what this chart consists of

### Chart.yaml

  `Chart.yaml`{{open}}

  Defines a Chart for myspringapp with starting version 0.1.0

  All of the values defined in this file will become available to the template using the Helm `.Chart` object

### values.yaml

  `values.yaml`{{open}}

  Defines a basic set of values for the chart which will be used by the deployment configuration.

  All of the values defined in this file will become available to the template using the Helm `.Values` object

  As you look at this file, take a look at these things:
  - The docker image is `dockerworkshopdallas/java:release` which is a Dockerized Spring Boot Application available publicly in Docker Hub. This will be the image that is used by the deployment.
  - Container port is 8181; thats the port the service listens on

### deployment.yaml

  `templates/deployment.yaml`{{open}}

  Templatized Kubernetes Deployment configuration.

  The `{{ }}` directives are interpolated with the definitions in the values.yaml, Chart.yaml, and any overridden values

  `.Chart` refers to anything defined in the `Chart.yaml` and `.Values` refers to anything defined in the `values.yaml`

  Using this templating format, a single chart can be applied to any environment simply by providing a different set of values in the values.yaml or a different values file.

  As you look at this file, take a look at these things:
  - the use of the `{{ }}` directives
  - the use of the Helm `quote` function
  - the use of the `.Values`, `.Chart`, and `.Release` Helm objects

### .helmignore

  `.helmignore`{{open}}

  This file defines the files or directories that should be ignored by Helm when packaging up the chart using **helm package**. This file is not required but is a nice to have to ensure a clean helm package is created. For anyone familiar with Git, this will loo very familiar as it follows the same patterns as a `.gitignore` file

## Package Chart

Package the chart using **helm package**

`helm package myspringapp`{{execute}}

Check that your chart was packaged correctly

`ls -l`{{execute}}

You should see a file called **myspringapp-0.1.0.tgz**

## Install Chart

Now you can install your chart

`helm install myspringapp myspringapp-0.1.0.tgz`{{execute}}

Expect to see

```shell
NAME: myspringapp
LAST DEPLOYED: Mon Jun 29 22:19:12 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

## Verify the Install

Verify your chart installed

`helm list`{{execute}}

Expect to see
```shell
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
myspringapp     default         1               2020-06-29 22:19:12.316658601 +0000 UTC deployed        myspringapp-0.1.1.0
```

---

Check that the deployment and pod was created

`kubectl get deployments`{{execute}}

`kubectl get pods`{{execute}}

# Congrats!

Congrats, you have created and installed your first chart

Move on to the next step to learn how to add a Service to your chart and upgrade the release
