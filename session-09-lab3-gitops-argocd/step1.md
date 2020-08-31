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
1. `my-nodejs-app`
1. `my-nodejs-app-config`

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
