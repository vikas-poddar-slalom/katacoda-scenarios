# Application Repository, Docker Image registry, and Pipeline

## 1. App Repository

Push the starter Node Application to your GitLab CI repository

Copy the nodejs_app.tgz file to your working directory, untar it, and push the code to your application repository

`mkdir -p ~/workdir/tmp && cd ~/workdir/tmp && cp ~/assets/nodejs_app.tgz . `{{execute}}

`tar xvf nodejs_app.tgz && mv nodejs_app ~/workdir/app && cd ~/workdir && rm -rf tmp`{{execute}}

`cd ~/workdir/app`{{execute}}

First set these variables to aid in configuring your local to connect with remote.

`YOUR-USER-NAME` should be your GitLab username e.g. first-last-slalom.

`export GLUSER="YOUR-USER-NAME"`{{copy}}

`export GLEMAIL="YOUR-EMAIL"`{{copy}}

`export GLNAME="Your Name"`{{copy}}

`git init`{{execute}}

`git config --global user.email "${GLEMAIL}"`{{execute}}

`git config --global user.name "${GLNAME}"`{{execute}}

`git remote add origin https://gitlab.com/${GLUSER}/my-nodejs-app.git`{{execute}}

`git add .`{{execute}}

`git commit -m "Initial commit"`{{execute}}

`git push -u origin master`{{execute}}

This will push the starter application code to your repository

## 2. Docker Image Registry

With GitLab, your new project already comes with a container registry. To utilize this, we will define a Dockerfile which builds a Docker image for your Node application and in the next step create a pipeline that will publish images to this repository

Copy the `Dockerfile` to your application directory

`cp ~/assets/Dockerfile .`{{execute}}

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

`cp ~/assets/.gitlab-ci.yml .`{{execute}}

Open this file in the UI editor above for an explanation of the configuration. Remember to place "<YOU-USER-NAME>" with your GitLab username e.g. first-last-slalom

---

**Note**: Sometimes this file does not show in the editor. Run the below command to replace YOUR-USER-NAME with the correct value if that happens

This step is optional if you run into this problem: `sed -i "s/YOUR-USER-NAME/${GLUSER}/g" .gitlab-ci.yml`{{execute}}

Use `cat .gitlab-ci.yml`{{execute}} to verify the contents of the file are correct

---

This pipeline currently only has one stage which is to build and publish the Docker image

Now push the pipeline to your app repository

`git add .`{{execute}}

`git commit -m "Configuring Pipeline"`{{execute}}

`git push`{{execute}}

Now that you have pushed your pipeline configuration to GitLab, you can view your pipeline configuration and status by
1. Navigate to the `projects` view https://gitlab.com/dashboard/projects and select the `My Nodejs App` project
1. On the left side, select `CI / CD` > `Pipelines`

Here you should see your pipeline running

## 4. Container Registry

After it runs, verify your container image was published by
1. Navigate to the `projects` view https://gitlab.com/dashboard/projects and select the `My Nodejs App` project
1. On the left side, select `Packages & Registries` > `Container Registry`
