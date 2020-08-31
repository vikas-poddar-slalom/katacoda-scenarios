# Argo CD GUI

In this step, you will use the UI to configure Argo CD to watch your config repository

## 1. Login

The default username is `admin` and the default password is the `argocd-server` pod's name

Get the password by running  

`kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2`{{execute}}

Once you login, you should see a landing page with no applications
![Argo CD Landing Page](argocd_landingpage_empty.png)

## 2. Connect Repository

Connect your App Config Repository by clicking on the gear icon on the left-hand menu
![Argo CD Add Repo](argocd_repos_setup1.png)

Click `Connect repo using HTTPS`

```
Type: git
Repository URL: Paste the HTTPS Repo URL for the config repository in GitLab i.e. https://gitlab.com/vikas-poddar-slalom/my-nodejs-app-config.git
Username and Password: Your GitLab account credentials

Leave all either fields empty or unchecked
```

After filling everything out, select `Connect`

![Look for a successful connection](argocd_repo_connect_success.png)

## 3. Add an Application

We can now add an application to the cluster using the UI

Go back to the `Applications` by selecting the layers icon on the left-hand menu
![Apps](argocd_applications.png)

Select `New App`
![Argo CD New App](argocd_landingpage_newapp.png)

```
Application Name: My NodeJS App
Project: default
Sync Policy: Manual
Sync Options: Uncheck all boxes
Source: Paste the HTTPS Repo URL for the config repository in GitLab i.e. https://gitlab.com/vikas-poddar-slalom/my-nodejs-app-config.git
Revision: HEAD
Path: /
Cluster URL: https://kubernetes.default.svc
Namespace: demo

Leave everything else empty or unchecked
```

After filling in the form, select `Create`
