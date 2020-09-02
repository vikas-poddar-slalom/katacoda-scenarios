# Install and Configure the Flux Operator

Now you can install the Flux Operator in your minikube cluster. This will be similar to Lab 1 where we did the same thing.

For this install, unlike Lab 1, we will install using a yaml configuration. The provided file has the following lines added to the `Deployment` to allow use to use the Deploy Token created in the previous step

```yaml
envFrom:
- secretRef:
    name: flux-git-auth
```

You can find these lines at line number 104

## 1. Edit the install file

`cp ~/assets/flux-install.yaml ~/workdir/.`{{execute}}

Open the file in the UI editor above and modify lines 186, 190, and 191 by replacing `<your-user-name>` with your actual username e.g. `first-last-slalom`

## 2. Install Flux

Install Flux in the cluster. With the installation, you are also configuring the repository that Flux will be watching and the username and email address Flux will use when making commits to that repository. Additionally, the `--git-path=namespaces,workloads` tells Flux to only watch these filepaths in the repository. You can omit it and have Flux watch the entire repository.

`cat ~/workdir/flux-install.yaml | kubectl apply -f -`{{execute}}

Expect to see

```
serviceaccount/flux created
clusterrole.rbac.authorization.k8s.io/flux created
clusterrolebinding.rbac.authorization.k8s.io/flux created
deployment.apps/flux created
secret/flux-git-deploy created
deployment.apps/memcached created
service/memcached created
```

---

Wait for Flux to start

`kubectl -n flux rollout status deployment/flux`{{execute}}

Expect to see a successful roll out

```
$ kubectl -n flux rollout status deployment/flux
Waiting for deployment "flux" rollout to finish: 0 of 1 updated replicas are available...
deployment "flux" successfully rolled out
```

## 3. Verify Flux is watching this repository

In this example we are using a simple example of a webservice that returns a message on the `localhost:8181/listUsers` endpoint.

Before we make a change and verify a re-deployment, lets see that Flux has installed this service into the cluster.

By default, Flux git pull frequency is set to 5 minutes. You can tell Flux to sync the changes immediately with:

`fluxctl sync --k8s-fwd-ns flux`{{execute}}

Expect to see
```
$ fluxctl sync --k8s-fwd-ns flux
Synchronizing with ssh://git@gitlab.com/vikas-poddar-slalom/my-nodejs-app-config
Revision of master to apply is 7ff201d
Waiting for 7ff201d to be applied ...
Done.
```

If you get an error like
```
$ fluxctl sync --k8s-fwd-ns flux
Error: git repository ... is not ready to sync

Full error message: git repo has been cloned but not yet checked for write access
Run 'fluxctl sync --help' for usage.
```
Try running the sync command again

## 4. Add a container registry secret

The secret for image pull needs to be in a specific base64 encoded JSON string. The easiest way to create this string and k8s manifest for it is to first manually login to the Docker registry and then manually create the secret using `kubectl`. Afterwards, the secret can be exported into a YAML file and uploaded to the repository.

Using `kubectl` commands, add a secret that will be used to pull images from the repository

`docker login registry.gitlab.com`{{execute}}

Enter your username and password and login to the registry. These will the same credential you use for git

Upon success, expect to see something like:
```bash
Username: vikas.poddar@slalom.com
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

Create the secret; this secret will be used as an image pull secret

`kubectl create secret generic my-nodeapp-registry-secret --from-file=.dockerconfigjson=/root/.docker/config.json --type=kubernetes.io/dockerconfigjson -n demo`{{execute}}

---

Then use `kubectl` to get the yaml for this secret. Every namespace that needs this secret will need to deploy this secret. We will utilize Flux to help us maintain this.

`kubectl get secret my-nodeapp-registry-secret -n demo -o yaml > namespaces/image-pull-secret.yaml`{{execute}}

Clean up the secret from the namespace `kubectl delete secret my-nodeapp-registry-secret -n demo`{{execute}} and let Flux take over handling it.

Before you commit and push the new `config/namespaces/image-pull-secret.yaml` file to GitLab, open the file in the editor and remove the lines with "creationTimestamp", "resourceVersion", and "uid"

`git add . && git commit -m "Adding image pull secret" && git push`{{execute}}

---

Force sync to speed up the process. If you want to see Flux work in its natural habitat, skip this and let it sync on its own.

`fluxctl sync --k8s-fwd-ns flux`{{execute}}

---

Flux transparently looks at the image pull secrets that you attach to workloads and service accounts, and thereby uses the same credentials that Kubernetes uses for pulling each image. In general, if your pods are running, then Kubernetes has pulled the images, and Flux should be able to access them too.

## 5. Verify

If everything has been configured correctly so far, you should be able to watch for changes in your cluster and see the `nodeapp` deployment running after a little while.

`kubectl get pods -n demo --watch`{{execute}}
```
$ kubectl get pods -n demo --watch
NAME                       READY   STATUS              RESTARTS   AGE
nodeapp-74d66d8cb4-nws2z   0/1     ContainerCreating   0          5s
nodeapp-74d66d8cb4-nws2z   1/1     Running             0          39s
```

Press `ctrl+c` in the terminal to exit the `watch`

---

Validate the service deployed correctly

`kubectl -n demo port-forward deployment/nodeapp 8181:8181 &`{{execute}}

Press `enter` to get the prompt back

`curl localhost:8181/listUsers`{{execute}}

Expect to see a JSON response in your terminal

Use `kill %1`{{execute}} to kill the port forwarding
