# Configuration Repository

In this step, you will setup your Config Repository with 2 k8s manifests to create your namespace and your deployment.

## 1. Configuration Repository

Push the k8s manifest files to the configuration repository. This repository will be monitored by FluxCD to synchronize the k8s deployment with the configuration

Copy the k8s_config.tgz file to your working directory, untar it, and push the code to your application repository

`mkdir -p ~/workdir/tmp && cd ~/workdir/tmp && cp ~/assets/k8s_config.tgz .`{{execute}}

`tar xvf k8s_config.tgz && mv k8s_config ~/workdir/config && cd ~/workdir && rm -rf tmp`{{execute}}

---

Using the UI editor, in the `config/workloads/nodeapp-dep.yaml`, replace `<YOUR-USER-NAME>` with the correct value e.g. first-last-slalom

Also replace the image version **1.0.0** with the version from your container registry in GitLab. Find it by navigating to your Container Registry and selecting your image name. This will let Kubernetes find the initial starting tag and then let Flux maintain auto-deploy with new tags afterwards.

The line should finally look like `image: registry.gitlab.com/vikas-poddar-slalom/my-nodejs-app:1.0.703198003` but with your own values

---

Now you can push the k8s manifests to your config repository

`cd ~/workdir/config`{{execute}}

`git init`{{execute}}

`git remote add origin https://gitlab.com/${GLUSER}/my-nodejs-app-config.git`{{execute}}

`git add . && git commit -m "Initial commit" && git push -u origin master`{{execute}}
