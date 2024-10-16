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


## Prerequisites

### GCP account and project

Make sure that you are in the GCP account and project that you intend to use for this work. In your Linux terminal, type

```bash
gcloud config list
```

The output shows your account and project. 



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

To build a disk image, you will need to [enable](https://cloud.google.com/endpoints/docs/openapi/enable-api#enabling_an_api) Compute Engine API (compute.googleapis.com) with

```bash
gcloud services enable compute.googleapis.com
```


## Create the disk

```bash
$ gcloud compute disks create pfnano-disk --zone=europe-west4-c --size=100GB --type=pd-standard
WARNING: You have selected a disk size of under [200GB]. This may result in poor I/O performance. For more information, see: https://developers.google.com/compute/docs/disks#performance.
Created [https://www.googleapis.com/compute/v1/projects/<PROJECT_ID>/zones/europe-west4-c/disks/pfnano-disk].
NAME               ZONE            SIZE_GB  TYPE         STATUS
pfnano-disk        europe-west4-c  100      pd-standard  READY

New disks are unformatted. You must format and mount a disk before it
can be used. You can find instructions on how to do this at:

https://cloud.google.com/compute/docs/disks/add-persistent-disk#formatting
```

## Create a VM

```bash
$ gcloud compute instances create vm-work --zone=europe-west4-c --machine-type=e2-standard-4 --disk=name=pfnano-disk --boot-disk-size=100GB --image-family=cos-113-lts --image-project=cos-cloud
WARNING: You have selected a disk size of under [200GB]. This may result in poor I/O performance. For more information, see: https://developers.google.com/compute/docs/disks#performance.
Created [https://www.googleapis.com/compute/v1/projects/<PROJECT_ID>/zones/europe-west4-c/instances/vm-work].
WARNING: Some requests generated warnings:
 - Disk size: '100 GB' is larger than image size: '10 GB'. You might need to resize the root repartition manually if the operating system does not support automatic resizing. See https://cloud.google.com/compute/docs/disks/add-persistent-disk#resize_pd for details.

NAME     ZONE            MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
vm-work  europe-west4-c  e2-standard-4               10.164.0.2   35.204.202.73  RUNNING
```

Login to this VM

```
$ gcloud compute ssh vm-work --zone=europe-west4-c
Updating project ssh metadata...⠶Updated [https://www.googleapis.com/compute/v1/projects/<PROJECT_ID>].
Updating project ssh metadata...done.
Waiting for SSH key to propagate.
Warning: Permanently added 'compute.5401844593939611330' (ED25519) to the list of known hosts.
kati@vm-work ~ $ 
```

Check the logical links that indicate the disk address

```
$ ls -l /dev/disk/by-id/google-*
lrwxrwxrwx 1 root root  9 Oct 16 13:31 /dev/disk/by-id/google-persistent-disk-0 -> ../../sda
lrwxrwxrwx 1 root root 10 Oct 16 13:31 /dev/disk/by-id/google-persistent-disk-0-part1 -> ../../sda1
[...]
google-persistent-disk-0-part9 -> ../../sda9
lrwxrwxrwx 1 root root  9 Oct 16 13:31 /dev/disk/by-id/google-persistent-disk-1 -> ../../sdb
```

It is the one without partitioning, in this `sdb`. Note that it can be other way round!

Format the disk


```
$ sudo mkfs.ext4 -m 0 -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/sdb
mke2fs 1.47.0 (5-Feb-2023)
Discarding device blocks: done
Creating filesystem with 26214400 4k blocks and 6553600 inodes
Filesystem UUID: 3975c0a0-6b73-4b5c-81a0-9d8531837479
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872

Allocating group tables: done
Writing inode tables: done
Creating journal (131072 blocks): done
Writing superblocks and filesystem accounting information: done
```

## Mount the disk

```
$ sudo mkdir -p /mnt/disks/img
$ sudo mount -o discard,defaults /dev/sdb /mnt/disks/img
$ sudo chmod a+w /mnt/disks/img
```

## Configure docker and pull the image

Add a line `"data-root": "/mnt/disks/img"` to  the docker config file `/etc/docker/daemon.json` so that docker stores data in the mounted disk. The file becomes:

```
{
        "live-restore": true,
        "log-opts": {
                "tag": "{{.Name}}"
        },
        "storage-driver": "overlay2",
        "mtu": 1460,
        "data-root": "/mnt/disks/img"
}
```

Remember the comma before the line that you added! I made the mistake, you do not have to...

Restart the docker service

```
sudo systemctl restart docker
```

Pull the images that you need. In the example workflow we use three of them (in addition to a tiny python image), so we pull:

```
docker pull ghcr.io/katilp/pfnano-image-build:main
docker pull cernopendata/cernopendata-client
docker pull rootproject/root:latest
```

After the pull is ready, you can see them with:

```
$ docker image ls
REPOSITORY                          TAG       IMAGE ID       CREATED         SIZE
rootproject/root                    latest    34eb35079484   4 weeks ago     2.76GB
cernopendata/cernopendata-client    latest    f690d7308889   7 weeks ago     282MB
ghcr.io/katilp/pfnano-image-build   main      d95521bc96a9   11 months ago   32.1GB
```


Exit from the VM with `exit` and stop it

```
gcloud compute instances stop vm-work
```

## Create the image

```
gcloud compute images create pfnano-image-disk --project=hip-new-full-account --source-disk=pfnano-disk --source-disk-zone=europe-west4-c --storage-location=europe-west4
```

## Delete the disk and the VM

Now as you have the image created, you can delete the VM and the original disk.

```
gcloud compute instances delete vm-work --zone=europe-west4-c
```

```
gcloud compute disks delete pfnano-disk --zone=europe-west4-c
```



## Costs

### VM

The VM costs depends on the time

### Disk


### Image


::::::::::::::::::::::::::::::::::::: keypoints 

- A secondary boot disk with the container image preloaded can speed up the workflow start.


::::::::::::::::::::::::::::::::::::::::::::::::

