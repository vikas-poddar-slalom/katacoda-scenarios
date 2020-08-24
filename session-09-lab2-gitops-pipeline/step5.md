# Setup access for k8s and the Flux Operator

Now that the operator is running in your cluster, we'll need to configure it to have access to your repository, so that it can read changes you make, as well as create commits when a service is automatically deployed.

To do this, we'll need to do a few things. Firstly, we'll need to allow the flux operator to act on the repository; secondly, we'll need the flux operator to be configured to look for cluster configuration in a particular repository. And last, we will need to add a secret that allows the cluster and Flux to pull images from the GitLab container registry

## 1. Allow Flux to operate on the repository

To allow flux to operate on the repository, we'll need to copy its public SSH key to GitLab.

We can get the operator's public key using

`fluxctl identity --k8s-fwd-ns flux`{{execute}}

The command should provide you with a public key:

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCl05 [...]
```

You now need to copy this key and add it to GitLab with `R/W` permissions for your remote repository.

Open https://gitlab.com
1. Navigate to `App Config` repository
1. In the left side navigation menu, select **Settings** > **Repository**
1. Expand the **Deploy Keys** section
1. Paste the public key that you copied into the **Key** text box
1. Give it a **Title** like `Deploy Key`{{copy}}
1. Check **Write access allowed** to enable write access for this key; this is a requirement for FluxCD
1. Click **Add key**


## 2. Verify Flux is watching this repository

In this example we are using a simple example of a webservice that returns a message on the `localhost:8181/listUsers` endpoint.

Before we make a change and verify a re-deployment, lets see that Flux has installed this service into the cluster.

By default, Flux git pull frequency is set to 5 minutes. You can tell Flux to sync the changes immediately with:

`fluxctl sync --k8s-fwd-ns flux`{{execute}}

```
$ fluxctl sync --k8s-fwd-ns flux
Synchronizing with ssh://git@gitlab.com/vikas-poddar-slalom/my-nodejs-app-config
Revision of master to apply is 7ff201d
Waiting for 7ff201d to be applied ...
Done.
```

## 3. Add a container registry secret

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

Commit and push the new `image-pull-secret.yaml` file to GitLab

`git add .`{{execute}}

`git commit -m "Adding image pull secret"`{{execute}}

`git push -u origin master`{{execute}}

---

Flux transparently looks at the image pull secrets that you attach to workloads and service accounts, and thereby uses the same credentials that Kubernetes uses for pulling each image. In general, if your pods are running, then Kubernetes has pulled the images, and Flux should be able to access them too.

## 4. Verify

If everything has been configured correctly so far, you should be able to watch for changes in your cluster and see the `nodeapp` deployment running after a little while.

`kubectl get pods -n demo --watch`{{execute}}
```
NAME                      READY   STATUS    RESTARTS   AGE
nodeapp-7599d75df-2b92b   1/1     Running   0          68s
```

Press `ctrl+c` in the terminal to exit the `watch`

---

Validate the service deployed correctly

`kubectl -n demo port-forward deployment/nodeapp 8181:8181 &`{{execute}}

Press `enter` to get the prompt back

`curl localhost:8181/listUsers`{{execute}}

Expect to see a JSON response in your terminal

Use `kill %1`{{execute}} to kill the port forwarding
