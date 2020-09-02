# Setup

In this lab, you will use the Katacoda minikube cluster. Within it, you will leverage the the Flux GitOps operators from the previous lab and set them up to integrate with a GitLab account. You will also setup a GitLab account meant to house your app and k8s code, pipeline, and Docker images.

## GitLab Account

You'll want to create a new repository for this tutorial and make it available from your cluster through a git URL.

Of course, you can also configure the Flux operator to use a private git host, but configuring this is outside the scope of this introductory tutorial.

### 1. Setup GitLab Account

If you do not already have a GitLab account, create one by visiting https://gitlab.com/

Sign up for an account with
```
username: first-last-slalom
email: Slalom email
password: something secure that you will remember
```

**Keep your GitLab credentials handy as you will be prompted for them several times during Lab 2 and 3**

### 2. Create Repositories

In GitLab, create 2 blank projects with a visibility level of private; for the slugs, use:
1. `my-nodejs-app`{{copy}}
1. `my-nodejs-app-config`{{copy}}

To create a new project, click the drop down next to the `Search` bar.

If you want, for ease of use, and if you are already familiar with Git CLI, you can use setup SSH access in your account. But for the purposes of this lab, we will be doing HTTPS as it requires less setup.

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
