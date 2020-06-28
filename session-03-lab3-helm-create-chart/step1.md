## Install Tools

Install kubectl and Helm CLI (Command Line Interface) tools and start the minikube cluster

---

## Start the minikube cluster

Check the status of your cluster

`minikube status`{{execute}}

If you see
```
host: Starting
kubelet: Starting
apiserver: Starting
kubeconfig: Starting
```

Give it a minute to start and check the status again

Once it is running, you should see
```
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

If you see any errors, run

`minikube start`{{execute}}

## Install Helm 3

helm is the CLI tool used to manage deployments of packages to a Kubernetes cluster

Download and Unzip the Helm package from the official Github repo

`curl -L https://get.helm.sh/helm-v3.2.2-linux-amd64.tar.gz | tar xvz && mv linux-amd64/helm /usr/local/bin/helm`{{execute}}

Check that Helm installed correctly

`helm version`{{execute}}

Expect to see
```
Version:"v3.2.2"
```

## Install kubectl

kubectl is the CLI tool used to interact with a Kubernetes cluster

You can install it from Google Storage if it is not already installed. Check if it is installed by running

`kubectl version`{{execute}}

You should expect to see a response similar to this

```
Client Version: version.Info{Major:"1", Minor:"17", GitVersion:"v1.17.3", GitCommit:"06ad960bfd03b39c8310aaf92d1e7c12ce618213", GitTreeState:"clean", BuildDate:"2020-02-11T18:14:22Z", GoVersion:"go1.13.6", Compiler:"gc", Platform:"linux/amd64"}
```

If you get an error response, do the following to install **kubectl**

`curl -Lo /usr/local/bin/kubectl https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/amd64/kubectl && chmod +x /usr/local/bin/kubectl`{{execute}}

Check that kubectl installed correctly

`kubectl version`{{execute}}
