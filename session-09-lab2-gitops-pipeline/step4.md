# Setup GitLab to use Flux over HTTPS

Within the Katacoda environment, sometimes SSH to GitLab does not work, so for the purposes of this lab, we will use the HTTPS protocol to connect Flux to the repository. To do this, we need to setup a deploy token within the `App Config` project and configure Flux to use a username/password to connect to the GitLab repository.

## 1. Create a personal access token to be used as the GIT_AUTHKEY

1. Sign in to GitLab.
1. Click on your avatar on the top right > **Settings** > **Access Tokens**
1. Give the token a name (flux-deploy-token) and expiration date (tomorrow)
1. Give the token the following scopes: read_repository, write_repository, read_registry, write_registry
1. Click the **Create personal access token** button
1. Make sure the save the generated password somewhere like a text file. If you do not save the password, you will need to create another token

## 2. Create k8s secret

Back in Katacoda, create a k8s secret with this token

Copy and execute with your token

`GIT_AUTHKEY="<access-token-generated-password>"`{{copy}}

Create a `flux` namespace

`kubectl create ns flux`{{execute}}

```bash
$ kubectl create ns flux
namespace/flux created
```

Create the secret

`kubectl create secret generic flux-git-auth --from-literal=GIT_AUTHUSER=${GLUSER} --from-literal=GIT_AUTHKEY=${GIT_AUTHKEY} -n flux`{{execute}}

This will result in a secret that has the structure

```yaml
apiVersion: v1
data:
  GIT_AUTHKEY: <base64 encoded token/password>
  GIT_AUTHUSER: <base64 encoded username>
kind: Secret
type: Opaque
metadata:
```
