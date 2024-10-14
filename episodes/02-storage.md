---
title: "Persistent storage"
teaching: 10
exercises: 5 
---

:::::::::::::::::::::::::::::::::::::: questions 

- How to create a Google Cloud Storage bucket?
- Why it done separately from the cluster?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Learn to create a Google Cloud Storage bucket
- Understand the persistant storage costs.

::::::::::::::::::::::::::::::::::::::::::::::::

## Storage for the output files

The processing workflow writes the output files to a storage from which they can be downloaded afterwards. 

For this tutorial, we use a Google Cloud Storage (GCS) bucket. 

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

If you created the project from the command line without specifying the billing account, you can link it to an existing account.

First list the billing accounts

```bash
gcloud billing accounts list
```

Take the account id from the output, and check if your project is linked to it

```bash
gcloud billing projects list --billing-account <ACCOUNT_ID>
```

If not, link your project to this account with
gcloud billing projects link <PROJECT_ID> --billing-account <ACCOUNT_ID>
```bash

```

### Enabling services

### Decide your region



::::::::::::::::::::::::::::::::::::: keypoints 

- Google Cloud Storage bucket can be used to store the output files.
- The storage cost depends on the volume stored.
- The download 


::::::::::::::::::::::::::::::::::::::::::::::::

