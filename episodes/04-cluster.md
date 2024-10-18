---
title: "Kubernetes cluster"
teaching: 10
exercises: 5 
---

:::::::::::::::::::::::::::::::::::::: questions 

- How to create a Google Kubernetes Engine cluster?
- How to access the cluster from the command line?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Learn to create a Google Kubernetes Engine cluster.
- Access the cluster and inspect it from the command line.

::::::::::::::::::::::::::::::::::::::::::::::::


## Prerequisites


### GCP account and project

Make sure that you are in the GCP account and project that you intend to use for this work. In your Linux terminal, type

```bash
gcloud config list
```

The output shows your account and project. 


### Enabling services

Before you can create resources on GCP, you will need to enable them

In addition to what was enabled in the previous section, we will now enable Kubernetes Engine API (container.googleapis.com):

```bash
gcloud services enable container.googleapis.com
```

### Bucket

If you worked through Section 02, you have now a storage bucket for the output files.

List the buckets with

```bash
gcloud storage ls
```

### Secondary disk

If you worked through Section 03, you have a secondary boot disk image available

## Get the code

The example Terraform scripts and Argo Workflow configuration are in 

Get them with

```bash
git clone git@github.com:cms-dpoa/cloud-processing.git
cd cloud-processing/standard-gke-cluster-gcs-imgdisk
```
## About Terraform

The resources are created by [Terraform](https://developer.hashicorp.com/terraform?product_intent=terraform) scripts (with the file extension `.tf`) in the working directory.

In the example case, they are very simple and the same could be easily done with `gcloud` commands.

Terraform, however, makes it easy to change and keep track of the parameters. Read more about Terraform on Google Cloud in the [Terraform overview](https://cloud.google.com/docs/terraform/terraform-overview).

The configurable parameters are defined in `variables.tf` and can be modified in `terraform.tfvars`.

## Create the cluster

Set the variables in the `terraform.tfvars` files.

Run 

```bash
terraform apply
```

and confirm "yes".

## Connect to the cluster and inspect

```bash
gcloud container clusters get-credentials <CLUSTER_NAME> --region europe-west4-a --project <PROJECT_ID>
```

```bash
kubectl get nodes
```

```bash
kubectl get ns
```

<!-- ## Enable image streaming

This is finally not needed. The streaming for the secondary disk works even if Image streaming shows Disabled in the cluster features

```bash
 gcloud container clusters update <CLUSTER_NAME> --zone europe-west4-a --enable-image-streaming

``` -->


## Costs






::::::::::::::::::::::::::::::::::::: keypoints 

- Kubernetes clusters can be created with Terraform scripts.
- kubectl is the tool to interact with the cluster.


::::::::::::::::::::::::::::::::::::::::::::::::

