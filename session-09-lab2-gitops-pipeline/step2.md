# Application Repository, Docker Image registry, and Pipeline

In this step, you will push a simple Node webservice to the App Repository and set up the project to build Docker images and publish these images to the built-in container registry via a pipeline.

## 1. App Repository

Push the starter Node Application to your GitLab CI repository

First set these variables to aid in configuring your local to connect with remote.

`YOUR-USER-NAME` should be your GitLab username e.g. first-last-slalom.

`export GLUSER="YOUR-USER-NAME"`{{copy}}

`export GLEMAIL="YOUR-EMAIL"`{{copy}}

`export GLNAME="Your Name"`{{copy}}

`git config --global user.email "${GLEMAIL}"`{{execute}}

`git config --global user.name "${GLNAME}"`{{execute}}

---

Copy the nodejs_app.tgz file to your working directory, untar it, and push the code to your application repository

`mkdir -p ~/workdir/tmp && cd ~/workdir/tmp && cp ~/assets/nodejs_app.tgz . `{{execute}}

`tar xvf nodejs_app.tgz && mv nodejs_app ~/workdir/app && cd ~/workdir && rm -rf tmp`{{execute}}

`cd ~/workdir/app`{{execute}}

`git init`{{execute}}

`git remote add origin https://gitlab.com/${GLUSER}/my-nodejs-app.git`{{execute}}

`git add .`{{execute}}

`git commit -m "Initial commit"`{{execute}}

`git push -u origin master`{{execute}}

This will push the starter application code to your repository

## 2. Docker and Image Registry

With GitLab, your new project already comes with a container registry. To utilize this, we will define a Dockerfile which builds a Docker image for your Node application and in the next step create a pipeline that will publish images to this repository

Copy the `Dockerfile` to your application directory

`cp ~/assets/Dockerfile .`{{execute}}

Before you push to this file, verify that it build correctly

`docker build .`{{execute}}

Expect to see a successful build. You can also take some time here to review the application code and Dockerfile in the editor above.

To prevent the container from being too heavy, the node_modules directory is not needed inside of it. Instead, node_modules will be recreated automatically when npm install is executed. To prevent the directory to be copied over, you will create a file named .dockerignore in the application directory.

`touch .dockerignore && echo "node_modules" >> .dockerignore`{{execute}}

Now push the new files to your app repository

`git add .`{{execute}}

`git commit -m "Adding Dockerfile"`{{execute}}

`git push`{{execute}}

## 3. Pipeline

Copy the `.gitlab-ci.yml` file to your application directory and push to GitLab. This file defines the pipeline.

`cp ~/assets/.gitlab-ci.yml .`{{execute}}

Open this file in the UI editor above for an explanation of the configuration. Remember to place "<YOU-USER-NAME>" with your GitLab username e.g. first-last-slalom

---

**Note**: Sometimes this file does not show in the editor. Run a `cat` command to print the contents of the file to the terminal.

`cat .gitlab-ci.yml`{{execute}}

---

The pipeline is a simple 1-stage execution that builds a Docker image and publishes it to the Docker registry. The `$CI_*` variables are built-in pre-defined variables provided by Gitlab. Here we are using a docker-in-docker (dind) image to run the pipeline which will allow us to build our application's Docker image.

```yaml
stages:
  - containerize
containerize:
  # Run the stage using a Docker image
  image: docker:19.03.12
  stage: containerize # must match the stage name declared above
  services:
    - docker:19.03.12-dind
  variables:
    # CI_REGISTRY_IMAGE would be resolved to the address of the registry tied to this project
    # CI_BUILD_ID is a unique build ID
    IMAGE_TAG: $CI_REGISTRY_IMAGE:1.0.$CI_BUILD_ID
  script:
    # Login to the registry.
    # Using the special CI_REGISTRY_USER variable: The user specified by this variable is created for you in order to push to the Registry connected to your project.
    # Its password is automatically set with the CI_REGISTRY_PASSWORD variable.
    # This allows you to automate building and deploying your Docker images and has read/write access to the Registry.
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    # Build the container image; tag it with registry/app:version
    - docker build -t $IMAGE_TAG .
    # Push the container image to the registry
    - docker push $IMAGE_TAG
    # Cleanup by removing the local image
    - docker image rm $IMAGE_TAG
```

---

Now push the pipeline to your app repository

`git add .`{{execute}}

`git commit -m "Configuring Pipeline"`{{execute}}

`git push`{{execute}}

Now that you have pushed your pipeline configuration to GitLab, you can view your pipeline configuration and status by
1. Navigate to the `projects` view https://gitlab.com/dashboard/projects and select the `My Nodejs App` project
1. On the left side, select `CI / CD` > `Pipelines`

Here you should see your pipeline running. You can click `status > 'containerize'` to navigate to the job where you can see console output.

Look for a `Job succeeded` message in the console output. The build may take a few minutes.

## 4. Container Registry

After it runs, verify your container image was published by
1. Navigate to the `projects` view https://gitlab.com/dashboard/projects and select the `My Nodejs App` project
1. On the left side, select `Packages & Registries` > `Container Registry`
