---
title: "Discussion"
teaching: 10
exercises: 0
---

:::::::::::::::::::::::::::::::::::::: questions 

- What was the user experience?
- What worked well?
- What difficulties were observed?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Share the practical user experience with CMS open data and GCP while preparing for this tutorial.

::::::::::::::::::::::::::::::::::::::::::::::::

## What works well?

### General setup

Implementing the processing workflow in the cloud environment was easy thanks to the availability of the CMSSW, CERN Open data client and ROOT software containers. Argo Workflows, being native for cloud environments, is well-adapted for the workflow definition on Kubernetes resources.

### Google Cloud Platform and Google Kubernetes Engine

From the technical point of view, running the workflow was smooth. It was our choice to work mainly with the command-line tool (`gcloud`) to achieve a longer lifetime of validity compared to instructions through a graphical UI. The provider-specific part of the Terraform resource provisioning scripts were reasonably well documented.

The resources were relatively stable, and we observed only few cluster-related failures during all the benchmarking process, which consisted of several processing runs over an entire dataset and numerous shorter runs.

Building the image disk with the large software container pre-installed worked fine using tools in a GitHub repository linked in the Google Cloud documentation.

Features such as autoscaling helps reduce costs and are easy to setup.

Further savings could be achieved with "spot" resources, i.e. selecting cheaper nodes that can get deleted without notice. That would require some further work for bookkeeping so that the failed jobs on deleted nodes could easily be restarted.


### Data access

Data access through XRootD (streaming from the CERN open data eos area) was relatively stable. However, some failures were observed during the long runs, but diminished considerably after an update of resources on the CERN Open data eos file system side.

It should be noted that we requested as much CPU for the jobs as they would need when running non-parallel. That was basically on job per vCPU. Pushing more jobs on one vCPU can easily make jobs fail as they starting compete for resources, and data access can fail due to timeout.

## What difficulties were observed?

### Google Cloud Platform

It is useful to acknowledge that the testing was done on a Google account with a CERN email that had been used for CERN-related work earlier, and with a 5000 USD worth total credit available.
When necessary, quota requests to increase the number of nodes and to use more performant machine types were immediately approved.

The final testing for these instructions were done with a fresh Google account connected to an institute email. We observed that the available quota, for example, for number of nodes or vCPUs was very low, and the quota increase request was not possible through the submission form that was available earlier. We got no reply (or wrong replies) from the Google support despite several inquiries, and were not able to increase the quota close to any useful value for a large-scale (i.e. full dataset) processing.

This leaves us very perplexed about the possibility of using Google Cloud Platform for small-scale processing by individual researchers. While we observed that - technically - deploying the resources, setting up the workflow and running the processing was very smooth, the usability might be limited by the lack of support as quota increase requires human intervention from the Google support staff. The goal of this work was to show how cloud resources can be used for a short-term need of powerful resources with a moderate cost, but this does not seem to be in the interest of Google as a service provider.

If there is interest, we can repeat this exercise with other cloud providers.

### Data access

While rare, data access timeouts occur. As a test case, we processed a full dataset with 352 files and after the server-side update, most jobs ran through without failures. However, some datasets are larger and a proper bookkeeping should be added for the failed jobs. 

::::::::::::::::::::::::::::::::::::: keypoints 

- Technically, deploying the resources, setting up the workflow and running the processing on Google Cloud Platform was very smooth.
- Final testing on a new Google Cloud account revealed problems with resource quota increase requests, i.e. the willingness of Google support to help small customers.


::::::::::::::::::::::::::::::::::::::::::::::::
