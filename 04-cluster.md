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

## Create the cluster

Set the variable in the `terraform.tfvars` files.

Run 

```bash
terraform apply
```

and confirm "yes".

## Connect to the cluster and inspect

```bash
gcloud container clusters get-credentials cluster-2 --region europe-we
st4-a --project hip-new-full-account
```

```bash
kubectl get nodes
```

```bash
kubectl get ns
```

## Enable image streaming

```bash
 gcloud container clusters update cluster-2 --zone europe-west4-a --ena
ble-image-streaming

```


## Costs






::::::::::::::::::::::::::::::::::::: keypoints 

- Kubernetes clusters can be created with Terraform scripts.
- kubectl is the tool to interact with the cluster.


::::::::::::::::::::::::::::::::::::::::::::::::

