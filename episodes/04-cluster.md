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

The example Terraform scripts and Argo Workflow configuration are in https://github.com/cms-dpoa/cloud-processing/tree/main/standard-gke-cluster-gcs-imgdisk

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

Set the variables in the `terraform.tfvars` file for example to

```
project_id          = "<PROJECT_ID>"
region              = "europe-west4-a"
name                = "1"
gke_num_nodes       = 2
```

With these parameters, a cluster named `cluster-1` with 2 nodes will be created in the region `europe-west4-a`. You must define your GCP project.

To create the resources, run

```bash
terraform apply
```

and confirm "yes".

## Connect to the cluster and inspect

Once the cluster is created - it will take a while - connect to it with

```bash
gcloud container clusters get-credentials <CLUSTER_NAME> --region europe-west4-a --project <PROJECT_ID>
```

You can inspect the cluster with `kubectl` commands, for example the nodes: 


```bash
kubectl get nodes
```

and the namespaces:

```bash
kubectl get ns
```

You will see several namespaces, and most of them are used by Kubernetes for different services. We will be using the `argo` namespace.

For more information about `kubectl`, check the [Quick Reference](https://kubernetes.io/docs/reference/kubectl/quick-reference/) and the links therein, or use the `--help` option with any of the `kubectl` commands.

<!-- ## Enable image streaming

This is finally not needed. The streaming for the secondary disk works even if Image streaming shows Disabled in the cluster features

```bash
 gcloud container clusters update <CLUSTER_NAME> --zone europe-west4-a --enable-image-streaming

``` -->


## Costs

### Cluster management fee

For the GKE "Standard" cluster, there's a cluster management fee of 
$0.10 per hour.

### CPU and memory

The cost is determined by the machine and disk type and is per time. 
For this small example cluster with two e2-standard-4 nodes (4 vCPUs and 16 GB memory) the cost the cost is 0.3$ per hour. Each node has a 100 GB disk, and the cost is for two of these disks is 0.006$ per hour, i.e. very small compared to the machine cost.

The cluster usage contributes to the cost through data transfer and networking, but for this example case it is minimal. 

::::::::::::::::::::::::::::::::::::: keypoints 

- Kubernetes clusters can be created with Terraform scripts.
- `kubectl` is the tool to interact with the cluster.


::::::::::::::::::::::::::::::::::::::::::::::::

