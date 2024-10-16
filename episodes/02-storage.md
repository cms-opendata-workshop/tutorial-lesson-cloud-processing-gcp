---
title: "Persistent storage"
teaching: 10
exercises: 5 
---

:::::::::::::::::::::::::::::::::::::: questions 

- How to create a Google Cloud Storage bucket?
- What are the basic operations?
- What are the cost of storage and download?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Learn to create a Google Cloud Storage bucket
- Learn to list the contents of a bukcet
- Understand the persistant storage costs.

::::::::::::::::::::::::::::::::::::::::::::::::

## Storage for the output files

The processing workflow writes the output files to a storage from which they can be downloaded afterwards. 

For this tutorial, we use a [Google Cloud Storage (GCS) bucket](https://cloud.google.com/storage/docs/buckets). 

::::::::::::::::::::::::::::::::::::: callout

The storage is created separately from the cluster resources. You can then delete the cluster just after the processing and avoid unnecessary costs, but keep the output files.

::::::::::::::::::::::::::::::::::::::::::::::::

## Prerequisites

### GCP account and project

Make sure that you are in the GCP account and project that you intend to use for this work. In your Linux terminal, type

```bash
gcloud config list
```

The output shows your account and project. 

:::::::::::::::::::::::::::::::::::::::::: spoiler

### How to change them?

If they are not what you expect (in case you have many), change them with

```bask
gcloud config set account <ACCOUNT>
```

and

```bash
gcloud config set project <PROJECT_ID>
```

#### Accounts

You can can check the credentialed accounts with

```bash
gcloud auth list
```

If you get none or your account does not appear in the list, run 

```bash
gcloud init
```

and follow the steps in the output.

#### Projects

List the projects within the active account with

```bash
gcloud projects list
```

::::::::::::::::::::::::::::::::::::::::::::::::

### Billing account?

If this is your first project or you created it from the Google Cloud Console Web UI, it will have a billing account linked to it.

If you created the project from the command line without specifying the billing account, you must link it to an existing billing account.

:::::::::::::::::::::::::::::::::::::::::: spoiler

### How to link a project to your billing account?

First list the billing accounts

```bash
gcloud billing accounts list
```

Take the account id from the output, and check if your project is linked to it

```bash
gcloud billing projects list --billing-account <ACCOUNT_ID>
```

If not, link your project to this account with

```bash
gcloud billing projects link <PROJECT_ID> --billing-account <ACCOUNT_ID>
```

::::::::::::::::::::::::::::::::::::::::::::::::

### Choose your region

In this tutorial, we will use the Google computing centre `europe-west4` located in Netherlands. You can use another one, but use it consistently.

If you computing resources are in a different region than your storage, additional cost and delay may occur.

## Create the bucket

Create a storage bucket with

```bash
gcloud storage buckets create gs://<BUCKET_NAME> --location europe-west4
```

You can test copying a file to it with

```bash
echo test > test.txt
gcloud storage cp test.txt gs://<BUCKET_NAME>/
```

and list the contents of the bucket with

```bash
gcloud storage ls gs://<BUCKET_NAME>/*
```

You can remove the file with

```bash
gcloud storage rm gs://<BUCKET_NAME>/test.txt
```

Note that the bucket is tied to your GCP project and you can only access it when authenticated.


## Costs

### Storage

The [storage cost](https://cloud.google.com/storage/pricing) of a GCS bucket depends on the data volume. The costs may vary from a region to another. For `europe-west4`, the current (October 2024) monthly cost is $0.020 / GB.

### Operations

An [operation](https://cloud.google.com/storage/pricing#operations-pricing) is an action that makes changes to or retrieves information from a bucket and it has a tiny cost: $0.005 per 1000 operations.
This applies, for example, to listing the contents of the bucket on your terminal. The traffic between GCP computing resources and storage within the same region (e.g. in `europe-west4`) is free.

In the context of this tutorial, the operations costs are insignificant. However, keep this is mind if you plan to write scripts that list the bucket content from your terminal.

### Networking and download

[Downloading data](https://cloud.google.com/storage/pricing#network-egress) from the GCS bucket to your computer has a significant cost: the current (October 2024) cost to internet locations (excluding China and Austalia) is $0.12 / GB.

In the context of the example of this tutorial, the resulting output files are approximately 30% of the original MiniAOD dataset volume. For example, downloading the 330 GB ouput from a processing of a 1.1 TB MiniAOD dataset will cost $40.


::::::::::::::::::::::::::::::::::::: keypoints 

- Google Cloud Storage bucket can be used to store the output files.
- The storage cost depends on the volume stored and for this type of processing is very small. 
- The download of big output files from the bucket can be costly.


::::::::::::::::::::::::::::::::::::::::::::::::

