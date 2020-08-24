# Application Repository, Docker Image registry, and Pipeline

## 1. App Repository

Push the starter Node Application to your GitLab CI repository

Copy the nodejs_app.tgz file to your working directory, untar it, and push the code to your application repository

`mkdir -p ~/workdir/app && cd ~/workdir/app && cp path/nodejs_app.tgz . `{{execute}}

`tar xvf nodejs_app.tgz && rm nodejs_app.tgz`{{execute}}

`cd nodejs_app`{{execute}}

`git init`{{execute}}

`git remote add origin https://gitlab.com/<YOUR-USER-NAME>/my-nodejs-app.git`{{copy}}
"<YOUR-USER-NAME>" will be the username you used to sign up for GitLab. For example, first-last-slalom

`git add .`{{execute}}

`git commit -m "Initial commit"`{{execute}}

`git push -u origin master`{{execute}}

This will push the starter application code to your repository

## 2. Docker Image Registry

With GitLab, your new project already comes with a container registry. To utilize this, we will define a Dockerfile which builds a Docker image for your Node application and in the next step create a pipeline that will publish images to this repository

Copy the `Dockerfile` to your application directory

`cp path/Dockerfile .`{{execute}}

Before you push to this file, verify that it build correctly

`docker build .`{{execute}}

Expect to see a successful build

To prevent the container from being too heavy, the node_modules directory is not needed inside of it. Instead, node_modules will be recreated automatically when npm install is executed. To prevent the directory to be copied over, create a file named .dockerignore in the application directory with the following as content.

`touch .dockerignore && echo "node_modules" >> .dockerignore`{{execute}}

Now push the new files to your app repository

`git add .`{{execute}}

`git commit -m "Adding Dockerfile"`{{execute}}

`git push`{{execute}}

## 3. Pipeline

Copy the `.gitlab-ci.yml` file to your application directory and push to GitLab. This file defines the pipeline.

`cp path/.gitlab-ci.yml .`{{execute}}

Open this file in the UI editor above for an explanation of the configuration. Remember to place "<YOU-USER-NAME>" with your GitLab username e.g. first-last-slalom

This pipeline currently only has one stage which is to build and publish the Docker image

Now push the pipeline to your app repository

`git add .`{{execute}}

`git commit -m "Configuring Pipeline"`{{execute}}

`git push`{{execute}}

Now that you have pushed your pipeline configuration to GitLab, you can view your pipeline configuration and status by
1. Navigate to the `projects` view https://gitlab.com/dashboard/projects and select the `My Nodejs App` project
1. On the left side, select `CI / CD` > `Pipelines`

Here you should see your pipeline, and can even trigger it manually to build and publish your Docker image

After it runs, verify your container image was published by
1. Navigate to the `projects` view https://gitlab.com/dashboard/projects and select the `My Nodejs App` project
1. On the left side, select `Packages & Registries` > `Container Registry`
