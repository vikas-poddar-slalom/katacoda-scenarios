# Argo CD - Rollback

Rolling back or reverting in Argo CD can be done one of 2 ways
1. The normal process of reverting or undoing a commit in the Git repository
1. Via Argo CD

In production scenarios, the former method is preferred as it allows for greater control, visibility, and governance on the process. But for quick development types of processes, Argo can be used to rollback a deployment.

---

To rollback, simply select the `History and Rollback` button in the application and select the revision you want to rollback to.

You can try it now with the `namespaces` application
