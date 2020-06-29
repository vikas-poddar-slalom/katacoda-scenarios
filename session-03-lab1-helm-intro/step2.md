## Initialize a Helm Repo

**Note: Since we're not specifying a Namespace, all commands and objects created are ran against the `default` Namespace**

Once you have Helm installed, you can add a chart repository. One popular starting location is the official Helm stable charts

A chart repository is a server that hosts a collection of charts published by other developers that can be used by others. Chart repositories can be public or private. For the purposes of this lab, we will use the official Helm stable charts public repository.

*Use* `helm --help`{{execute}} *at any time to see detailed usage instructions*

---

Before we can install a chart, we need to first add a repository to our Helm context

`helm repo list`{{execute}} will show an empty list

---

Add the stable public repository to your helm context

`helm repo add stable https://kubernetes-charts.storage.googleapis.com/`{{execute}}

With this command, we are adding the repository at https://kubernetes-charts.storage.googleapis.com/ to our local Helm configuration with the name 'stable'. When we want to interact with this repository, we can reference it using its name.

---

Verify the repo is added correctly

`helm repo list`{{execute}}

![Helm List Repos](repo-list.png)
