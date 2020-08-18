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
