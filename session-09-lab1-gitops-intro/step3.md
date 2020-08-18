# Share the operator's public key with the remote git repository

Now that the operator is running in your cluster, we'll need to configure it to have access to your repository, so that it can read changes you make, as well as create commits when a service is automatically deployed.

To do this, we'll need to do two things. Firstly, we'll need to allow the flux operator to act on the repository; secondly, we'll need the flux operator to be configured to look for cluster configuration in a particular repository.

## 1. Allow Flux to operate on the repository

To allow flux to operate on the repository, we'll need to copy its public SSH key to Github or Bitbucket.

We can get the operator's public key using

`fluxctl identity --k8s-fwd-ns flux`{{execute}}

The command should provide you with a public key:

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCl05 [...]
```

You now need to copy this key and add it to github as a R/W key for your remote repository.

Open GitHub, navigate to your fork, go to *Setting* > *Deploy keys*, click on *Add deploy key*, give it a `Title` like `Deploy Key`, check *Allow write access*, paste the public key and click Add key.

## 2. Verify Flux is watching this repository

In this example we are using a simple example of a webservice that returns a message.

Before we make a change and verify a re-deployment, lets see that Flux has installed this service into the cluster.

By default, Flux git pull frequency is set to 5 minutes. You can tell Flux to sync the changes immediately with:

`fluxctl sync --k8s-fwd-ns flux`{{execute}}

```
$ fluxctl sync --k8s-fwd-ns flux
Synchronizing with ssh://git@github.com/vikas-poddar-slalom/flux-get-started
Revision of master to apply is 7ff201d
Waiting for 7ff201d to be applied ...
Done.
```

---

If everything has been configured correctly so far, you should be able to watch for changes in your cluster and see the `podinfo` deployment running after a little while.

`watch kubectl get pods -n demo`{{execute}}
```
NAME                      READY   STATUS    RESTARTS   AGE
podinfo-54d69dd76-q2pvv   1/1     Running   0          92s
podinfo-54d69dd76-zrs2b   1/1     Running   0          81s
```

Press `ctrl+c` in the terminal to exit the `watch`

---

Validate the service deployed correctly

`kubectl -n demo port-forward deployment/podinfo 9898:9898 &`{{execute}}

Press `enter` to get the prompt back

`curl localhost:9898`{{execute}}

Make note of the `message` value in the JSON reply.

Use `kill %1`{{execute}} to kill the port forwarding

---

Move on to the next step where we will make a change to the deployment code and watch Flux automatically update the k8s deployment to match!
