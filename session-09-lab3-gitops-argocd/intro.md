# What is GitOps?
GitOps is a way to do Kubernetes cluster management and application delivery.  It works by using Git as a single source of truth for declarative infrastructure and applications. With GitOps, the use of software agents can alert on any divergence between Git with what's running in a cluster, and if there's a difference, Kubernetes reconcilers automatically update or rollback the cluster depending on the case. With Git at the center of your delivery pipelines, developers use familiar tools to make pull requests to accelerate and simplify both application deployments and operations tasks to Kubernetes.

# ArgoCD

Application definitions, configurations, and environments should be declarative and version controlled. Application deployment and lifecycle management should be automated, auditable, and easy to understand. Argo CD is a declarative, GitOps continuous delivery tool for Kubernetes that utilizes these principles.

# Prerequisites

**This lab will build on components and concepts from Lab 2. Please complete Lab 2 GitOps Pipelines before starting this lab**

This tutorial could be done in free tier of each service.

- GitLab CI for hosting 2 public repos - app repository, k8s config repository
- GitLab CI for building and pushing docker images
- GitLab CI for hosting an image repoitory
- Katacoda for using configured k8s

# Lab Contents

This lab will expand on GitOps pipelines from the previous lab by setting up Argo CD to replace Flux CD to handle your continuos delivery system.

We will build the following components
1. An app repository with a simple NodeJS API hosted in GitLab CI
1. A GitLab CI job that watches for commits to the app repository
1. App commits are auto-built and the generated Docker image is published to an image repository hosted in GitLab CI
1. Argo CD will monitor a config repository to sync deployments of the k8s config files
1. Argo CD will also monitor GitLab CI for new app images and auto-deploy the images to the k8s cluster
