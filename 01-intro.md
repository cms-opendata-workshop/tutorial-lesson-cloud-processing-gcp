---
title: "Introduction"
teaching: 10
exercises: 5 
---

:::::::::::::::::::::::::::::::::::::: questions 

- What are public cloud provides?
- Why would you use them?
- What is Kubernetes?
- What else do you need?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain the motivation for using Kubernetes cluster from public cloud providers.
- Explain the tools used to set up the Kubernetes cluster and run the processing workflow.

::::::::::::::::::::::::::::::::::::::::::::::::

## Introduction

This tutorial shows how to set up a CMS open data processing workflow using Kubernetes clusters from Google Cloud Platform (GCP).

::::::::::::::::::::::::::::::::::::: callout

To learn about CMS open data and the different data formats, work through the tutorials in one of our [workshops](https://cms-opendata-guide.web.cern.ch/cmsOpenData/workshops/).

::::::::::::::::::::::::::::::::::::::::::::::::


Using public cloud resources is an option for you if you do not have enough computing resources and want to run some heavy processing. In this tutorial, we use as an [example processing](https://opendata.cern.ch/record/12504) of CMS open data MiniAOD to a "custom" NanoAOD, including more information than the standard NanoAOD but still in the same flat file format. 

We assume that you would want to download the output files to your local area and analyse them with your own resources. Note that analysis using GCP resources with your files stored on GCP is also possible, but is not covered in this tutorial.


## Google Cloud Platform

Public cloud providers are companies that offers computing resources and services over the internet to multiple users or organizations. Google Cloud Platform (GCP) is one of them. You define and deploy the resources that you need and pay for what you use. As many other such resource providers (for example AWS, Azure, OHV), it offers some free getting-started "credits".

::::::::::::::::::::::::::::::::::::: callout

GCP offers [free trial](https://cloud.google.com/free/docs/free-cloud-features#free-trial) credits for $300 for a new account. This credit is valid  for 90 days.

This tutorial was set up using [Google Cloud Research credits](https://cloud.google.com/edu/researchers). You can apply for similar credits for your research projects. Take note that the credit has to be used within 12 months.

::::::::::::::::::::::::::::::::::::::::::::::::


You can create, manage and delete resources using the Google Cloud Console (a Web UI) or a command-line tool `gcloud`. 

In this tutorial, we use `gcloud` commands to create the persistent storage for the output data, and a Terraform script to provision the Kubernetes cluster where the processing workflow will run.

## Terraform

[Terraform](https://www.terraform.io/) is a tool to define, provision, and manage cloud infrastructure using configuration scripts. 

In this tutorial, we use Terraform scripts to create the Kubernetes cluster in a single step. The advantage - compared to plain command-line `gcloud` commands - is that we can easily configure input variables. Also, after the workflow finishes, it is easy to delete the resources in a single step.

## Kubernetes

Kubernetes is a system to managed containerized workflows on computing clusters. `kubectl` is the command-line tool to interact with the cluster resources.

In this tutorial, we use `kubectl` commands to set up some services and to observe the status of the cluster and the data processing workflow. 

## Argo Workflows

The processing workflow consist of some sequential and parallel steps. We use [Argo Workflows](https://argoproj.github.io/workflows/) to define (or "orchestrate") the workflow steps.

In this tutorial, the Argo Workflows services are set up using a `kubectl` command. We use `argo`, the command-line tool, to submit and manage the workflows.


## Ready to go?

:::::::::::::::::: checklist

Check the instructions in [Software setup](index.html#software-setup)

- [ ] a GCP account and a GCP project
- [ ] Linux shell available (Linux, macOS or WSL2 on Windows)
- [ ] gcloud installed
- [ ] kubectl installed
- [ ] Terraform installed
- [ ] go installed
- [ ] Argo CLI installed

When done, let's go!

::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::: spoiler

### Too much to install? I just want a quick try...

If you don't have access to a Linux terminal or prefer not to install tools locally, you can use Google Cloud Shell. You'll need a Google Cloud Platform (GCP) account and a GCP project. To open Cloud Shell, click the Cloud Shell icon in the top-right corner of the Google Cloud Console.

Cloud Shell comes pre-installed with gcloud, kubectl, terraform, and go. However, you'll need to install the Argo CLI manually.

Remember that while using Cloud Shell is free, you will need to have credits (or pay) for the resources you deploy.

::::::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: keypoints 

- Public cloud providers are companies that offer pay-as-you-go computing resources and services over the internet to multiple users or organizations.
- Terraform is an open-source tool to provision and delete computing infrastructure.
- Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications and their associated workflows across clusters of hosts..
- Argo Workflows is an open-source tool for orchestrating sequential and parallel jobs on Kubernetes.


::::::::::::::::::::::::::::::::::::::::::::::::

