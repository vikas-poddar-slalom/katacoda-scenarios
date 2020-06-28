## Initialize a Helm Repo
### Before we start
- **We recommend that you type out the commands to become more familiar with `kubectl` and `helm` but you can also copy them to save time!**
- **If you're feeling stuck with `kubectl` or `helm` don't forget about the `-h` `--help` switch**
- **The `-h` or `--help` switch can be used at any point in a command to see usage details for that context e.g. `helm repo add --help` versus `helm --help`**
- **Since we're not specifying a Namespace, all commands and objects created are ran against the `default` Namespace**

Once you have Helm installed, you can add a chart repository. One popular starting location is the official Helm stable charts

A chart repository is a server that hosts a collection of charts published by other developers that can be used by others. Chart repositories can be public or private. For the purposes of this lab, we will use the official Helm stable charts public repository.

---

Add the repository to your helm installation
`helm repo add stable https://kubernetes-charts.storage.googleapis.com/`{{execute}}
With this command, we are adding the repository at https://kubernetes-charts.storage.googleapis.com/ to our local Helm configuration with the name 'stable'. When we want to interact with this repository, we can reference it using its name.

---

Verify the repo is added correctly
`helm repo list`{{execute}}
![Helm List Repos](repo-list.png)
