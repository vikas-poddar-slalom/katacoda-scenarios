# Argo CD GUI

In this step, you will use the UI to configure Argo CD to watch your config repository

## 1. Login

The default username is `admin` and the default password is the `argocd-server` pod's name

Back in the `Terminal` tab, get the password by running  

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

After filling everything out, select `Connect`. Look for a successful connection
![Look for a successful connection](argocd_repo_connect_success.png)

## 3. Add an Application

We can now add an application to the cluster using the UI

Go back to the `Applications` by selecting the layers icon on the left-hand menu
![Apps](argocd_applications.png)

Select `New App`
![Argo CD New App](argocd_landingpage_newapp.png)

```
Application Name: my-nodejs-app
Project: default
Sync Policy: Manual
Sync Options: Uncheck all boxes
Source: Paste the HTTPS Repo URL for the config repository in GitLab i.e. https://gitlab.com/vikas-poddar-slalom/my-nodejs-app-config.git
Revision: HEAD
Path: workloads
Cluster URL: https://kubernetes.default.svc

Leave everything else empty or unchecked
```

Create another application for the `namespace` directory

```
Application Name: my-app-namespaces
Project: default
Sync Policy: Manual
Sync Options: Uncheck all boxes
Source: Paste the HTTPS Repo URL for the config repository in GitLab i.e. https://gitlab.com/vikas-poddar-slalom/my-nodejs-app-config.git
Revision: HEAD
Path: namespaces
Cluster URL: https://kubernetes.default.svc

Leave everything else empty or unchecked
```

After filling in the form, select `Create`

Expect to see an `Out of Sync` status. This is because you set the sync type to manual and have to initiate the sync
![Out of Sync](argocd_app_outofsync.png)

## 4. Manual Sync

Select the `namespaces` application, and click on `Sync`

Ensure the demo namespace is selected and click on `Synchronize`
![Synchronize](argocd_namespaces_sync.png)

If the repo and app are setup correctly, expect to see a successful sync
![Great Success](argocd_namespaces_sync_success.png)

In Katacoda, go back to the `Terminal` tab and verify the `demo` namespace was created

`kubectl get ns`{{execute}}

---

Back in ArgoCD, sync the `nodejs-app` application now using the same steps.
![Synchronize](argocd_nodeapp_sync.png)

Expect to see a successful sync here as well
![Graph](argocd_nodeapp_sync_success.png)

You will see ArgoCD build out a graph of the release. The application will have the following structure
* Deployment
    * Replica Set
        * Pod
* Service
    * Endpoint

You can explore the logs and events for a Pod directly from the UI by selecting the Pod.

In Katacoda's `Terminal`, verify your things were created

`kubectl get all -n demo`{{execute}}

```bash
NAME                           READY   STATUS    RESTARTS   AGE
pod/nodeapp-5d6c998b87-gt8v6   1/1     Running   0          7m35s

NAME              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/nodeapp   ClusterIP   10.102.196.89   <none>        8080/TCP   7m37s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nodeapp   1/1     1            1           7m38s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/nodeapp-5d6c998b87   1         1         1       7m38s
```

---

If everything has been configured correctly so far, you should be able see a new `nodeapp` service running.

Using the `Terminal` tab

`kubectl get services -n demo`{{execute}}
```bash
$ kubectl get services -n demo
NAME      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
nodeapp   ClusterIP   10.108.95.142   <none>        8080/TCP   9s
```

Confirm the change by hitting the service directly without port-forward using the IP Address above

`export CIP=$(kubectl get services -n demo -l "app=nodeapp" -o jsonpath="{.items[0].spec.clusterIP}")`{{execute}}

`curl http://${CIP}:8080/listUsers`{{execute}}

Expect to see a JSON response with 4 users.

## 5. Auto-Sync

Lets enable auto-sync on the `namespaces` application and watch ArgoCD automatically sync it.

To do this, select the **namespaces application** in Argo CD > **App Details** in the top left > **Edit** > **Enable Auto-Sync** > **Ok** > **Save** > Click on the 'X' to close the pop-out

*Note: When I did this, the setting wouldn't save the first time so I had to do the above steps again to persist the change*

---

Back in Katacoda, add a new namespace like `demo2` to the namespaces directory and push it to the git repository

`cp ~/assets/demo2.yaml ~/workdir/my-nodejs-app-config/namespaces/.`{{execute}}

Commit and push this new configuration

`cd ~/workdir/my-nodejs-app-config && git add . && git commit -m 'adding demo2 namespace'`{{execute}}

`git push`{{execute}}

---

In Argo CD, watch the `namespaces application` to see Argo CD automatically sync the application and create the demo2 namespace. Hit the `Refresh` button if it does not update.

You should now see a `demo2` namespace in the UI.

In the Katacoda terminal, verify the `demo2` namespace was created

`kubectl get ns`{{execute}}

---

# Congrats

Congrats! You have finished Lab 3 of the GitOps sessions!
