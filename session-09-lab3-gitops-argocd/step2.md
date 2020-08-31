# Install Argo CD in your cluster

In this step, you will install the Argo CD operator into your cluster.

Argo provides the k8s manifest file to do this; to follow a secure deployment model and to ensure the release that we use stays consistent, instead of directly installing the latest manifest from the internet, you can use the pre-downloaded file provided as part of this lab. The latest file is available at https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

## 1. Inspect Manifest

`cd ~/workdir && cp ~/assets/argo-cd-install.yaml .`{{execute}}

You can view the manifest in the UI editor above.

`More content to point stuff out in the manifest.`

## 2. Install

Create an `argocd` namespace and apply the manifest

`kubectl create namespace argocd`

`kubectl apply -n argocd -f argo-cd-install.yaml`{{execute}}

## 3. Open the Argo CD GUI

Argo CD provides a UI that makes it easy to manage your deployments.

In Lab 2, we saw Flux synchronize with our configuration, but all of our interactions were via the command line, and as you could see, if something failed, there was no immediate feedback on status. Additionally, you could configure Flux to only watch one repository at a time.

Argo improves upon these challenges and more. You can see a full list of features at https://argoproj.github.io/argo-cd/#features

Switch to Argo CD tab in Katacoda to see the UI
