---
title: Kahu Deployment Guide
menuTitle: Deployment Guide
description: "Deployment guide for the Kahu (Container Data Backup & Restore) project"
weight: 20
disableToc: false
tags: ["deployment guide", "kahu", "soda-cdm"] 
---

Kahu features can be realised through deployment on kubernetes(k8s) cluster. 
If you do not have a k8s cluster, please refer [kubernetes setup documentation](https://kubernetes.io/docs/setup) to setup one.

Note: Need to install the required things for nfs server on the k8s nodes (Example: sudo apt install nfs-kernel-server on ubuntu)


## Deployment options
### Install from source using  deployment yamls : 
1) Kahu deployment is done in its own namespace. Default namespace choosen is "kahu" which can be changed. So "kahu" namespace to be created before deploying kahu services
```shell
kubectl create namespace kahu
```

2) Clone kahu repository to get deploy related files
```shell
git clone https://github.com/soda-cdm/kahu.git
```

3) Deploy kahu custom resource definitions (CRD)
```shell
cd kahu/config/crd/v1beta1/bases/
kubectl apply -f .
```

4) Kahu controller service watches on Kubernetes csi snapshot related CRD's. So these CRDs need to be installed as well.

Based on the kubernetes version, deploy the respective version of CustomResourceDefinition(CRD).

For version v1beta1:
```shell
cd ../../../../deploy/dependency/snapshotcrds/v1beta1
kubectl apply -f .
```

For version v1:
```shell
cd ../../../../deploy/dependency/snapshotcrds/v1
kubectl apply -f .
```

5) Kahu provides nfs as the default way for backing up metadata. For using nfs as the metadata backup provider, nfs server need to be up and running.
This nfs server can be hosted anywhere and used provided it has connectivity and access with kahu provider.

6) Alternatively we can also host an nfs server inside cluster with below steps. Create a nfs service and extract service ip address
```shell
cd ../../../../deploy/yamls/nfsserver/
kubectl apply -f nfs-server-service.yaml
kubectl get service -n kahu | grep nfs-server
```
7) Edit nfs-pv.yaml to update NFS-SERVER-IP-ADDR with service ip address obtained in step 6

8) Create pv
```shell
kubectl apply -f nfs-pv.yaml
```  

9) Create nfs server deployment
```shell
kubectl apply -f nfs-server-deployment.yaml
```  
10) Create nfs provider deployment
```shell
cd ../nfsprovider/
kubectl apply -f nfs-provider-deployment.yaml 
```    
11) Finally, create kahu controller service to enable backup and restore controlers
```shell
cd ../controllers/
kubectl apply -f backup_restore_controllers.yaml
```    
12) Verify state of kahu microservices after deployment
```shell
kubectl get pods -n kahu
```  

#### Install volume backup provider
For performing backup which involves volume, corresponding volume provider needs to be deployed.

For demonstration, we use OpenEBS ZFS CSI Driver for provisioning local PVs.
To deploy OpenEBS ZFS CSI Driver and set it up for volume provisioning, please refer the [documentation](https://github.com/openebs/zfs-localpv)

Once it is completed, follow below steps

1) Create openebs zfs volume provider deployment
```shell
kubectl create -f https://raw.githubusercontent.com/soda-cdm/kahu/main/deploy/volumeprovider/openebs-zfs-provider-deployment.yaml
```  
2) Verify the state of deployment and ensure it is running
```shell
kubectl get pods -n kahu | grep kahu-openebs-zfs-provider
```
