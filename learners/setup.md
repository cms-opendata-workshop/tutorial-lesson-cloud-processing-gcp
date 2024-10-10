---
title: Setup
---


## Google Cloud account

[Create an account](https://accounts.google.com/) for Google Cloud Platform (GCP). The login is an e-mail address: it can be your existing gmail account, but you can create an account with another e-mail address.

If you want to use the [free trial](https://cloud.google.com/free= resources) ($300), you need to activate them. A billing account with a credit card number is needed. You will not be charged during the free trial.

## Software setup

In this tutorial, we expect that you will be using a Linux shell on native Linux, macOS or through WSL2 on Windows. If you do not have any of them available, you can work through [Google Cloud Shell](https://cloud.google.com/shell/docs) but the instructions in this tutorial are for the Linux shell.

You should be familiar with basic Linux shell commands and have `git` available.

### GCP Command-line interface: gcloud

Follow [these instructions](https://cloud.google.com/sdk/docs/install) to install glcoud, the command-line interface to GCP resources.

Initialize it with

```bash
gcloud init
```

List your project(s) with

```bash
gcloud projects list
```

Check the current project with

```bash
gcloud config list
```

Change the project if needed with:

```bash
gcloud config set project <PROJECT_ID>
```

### Kubernetes command-line tool: kubectl

`kubectl` is the tool to interact with the Kubernetes clusters. Install it either [on its own](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management) or with [Docker Desktop](https://www.docker.com/products/docker-desktop/).

### Deploying resources: Terraform

This tutorial uses Terraform scripts to facilitate the creation and deletion of GCP resources. Install it following [these instructions](https://developer.hashicorp.com/terraform/install) (WSL2 users should follow the Linux instructions).

### Optional for building a customized CMSSW container image: Docker

This tutorial uses a CMSSW open data container image with the [pfnano producer code](https://opendata.cern.ch/record/12504) downloaded and compiled. You do not need to install Docker to use it in the context of this tutorial. You need Docker, if you want to modify the code with your selections and build a new container image. 

### Optional for building an image disk: go

A secondary boot disk image with the CMSSW container image preinstalled makes the processing workflow step start immediately. Otherwise, the image needs to be pulled at the start of the processing step and done for each cluster node separately. 

This disk image can be created and stored using GCP resources. A `go` script is [available](https://github.com/GoogleCloudPlatform/ai-on-gke/tree/main/tools/gke-disk-image-builder) for creating the image. To run it, install `go` following [these instructions](https://go.dev/doc/install) (WSL2 users should follow the Linux instructions).



