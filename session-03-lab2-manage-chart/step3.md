# Upgrade mychart

Now that you have created your own chart called **mychart** from a template, you can modify it to fit your needs and upgrade the deployment


## Modify mychart

1. Replace the Chart.yaml in mychart/ with a modified version  

  `cp /usr/local/bin/Chart.yaml mychart/.`{{execute}}

  The changed version has a new description and upgraded version number
  ```yaml
  apiVersion: v2
  name: mychart
  description: My Helm chart for Kubernetes

  # A chart can be either an 'application' or a 'library' chart.
  #
  # Application charts are a collection of templates that can be packaged into versioned archives
  # to be deployed.
  #
  # Library charts provide useful utilities or functions for the chart developer. They're included as
  # a dependency of application charts to inject those utilities and functions into the rendering
  # pipeline. Library charts do not define any templates and therefore cannot be deployed.
  type: application

  # This is the chart version. This version number should be incremented each time you make changes
  # to the chart and its templates, including the app version.
  # Versions are expected to follow Semantic Versioning (https://semver.org/)
  version: 0.2.0

  # This is the version number of the application being deployed. This version number should be
  # incremented each time you make changes to the application. Versions are not expected to
  # follow Semantic Versioning. They should reflect the version the application is using.
  appVersion: 1.16.0
  ```

## Lets upgrade the deployment

1. Before we upgrade, if you run

  `helm list`{{execute}}

  You should see that the version is **0.1.0**

  ```yaml
  NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
  mychart default         1               2020-06-22 16:43:20.782908915 +0000 UTC deployed        mychart-0.1.0   1.16.0
  ```

1. Package and Upgrade

  Package the chart using **tar**
  `helm package ./mychart`{{execute}}

  Check that your chart was packaged correctly

  `ls -l`{{execute}}

  You should see a file called **mychart-0.2.0.tgz**

  ---

  Because the chart is already installed, use the **helm upgrade** command to upgrade the chart

  `helm upgrade mychart mychart-0.2.0.tgz`{{execute}}

  See that the **REVISION** has changed to **2**
  ```yaml
  Release "mychart" has been upgraded. Happy Helming!
  NAME: mychart
  LAST DEPLOYED: Mon Jun 22 16:46:54 2020
  NAMESPACE: default
  STATUS: deployed
  REVISION: 2
  NOTES:
  1. Get the application URL by running these commands:
    export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=mychart" -o jsonpath="{.items[0].metadata.name}")
    echo "Visit http://127.0.0.1:8080 to use your application"
    kubectl --namespace default port-forward $POD_NAME 8080:80
  ```

  ---

  Verify your chart upgraded

  `helm list`{{execute}}

  See that the **REVISION** has changed to **2** and the **CHART** is now set to **mychart-0.2.0**
  ```yaml
  NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
  mychart default         2               2020-06-22 16:46:54.918411351 +0000 UTC deployed        mychart-0.2.0   1.16.0
  ```

# Congrats!

Congrats, you have modified and upgraded your first chart

Move on to the next section to see how to rollback your chart to a previous version
