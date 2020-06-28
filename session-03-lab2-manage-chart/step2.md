# Create chart from template

Helm comes pre-installed with a command to create a chart from a pre-configured template with the required files and directory structure.

Run the create command to create an empty chart called **mychart**

`helm create mychart`{{execute}}

---

Check your chart is created correctly

`ls -l`{{execute}}

You should see a directory called **mychart**

---

Before we modify the chart, lets package and install your chart

Package the chart using **helm package**

`helm package ./mychart`{{execute}}

Check that your chart was packaged correctly

`ls -l`{{execute}}

You should see a file called **mychart-0.1.0.tgz**

---

Now you can install your chart

`helm install mychart mychart-0.1.0.tgz`{{execute}}

---

Verify your chart installed

`helm list`{{execute}}

## Interact with the deployed chart

Using **kubectl**, get the running pod name

`export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=mychart,app.kubernetes.io/instance=mychart" -o jsonpath="{.items[0].metadata.name}")`{{execute}}

Using **kubectl**, forward the pod's traffic on port 80 to your machine's port 8080

`kubectl --namespace default port-forward $POD_NAME 8080:80 &`{{execute}}

*You may need to press 'Enter' after this command to get back to the prompt*

Using **curl**, call the service on port 8080 (the forwarded port)

`curl http://127.0.0.1:8080`{{execute}}

![Expected response](running-service-1.png)

## Clean up

Stop the port forward process

`kill %1`{{execute}}

## Congrats!

Congrats, you have created and installed your first chart

Move on to the next step to learn how to modify your chart and upgrade the installation
