# Configuration Repository

## 1. Configuration Repository

Push the k8s manifest files to the configuration repository. This repository will be monitored by FluxCD to synchronize the k8s deployment with the configuration

Copy the k8s_config.tgz file to your working directory, untar it, and push the code to your application repository

`mkdir -p ~/workdir/config && cd ~/workdir/config && cp path/k8s_config.tgz . `{{execute}}

`tar xvf k8s_config.tgz && rm k8s_config.tgz`{{execute}}

`cd k8s_config`{{execute}}

`git init`{{execute}}

`git remote add origin https://gitlab.com/<YOUR-USER-NAME>/my-nodejs-app-config.git`{{copy}}
"<YOUR-USER-NAME>" will be the username you used to sign up for GitLab. For example, first-last-slalom

`git add .`{{execute}}

`git commit -m "Initial commit"`{{execute}}

`git push -u origin master`{{execute}}

This will push the k8s manifest to your repository
