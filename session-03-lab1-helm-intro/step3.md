# Helm CLI

Let's get familiar with Helm CLI by checking out a few different commands

To see a full list of available commands, use the **help** flag

`helm --help`{{execute}}

All `helm` commands execute against the cluster you define in your context. For the purposes of this workshop, we will be executing all commands against the local minikube cluster.

---

Common commands include
* helm search:    search for charts
* helm fetch:     download a chart to your local directory to view
* helm install:   upload the chart to Kubernetes
* helm list:      list releases of charts

## Run mysql in Kubernetes cluster

We will search the official Helm repository for a mysql chart and install the chart into the locally running Kubernetes cluster

Lets find a mysql chart.

`helm search repo stable`{{execute}}

This command returns a comprehensive list of all of the charts available in this repository.

Lets filter the list to only include the type of chart we are interested in

`helm search repo stable/mysql`{{execute}}

---

Let's install the chart into our cluster. With this command we will install the **stable/mysql** chart with a release name of **mysql**

`helm install mysql stable/mysql`{{execute}}

![Helm Install](helm-install-chart.png)

---

Verify the release is installed in the cluster

`helm list`{{execute}}

Expect to see

![Helm List](helm-list.png)

---

Check the status of the release

Lets first clear the screen as it is getting cluttered. Use this command at anytime to clear the screen.

`clear`{{execute}}

`helm status mysql`{{execute}}

Expect to see
```yaml
LAST DEPLOYED: Tue Jun 30 03:24:50 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
MySQL can be accessed via port 3306 on the following DNS name from within your cluster:
mysql.default.svc.cluster.local
.
.
.
```

---

Check the status of the release using `kubectl` commands

`kubectl get deployments`{{execute}}

`kubectl get services`{{execute}}

Expect to see

```shell
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP    11m
mysql        ClusterIP   10.98.102.99   <none>        3306/TCP   4m1s
```

`kubectl get pods --watch`{{execute}}

Once the pod has a status of Running use `ctrl+c` to exit out of the watch command.

## Uninstall the chart from the cluster

Uninstall the release

`helm delete kubernetes-dashboard`{{execute}}

---

Verify the release is uninstalled

`helm list`{{execute}}

Expect to see an empty list
```shell
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
```

---

Verify the kubernetes-dashboard components are deleted using **kubectl** commands

`kubectl get pods --watch`{{execute}}

After the pod is deleted use `ctrl+c` to exit out of the watch command.

`kubectl get deployments`{{execute}}

`kubectl get services`{{execute}}

Expect to see

```shell
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   9m2s
```

## Congrats!

That's it! Good job on completing this lab and we hope it has introduced some fundementals of Helm CLI

# Optional Step

Before we install the chart into our cluster, we may want to take a look at it to verify it is what we want or to modify it to fit your needs.

This step is not required to install a chart and can be done optionally.

Fetch the chart using

`helm fetch stable/kubernetes-dashboard`{{execute}}

This will download the actual Chart templates to your local file system.

Additionally, it is more secure to download the chart and keep a local copy of it with your source code or upload it to a private repository so you can better control version upgrades and dependencies when installing helm charts sourced from public repos.

Instead of installing the chart directly from a public repository, install it from a private repository that you control or from a local directory.

`helm install kubernetes-dashboard <path_to_local_directory>`

In this case

`helm install kubernetes-dashboard ./kubernetes-dashboard-1.11.1.tgz`
