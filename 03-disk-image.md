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

We will follow the [instructions](https://cloud.google.com/kubernetes-engine/docs/how-to/data-container-image-preloading#prepare) provided by GCP to build the secondary disk image. All necessary steps are provided in this lesson.


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

### Go installed?

You should have `go` installed, see [Software setup](index.html#software-setup).

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

Often, the resources need to work with each other. In this case, the Cloud Build service needs to have two additional roles to access Compute resources (the image disk belongs to that category).

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
go run ./cli --project-name=<PROJECT_ID> --image-name=pfnano-disk-image --zone=europe-west4-a --gcs-path=gs://<BUCKET_FOR_LOGS> --disk-size-gb=50 --container-image=docker.io/cernopendata/cernopendata-client:latest --container-image=docker.io/rootproject/root:latest  --container-image=ghcr.io/cms-dpoa/pfnano-image-build:main --timeout 100m
```

:::::::::::::::::::::::::::::::::::::::::: callout

Note that while images can in most cases be "pulled" from Dockerhub  specifying only the image name (e.g. `cernopendata/cernopendata-client` and `rootproject/root`), in this script you must give the full registry address starting with `docker.io` **and** specify a tag (i.e. `:latest`).

::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::: spoiler

### Error: Quota exceeded?

Failure with errors of type

```
Code: QUOTA_EXCEEDED
Message: Quota 'N2_CPUS' exceeded.
```

are due to requested machine type no being available in the requested zone. Nothing to do with you quota.

Try in a different region or with a different machine type. You can give them as parameters  e.g. `--zone=europe-west4-a --machine-type=e2-standard-4`.
Independent of the zone specified in parameters, the disk image will have `eu` as the location, so any zone in `europe` is OK (if you plan to create your cluster in a zone in `europe`).


Note that the bucket for logs has to be in the same region so you might need to create another one. Remove the old one with `gcloud storage rm -r gs://<BUCKET_FOR_LOGS>`.

::::::::::::::::::::::::::::::::::::::::::::::::

Once the image is built, you can see it in the list of available images with

```bash
gcloud compute images list
```

That's a long list, there are many images already available. Your new image has your project name under "PROJECT" and `secondary-disk-image` under "FAMILY".

## Costs

### Computing

The script runs a Google Cloud Build process and there's per-minute small [cost](https://cloud.google.com/build/pricing). 120 minutes are included in the [Free tier services](https://cloud.google.com/free/docs/free-cloud-features#free-tier-usage-limits). 

### Storage

The image is stored in Google Compute Engine image storage, and the cost is computed by the archive size of the image. The cost is very low: in our example case, the size is 12.25 GB, and the monthly cost is $0.05/GB. 

There's a minimal cost for the output logs GCS bucket. The bucket can be deleted after the build.


::::::::::::::::::::::::::::::::::::: keypoints 

- A secondary boot disk with the container image preloaded can speed up the workflow start.


::::::::::::::::::::::::::::::::::::::::::::::::

