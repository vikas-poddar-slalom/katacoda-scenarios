# Argo CD - Rollback

Rolling back or reverting in Argo CD can be done one of 2 ways
1. The normal process of reverting or undoing a commit in the Git repository
1. Via Argo CD

In production scenarios, the former method is preferred as it allows for greater control, visibility, and governance on the process. But for quick development types of processes, Argo can be used to rollback a deployment.

---

*Note: In my testing, this did not work. If it does not work for you, it is expected; if it does work for you, you are a wizard. In any case, the preferred method for doing rollbacks is still via your source control. Because the preferred way is using Git, you have the option to skip this section and move on to the next one...I recommended this.*

To rollback, select the `History and Rollback` button in the application and select the revision you want to rollback to.

You can try it now with the `namespaces` application

After rolling back, verify the `demo2` namespace is deleted

`kubectl get ns`{{execute T1}}

---

The preferred method is reverting via Git. So lets try that here

Go to https://gitlab.com/your-user-name/my-nodejs-app-config/-/commits/master to view the commits; remember to replace "your-user-name" with your GitLab username

Find and click on the commit message `adding demo2 namespace`

In the top-right **Options** dropdown, click **Revert**

**Revert in master branch** > Uncheck **start a merge request** > Click **Revert**

---

Switch back to the `Argo CD` tab in Katacoda and watch the `namespace` application. You should see the `demo2` namespace be deleted after a few minutes. Hit `Refresh` if the view doesn't update on its own or if you are in a hurry.

Verify the `demo2` namespace is deleted from the cluster

`kubectl get ns`{{execute T1}}

# Congrats

Congrats! You have finished Lab 3 of the GitOps sessions!
