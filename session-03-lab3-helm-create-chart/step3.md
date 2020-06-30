# Add a Service

In this step, we will add a Service to the chart. Think of a service as a load balancer; adding this will allow us to route traffic to our deployment

1. Copy the **myspringapp2** files from **~/** into the chart directory

  `cp ~/charts/Chart2.yaml myspringapp/Chart.yaml`{{execute}}

  `cp ~/charts/values2.yaml myspringapp/values.yaml`{{execute}}

  The `service.yaml` is a Kubernetes template and will be placed in the templates directory

  `cp ~/charts/service2.yaml myspringapp/templates/service.yaml`{{execute}}

  We can see the structure of the chart either via the editor, or by running `tree -a myspringapp`{{execute}}

## Let't take a look around and see what has changed

**You may need to close the file and re-open it to see the changes**

### Chart.yaml

  `Chart.yaml`{{open}}

  As you look at this file, take a look at these changes:
  - version number has been bumped to 0.2.0

### values.yaml

  `values.yaml`{{open}}

  As you look at this file, take a look at these changes:
  - The `spec` block of values have been added to support the Service

### service.yaml

  `templates/service.yaml`{{open}}

  As you look at this file, take a look at these things:
  - the use of the `{{}}` directives
  - the use of the Helm `quote` function
  - the use of both the `.Values` and `.Release` Helm objects
  - the use of the Helm `if` control to either toggle creation of this resource based on the value set in the values file

## Render the template locally to see what will be created

Before we package and deploy the chart, we can see what the final output of rendering the templates will look like

`helm template ./myspringapp`{{execute}}

Here you will see that although we were expecting 2 templates, service and deployment, we only see 1 being rendered

This is because we are using an `if` control in our Service template and currently the values file has disabled the service

## Modify the values to enable the service and render again

Lets modify the `values.yaml` and set the value for `spec.enabled` to `true`

Re-run the `template` command to render the template again

`helm template ./myspringapp`{{execute}}

And see that Helm is now rendering both the service and the deployment

## Package Chart

Now that the Service is enabled, package the chart using **helm package**

`helm package myspringapp`{{execute}}

Check that your chart was packaged correctly

`ls -l`{{execute}}

You should see a file called **myspringapp-0.2.0.tgz**

## Upgrade Release

Now you can upgrade your chart

`helm upgrade myspringapp myspringapp-0.2.0.tgz`{{execute}}

## Verify the Upgrade

Verify your chart installed correctly

`helm list`{{execute}}

---

And check the new Service component has been deployed

`kubectl get services --namespace default -l "AppName=myspringapp"`{{execute}}

Using this command, you can also quickly verify the status of all components of the release
`helm get manifest myspringapp | kubectl get -f -`{{execute}}

## Interact with the deployed chart

Using `kubectl`, get the running pod name

`export POD_NAME=$(kubectl get pods --namespace default -l "AppName=myspringapp" -o jsonpath="{.items[0].metadata.name}")`{{execute}}

Using `kubectl`, forward the pod's traffic on port 8181 to your machine's port 8181

`kubectl --namespace default port-forward $POD_NAME 8181:8181 &`{{execute}}

*You may need to press 'Enter' after this command to get back to the prompt*

Using `curl`, call the service on port 8181 (the forwarded port)

`curl -vvv http://127.0.0.1:8181/demo/demo1`{{execute}}

Expect to see a successful response
```
< HTTP/1.1 200
< Content-Type: text/plain;charset=UTF-8
< Content-Length: 6
< Date: Fri, 26 Jun 2020 23:30:59 GMT
<
Demo1
```

## Clean up

Stop the port forward process

`kill %1`{{execute}}

# Congrats!

Congrats, you have added a Service component to your chart and upgraded it

Move on to the next step to improve your chart by defining resource requests and limitations and adding liveness and readiness probes to your release
