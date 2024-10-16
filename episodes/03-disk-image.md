---
title: "Disk image"
teaching: 10
exercises: 5 
---

:::::::::::::::::::::::::::::::::::::: questions 

- Why to build a disk image for cluster nodes?
- How to build a disk image?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Create a disk image with the container images.

::::::::::::::::::::::::::::::::::::::::::::::::

## Why a disk image?


The container image for the MiniAOD processing is very big and it needs to be pulled to the nodes of the cluster. It can take 30 mins to pull it.
Making it available to the nodes of the cluster through a pre-built secondary disk image speeds up the workflow start.

A script is available but building the disk image does not work for a new GCP account for the moment.

Investigating....


## Prerequisites

### GCP account and project

Make sure that you are in the GCP account and project that you intend to use for this work. In your Linux terminal, type

```bash
gcloud config list
```

The output shows your account and project. 

### Bucket for logs

To build an image disk, we will use a script that produces some logs.
We create a bucket for these logs with

```
gcloud storage buckets create gs://<BUCKET_FOR_LOGS>/ --location europe-west4
```


### Enabling services

Before you can create resources on GCP, you will need to enable the corresponding services.

Your project usually has several services enabled already. You can list them with

```bash
gcloud services list --enabled
```

You can list all available services with

```bash
gcloud services list --available
```

but that's a long list!

To build a disk image, you will need to [enable](https://cloud.google.com/endpoints/docs/openapi/enable-api#enabling_an_api) Cloud Build API (cloudbuild.googleapis.com) and Compute Engine API (compute.googleapis.com) with

```bash
gcloud services enable cloudbuild.googleapis.com compute.googleapis.com
```

When services are enabled, some "service accounts" with specific roles get created. You can list them with

```bash
gcloud projects get-iam-policy <PROJECT_ID>
```

Often, the resources need to work with each other. In this case, the Cloud Build service needs to have two additional roles to access Compute resources (the image disk belongs to that category)

Add them with

```bash
gcloud projects add-iam-policy-binding <PROJECT_ID> --member serviceAccount:<PROJECT_NR>@cloudbuild.gserviceaccount.com --role roles/compute.serviceAgent
```

```bash
gcloud projects add-iam-policy-binding <PROJECT_ID> --member serviceAccount:<PROJECT_NR>@cloudbuild.gserviceaccount.com --role roles/compute.admin
```

### Application login

You need to create a credential file to run the script:

```bash
gcloud auth application-default login
```

and authenticate in the browser window that opens.

## Get the code

Pull the code from https://github.com/GoogleCloudPlatform/ai-on-gke/tree/main/tools/gke-disk-image-builder

If you want only this directory and not the full repository, use the sparse checkout:

```
git init <your_new_folder>
cd <your_new_folder>
git remote add -f origin git@github.com:GoogleCloudPlatform/ai-on-gke.git
```

This takes a while.

```
git config core.sparseCheckout true
echo "tools/gke-disk-image-builder/" >>  .git/info/sparse-checkout
git pull origin main
cd tools/gke-disk-image-builder/
```

## Create the image

To create the image with the script, you must have `go` installed.

Run the script with

```bash
go run ./cli --project-name=<PROJECT_ID> --image-name=pfnano-disk-image --zone=europe-west4-a --gcs-path=gs://<BUCKET_FOR_LOGS> --disk-size-gb=50 --container-image=ghcr.io/katilp/pfnano-image-build:main --timeout 100m
```

:::::::::::::::::::::::::::::::::::::::::: spoiler

### Error: Quota exceeded?

Failure with errors of type

```
Code: QUOTA_EXCEEDED
Message: Quota 'N2_CPUS' exceeded.
```

are due to requested machine type no being available in the requested zone. Nothing to do with you quota.

Try in a different region or with a different machine type. You can give them as parameters  e.g. `--zone=europe-west4-a --machine-type=e2-standard-4`

Note that the bucket for logs has to be in the same region so you might need to create another one. Remove the old one with `gcloud storage rm -r gs://<BUCKET_FOR_LOGS>`.

::::::::::::::::::::::::::::::::::::::::::::::::


## Costs






::::::::::::::::::::::::::::::::::::: keypoints 

- A secondary boot disk with the container image preloaded can speed up the workflow start.


::::::::::::::::::::::::::::::::::::::::::::::::

