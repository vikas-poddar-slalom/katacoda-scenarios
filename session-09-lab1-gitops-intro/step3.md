# Share the operator's public key with the remote git repository

Now that the operator is running in your cluster, we'll need to configure it to have access to your repository, so that it can read changes you make, as well as create commits when a service is automatically deployed.

To do this, we'll need to do two things. Firstly, we'll need to allow the flux operator to act on the repository; secondly, we'll need the flux operator to be configured to look for cluster configuration in a particular repository.

## 1. Allow Flux to operate on the repository

To allow flux to operate on the repository, we'll need to copy its public SSH key to Github or Bitbucket.

We can get the operator's public key using the [fluxctl](https://github.com/weaveworks/flux/blob/master/site/fluxctl.md) tool.

First, we'll need to install the `fluxctl` tool. There are [different ways of installing it](https://github.com/weaveworks/flux/blob/master/site/fluxctl.md#installing-fluxctl) depending on your platform. For this tutorial, we can grab the binary release directly.

`wget https://github.com/weaveworks/flux/releases/download/1.8.1/fluxctl_linux_amd64`{{execute}}
`chmod +x fluxctl_linux_amd64`{{execute}}

Once installed and executable, we can use the fluxctl command line tool to get the Flux operator's public key:

`./fluxctl_linux_amd64 identity`{{execute}}

The command should provide you with a public key:

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCl05 [...]
```
You now need to copy this key and add it to github or bitbucket as a R/W key for your remote repository.

See the documentation on [how to do this for Github and Bitbucket](docs/adding-write-keys.md). Bitbucket will require the keys to be added to your profile instead of your repository.

## 2. Configure the operator to read from your repository

Now that your repository will accept read and write requests from the flux operator, we should configure Flux to use your remote repository. To do this, we'll reconfigure the Flux deployment.

Edit the `flux/flux-deployment.yaml` file to reconfigure the Flux deployment with the url of your repository by changing the arguments. Make sure to add the `--git-path=deploy/kubernetes` argument as well to point the Flux operator to the right folder.

```diff
diff --git a/flux/flux-deployment.yaml b/flux/flux-deployment.yaml
index 15d0f31..c57d3b0 100644
--- a/flux/flux-deployment.yaml
+++ b/flux/flux-deployment.yaml
@@ -100,8 +100,9 @@ spec:
         - --ssh-keygen-dir=/var/fluxd/keygen

         # replace or remove the following URL
-        - --git-url=git@github.com:weaveworks/flux-get-started
+        - --git-url=<GIT URL OF YOUR REPOSITORY>
+        - --git-path=deploy/kubernetes
         - --git-branch=master


         # include these next two to connect to an "upstream" service
         # (e.g., Weave Cloud). The token is particular to the service
```

Once you've edited and saved the file, reapply the configuration to your cluster.

`kubectl apply -f flux/flux-deployment.yaml`{{execute}}
