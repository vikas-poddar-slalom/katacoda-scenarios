# Install Tools

Install Helm CLI (Command Line Interface) tools and start the minikube cluster if it is not already running

## Install Helm 3

helm is the CLI tool used to manage deployments of packages to a Kubernetes cluster

Download and Unzip the Helm package from the official Github repo

`curl -L https://get.helm.sh/helm-v3.2.2-linux-amd64.tar.gz | tar xvz && mv linux-amd64/helm /usr/local/bin/helm`{{execute}}

Check that Helm installed correctly

`helm version`{{execute}}

Expect to see
```shell
Version:"v3.2.2"
```

## Start the minikube cluster

Check the status of your cluster

`minikube status`{{execute}}

You should see
```shell
host: Starting
kubelet: Starting
apiserver: Starting
kubeconfig: Starting
```

Give it a minute to start and check the status again

Once it is running, you should see
```shell
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

If you see any errors, run

`minikube start`{{execute}}
