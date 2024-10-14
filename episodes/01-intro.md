---
title: "About Kubernetes"
teaching: 10
exercises: 5 
---

:::::::::::::::::::::::::::::::::::::: questions 

- What is Kubernetes?
- What are public cloud provides?
- Why would you use them?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain the motivation for using Kubernetes cluster from public cloud providers
- List the information sources

::::::::::::::::::::::::::::::::::::::::::::::::

## Introduction

This tutorial shows how to set up a CMS open data processing workflow using Kubernetes clusters from Google Cloud Platform (GCP).

::::::::::::::::::::::::::::::::::::: callout

To learn about CMS open data and the different data formats, work through the tutorials in one of our [workshops](https://cms-opendata-guide.web.cern.ch/cmsOpenData/workshops/).

::::::::::::::::::::::::::::::::::::::::::::::::


This is an option for you if you do not have enough computing resources and want to run some heavy processing. In this tutorial, we use as an [example processing](https://opendata.cern.ch/record/12504) of CMS open data MiniAOD to a "custom" NanoAOD, including more information than the standard NanoAOD but still in the same flat file format. 

We expect that you would want to download the output files to your local area and analyse the with your owm resources. Note that analysis using GCP resources with your files stored on GCP is also possible, but is not covered in this tutorial.


## Google Cloud Platform

Public cloud providers are companies that offers computing resources and services over the internet to multiple users or organizations. Google Cloud Platform (GCP) is one of them. You define the resources that you need and pay for what you use. As many other such resource providers (for example AWS, Azure, OHV), it offers some free getting-started "credits".

This tutorial was set up using [Google Cloud Research credits](https://cloud.google.com/edu/researchers). You can apply for similar credits for your research projets. Take note that the credit has to be used within 12 months.

You can create, manage and delete resources using the Google Cloud Console (a Web UI) or a command-line tool `gcloud`. In this tutorial, we use the command-line tool or Terraform scripts for most of the operations.

In this tutorial, we use `gcloud` commands to create the persistent storage for the output data, and a Terraform script to provision the Kubernetes cluster where the processing workflow will run.

## Terraform

[Terraform](https://www.terraform.io/) is a tool to define, provision, and manage cloud infrastructure using configuration scripts. 

In this tutorial, we use Terraform scripts to create the Kubernetes cluster in a single step. After the workflow finishes, it is easy to delete the resources similary, in a single step.

## Kubernetes

Kubernetes is a system to managed containerized workflows on computing clusters. `kubectl` is the command-line tool to interact with the cluster resources.

In this tutorial, we use `kubectl` commands to set up some services and to observe the status of the cluster and the data processing workflow. 

## Argo Workflows

The processing workflow consist of some sequential and parallel steps. We use [Argo Workflows](https://argoproj.github.io/workflows/) to define (or "orchestrate") the workflow steps.

In this tutorial, the Argo Workflows services are set up using a `kubectl` command. We use `argo`, the command-line tool, to submit and manage the workflows.


::::::::::::::::::::::::::::::::::::: keypoints 

- Public cloud providers are companies that offer pay-as-you-go computing resources and services over the internet to multiple users or organizations.
- Terraform is an open-source tool to provision and delete computing infrastructure.
- Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications and their associated workflows across clusters of hosts..
- Argo Workflows is an open-source tool for orchestrating sequentails and parallel jobs on Kubernetes.


::::::::::::::::::::::::::::::::::::::::::::::::

