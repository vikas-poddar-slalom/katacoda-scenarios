# Setup

In this lab, you will use the Katacoda minikube cluster. Within it, you will leverage the the Argo CD GitOps operators from the previous lab and set them up to integrate with a GitLab account. You will also setup a GitLab account meant to house your app and k8s code, pipeline, and Docker images.

## GitLab Account

You will re-use the repositories from Lab 2 in this lab.

### 1. Setup GitLab Account

If you do not already have a GitLab account, create one by visiting https://gitlab.com/

Sign up for an account with
```
username: first-last-slalom
email: Slalom email
password: something secure that you will remember
```

### 2. Clone Repositories

In Lab 2, you created these repositories:
1. `my-nodejs-app`{{copy}}
1. `my-nodejs-app-config`{{copy}}

We will re-use them for this lab

In this step, clone your repositories

First set these variables to aid in configuring your local to connect with remote.

`YOUR-USER-NAME` should be your GitLab username e.g. first-last-slalom.

`export GLUSER="YOUR-USER-NAME"`{{copy}}

`export GLEMAIL="YOUR-EMAIL"`{{copy}}

`export GLNAME="Your Name"`{{copy}}

`git config --global user.email "${GLEMAIL}"`{{execute}}

`git config --global user.name "${GLNAME}"`{{execute}}

#### App Repository

`cd ~/workdir && git clone https://gitlab.com/${GLUSER}/my-nodejs-app.git`{{execute}}

#### Config Repository

`cd ~/workdir && git clone https://gitlab.com/${GLUSER}/my-nodejs-app-config.git`{{execute}}

### 4. Install Argo CD CLI

`VERSION=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')`{{execute}}  

`curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64`{{execute}}

`chmod +x /usr/local/bin/argocd`{{execute}}

Verify

`argocd version`{{execute}}

Expect to see
```
$ argocd version
argocd: v1.7.2+c342d3f
  BuildDate: 2020-08-27T23:33:37Z
  GitCommit: c342d3fc9c9c9f0d1c18254b6ffa1e106984a76c
  GitTreeState: clean
  GoVersion: go1.14.1
  Compiler: gc
  Platform: linux/amd64
FATA[0000] Argo CD server address unspecified
```

### 3. Start the minikube cluster

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
