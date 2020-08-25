# Add a new API endpoint and watch your Pipeline and Flux do its magic

In this step, you will add an API endpoint to the Node app and watch GitLab build a new Docker image and publish it to your repository. After which, you can see Flux update your deployment using the new image. Continue below to configure this and understand what Flux is doing.

## 1. Add API endpoint

Using the UI editor, in the `workdir/app/server.js` add the following code below the `listUsers` function

```javascript
app.get('/:id', function (req, res) {
   // First read existing users.
   fs.readFile( __dirname + "/" + "users.json", 'utf8', function (err, data) {
      var users = JSON.parse( data );
      var user = users["user" + req.params.id]
      console.log( user );
      res.end( JSON.stringify(user));
   });
})
```

This will create an endpoint where we can get a user by ID.

Now commit and push your code to GitLab

`cd ~/workdir/app`{{execute}}

`git add .`{{execute}}

`git commit -m "Adding new get by ID endpoint"`{{execute}}

`git push`{{execute}}

## 2. Pipeline

In GitLab, for the `App` project, navigate to the pipeline and ensure it ran successfully for the new commit

## 3. Docker Image

After the pipeline runs, you should see a new image in the `App` project's container registry

## 4. k8s and Flux

If you look at the `workdir/config/workloads/nodeapp-dep.yml`, you will see these lines

```yaml
annotations:
  fluxcd.io/automated: "true"
  fluxcd.io/tag.app: semver:~1.0
```

These annotations let Flux know that we want it to watch the container registry for new images with the version pattern `1.0.x` and auto-deploy them. If you do _not_ want auto-deploy, simply remove these lines or set `fluxcd.io/automated` to **false**

Wait a few minutes and you should see the pod(s) restarted

`kubectl get pods -n demo --watch`{{execute}}
```
$ kubectl get pods -n demo --watch
NAME                       READY   STATUS        RESTARTS   AGE
nodeapp-6f76589966-v8bb2   1/1     Running       0          28s
nodeapp-74d66d8cb4-nws2z   1/1     Terminating   0          8m27s
```

Describe the pod to see the container image version is updated

`export POD_NAME=$(kubectl get pods --namespace demo -l "app=nodeapp" -o jsonpath="{.items[0].metadata.name}")`{{execute}}

`kubectl describe pod $POD_NAME -n demo`{{execute}}

Look for a line in the Events as such
```
Normal  Pulled     77s   kubelet, minikube  Successfully pulled image "registry.gitlab.com/vikas-poddar-slalom/my-nodejs-app:1.0.703238796"
```
Compare that the image version is the latest image in your registry in Gitlab.

*If you think back to Step 5, we added an image pull secret. Flux transparently uses that secret to monitor for updated images*

## 5. Verify the new deployment

The new image has a new endpoint to get user by ID. Let's hit this new endpoint.

`export CIP=$(kubectl get services -n demo -l "app=nodeapp" -o jsonpath="{.items[0].spec.clusterIP}")`{{execute}}

`curl http://${CIP}:8080/1`{{execute}}

Expect to see a JSON response with 1 user.

# Congratulations!!

Congrats! In this lab, you have successfully configured an application pipeline to auto-build your code using traditional DevOps and Flux synchronize your deployment using GitOps.
