# Configuration Repository

## 1. Configuration Repository

Push the k8s manifest files to the configuration repository. This repository will be monitored by FluxCD to synchronize the k8s deployment with the configuration

Copy the k8s_config.tgz file to your working directory, untar it, and push the code to your application repository

`mkdir -p ~/workdir/tmp && cd ~/workdir/tmp && cp ~/assets/k8s_config.tgz . `{{execute}}

`tar xvf k8s_config.tgz && mv k8s_config ~/workdir/config && cd ~/workdir && rm -rf tmp`{{execute}}

`cd ~/workdir/config`{{execute}}

`git init`{{execute}}

`git remote add origin https://gitlab.com/${GLUSER}/my-nodejs-app-config.git`{{copy}}
"<YOUR-USER-NAME>" will be the username you used to sign up for GitLab. For example, first-last-slalom

---

Using the UI editor, in the `config/workloads/nodeapp-dep.yaml`, replace "<YOUR-USER-NAME>" with the correct value e.g. first-last-slalom

Also replace the image tag **1.0.0** with the tag from your container registry in GitLab. Find it by navigating to you Container Registry and selecting your image name. This will let Kubernetes find the initial starting tag and then let Flux maintain auto-deploy with new tags afterwards.

---

`git add .`{{execute}}

`git commit -m "Initial commit"`{{execute}}

`git push -u origin master`{{execute}}

This will push the k8s manifest to your repository
