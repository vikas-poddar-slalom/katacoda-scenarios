# Argo CD - Rollback

Rolling back or reverting in Argo CD can be done one of 2 ways
1. The normal process of reverting or undoing a commit in the Git repository
1. Via Argo CD

In production scenarios, the former method is preferred as it allows for greater control, visibility, and governance on the process. But for quick development types of processes, Argo can be used to rollback a deployment.

---

*Note: In my testing, this did not work. If it doesn't work for you, it is expected; if it does work for you, you are a wizard. In any case, the preferred method for doing rollbacks is still via your source control.*

To rollback, select the `History and Rollback` button in the application and select the revision you want to rollback to.

You can try it now with the `namespaces` application

After rolling back, verify the `demo2` namespace is deleted

`kubectl get ns`{{execute T1}}

# Congrats

Congrats! You have finished Lab 3 of the GitOps sessions!
