---
title: "Set up workflow"
teaching: 10
exercises: 5 
---

:::::::::::::::::::::::::::::::::::::: questions 

- How to set up Argo Workflow engine?
- How to submit a test job?
- Where to find the output?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Deploy Argo Workflows services to the cluster.
- Submit a test job.
- Find the output in your bucket.

::::::::::::::::::::::::::::::::::::::::::::::::


## Prerequisites


### GCP account and project

Make sure that you are in the GCP account and project that you intend to use for this work. In your Linux terminal, type

```bash
gcloud config list
```

The output shows your account and project. 

### Bucket

If you worked through [Section 02](episodes/02-storage), you have now a storage bucket for the output files.

List the buckets with

```bash
gcloud storage ls
```

### Code

In the [previous section](episodes/04-cluster), you pulled the [code](https://github.com/cms-dpoa/cloud-processing/tree/main/standard-gke-cluster-gcs-imgdisk) and moved to your local `cloud-processing/standard-gke-cluster-gcs-imgdisk` directory.

### Argo CLI installed?

You should have Argo CLI installed, see [Software setup](index.html#software-setup).



## Deploy Argo Workflows service

Deploy Argo Workflows services with

```bash
kubectl apply -n argo  -f https://github.com/argoproj/argo-workflows/releases/download/v3.5.10/install.yaml
kubectl apply -f argo/service_account.yaml
kubectl apply -f argo/argo_role.yaml
kubectl apply -f argo/argo_role_binding.yaml
```

Wait for the services to start. 

You should see the following:

```bash
$ kubectl get all -n argo
NAME                                       READY   STATUS    RESTARTS   AGE
pod/argo-server-5f7b589d6f-jkf4z           1/1     Running   0          24s
pod/workflow-controller-864c88655d-wsfr8   1/1     Running   0          24s

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/argo-server   ClusterIP   34.118.233.69   <none>        2746/TCP   25s

NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argo-server           1/1     1            1           24s
deployment.apps/workflow-controller   1/1     1            1           24s

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/argo-server-5f7b589d6f           1         1         1       24s
replicaset.apps/workflow-controller-864c88655d   1         1         1       24s
```

## About Argo Workflows

The data processing example is defined as an Argo workflow. You can learn about Argo Workflows in their [documentation](https://argo-workflows.readthedocs.io/en/latest/).

Every step in the workflow runs in a container, and there are several ways to pass the information between the steps.

The example configuration in `argo/argo_bucket_run.yaml` has comments to help you to understand how the files and/or parameters can be passed from a step to another.

## Submit a test job

The workflow is defined in the `argo/argo_bucket_run.yaml` file. It is composed of four steps:

- `get-metadata`: gets the number of files and the file list for a given CMS Open Data 2016 MiniAOD record
- `joblist`: divides the file list into given number of jobs and coumputes the number of events per job (if not all)
- `runpfnano`: runs the processing in parallel jobs
- `plot`: creates some simple plots.

Edit the parameters in the `argo/argo_bucket_run.yaml` so that they are

```
    parameters:
    - name: nEvents
      # Number of events in the dataset to be processed (-1 is all)
      value: 1000
    - name: recid
      # Record id of the dataset to be processed
      value: 30511
    - name: nJobs
      # Number of jobs the processing workflow should be split into
      value: 2
    - name: bucket
      # Name of cloud storage bucket for storing outputs
      value: <YOUR_BUCKET_NAME>
```

Now submit the workflow with

```bash
argo submit -n argo argo/argo_bucket_run.yaml
```

Observe its progress with

```bash
argo get -n argo @latest
```

Once done, check the ouput in the bucket with

```bash
$ gcloud storage ls gs://<YOUR_BUCKET_NAME>/**
gs://<YOUR_BUCKET_NAME>/pfnano/30511/files_30511.txt
gs://<YOUR_BUCKET_NAME>/pfnano/30511/logs/1.logs
gs://<YOUR_BUCKET_NAME>/pfnano/30511/logs/2.logs
gs://<YOUR_BUCKET_NAME>/pfnano/30511/plots/h_num_cands.png
gs://<YOUR_BUCKET_NAME>/pfnano/30511/plots/h_pdgid_cands.png
gs://<YOUR_BUCKET_NAME>/pfnano/30511/scatter/pfnanooutput1.root
gs://<YOUR_BUCKET_NAME>/pfnano/30511/scatter/pfnanooutput2.root
```

You can copy the files with `gcloud storage cp ...`.

### Delete resources

Delete the workflow after each run so that the "pods" do not accumulate. They are not running anymore but still visible.

```bash
argo delete -n argo @latest
```

Do not delete the cluster if you plan to continue to the [next section](episodes/06-scaleup), but do not keep it idle. The cost goes by the time it exists, not by the time it is in use. You can always create a new cluster.

You can delete all resources created by the Terraform script with

```bash
terraform destroy
```

Confirm with "yes".

## Costs

### Cluster

Running the workflow in the cluster does not increase the cost, so for this small test, the estimates in the [previous section](episodes/04-cluster#costs) are valid.

### Data download

As detailed in [Section 02](episodes/02-storage#networking-and-download), downloading data from the storage costs $0.12 / GB. The output file size - and consequently the download cost - for this quick example is small.









::::::::::::::::::::::::::::::::::::: keypoints 

- Once the cluster is up, you will first deploy the Argo Workflows services using `kubectl`.
- You will submit and monitor the workflow with `argo`.
- You can see the output in the bucket with `gcloud` commands or on Google Cloud Console Web UI.


::::::::::::::::::::::::::::::::::::::::::::::::

