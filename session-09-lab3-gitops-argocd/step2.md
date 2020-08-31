# Install Argo CD in your cluster

In this step, you will install the Argo CD operator into your cluster.

Argo provides the k8s manifest file to do this; to follow a secure deployment model and to ensure the release that we use stays consistent, instead of directly installing the latest manifest from the internet, you can use the pre-downloaded file provided as part of this lab. The latest file is available at https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

## 1. Inspect Manifest

`cd ~/workdir && cp ~/assets/argo-cd-install.yaml .`{{execute}}

You can view the manifest in the UI editor above.

`More content to point stuff out in the manifest.`

## 2. Install

Create an `argocd` namespace and apply the manifest

`kubectl create namespace argocd`{{execute}}

`kubectl apply -n argocd -f argo-cd-install.yaml`{{execute}}

Wait for the pods to start

`kubectl get pods -n argocd --watch`{{execute}}

Expect to see the following pods starting up and reach a running state
```
NAME                                             READY   STATUS    RESTARTS   AGE
argocd-application-controller-64f989c7c4-qvgnw   1/1     Running   0          2m2s
argocd-dex-server-688b8f4f48-rbk7c               1/1     Running   0          2m2s
argocd-redis-54b6ff7bf6-2lpx8                    1/1     Running   0          2m2s
argocd-repo-server-7bb588466-8vdvx               1/1     Running   0          2m2s
argocd-server-7cb78565f5-pr75m                   1/1     Running   0          2m2s
```

Use `ctrl+c` to exit the watch

## 3. Open the Argo CD GUI

Argo CD provides a UI that makes it easy to manage your deployments.

In Lab 2, we saw Flux synchronize with our configuration, but all of our interactions were via the command line, and as you could see, if something failed, there was no immediate feedback on status. Additionally, you could configure Flux to only watch one repository at a time.

Argo improves upon these challenges and more. You can see a full list of features at https://argoproj.github.io/argo-cd/#features

Switch to the `Port Forward` terminal to run this command

`kubectl port-forward svc/argocd-server -n argocd 8080:443 --address 0.0.0.0`{{execute}}

We have to add `--address 0.0.0.0` to allow the port-forward to allow the Katacoda Proxy work with the forwarding.

After port-forwarding, select the `Argo CD` tab to open a browser window to the GUI.

Or click the link https://[[HOST_SUBDOMAIN]]-8080-[[KATACODA_HOST]].environments.katacoda.com

You should be directed to a login page
