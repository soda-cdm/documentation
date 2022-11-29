---
title: Kahu Deployment Guide
menuTitle: Deployment Guide
description: "Deployment guide for the Kahu (Container Data Backup & Restore) project"
weight: 20
disableToc: false
tags: ["deployment guide", "kahu", "soda-cdm"] 
---

# Deployment Guide
Kahu features can be realised through deployment on kubernetes(k8s) cluster. 
If you do not have a k8s cluster, please refer [kubernetes setup documentation](https://kubernetes.io/docs/setup) to setup one.


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

4) Kahu provides nfs as the default way for backing up metadata. For using nfs as the metadata backup provider, nfs server need to be up and running.
This nfs server can be hosted anywhere and used provided it has connectivity and access with kahu provider.

5) Alternatively we can also host an nfs server inside cluster with below steps. Create a nfs service and extract service ip address
```shell
cd ../../../../deploy/yamls/nfsserver/
kubectl apply -f nfs-server-service.yaml
kubectl get service -n kahu | grep nfs-server
```
6) Edit nfs-pv.yaml to update NFS-SERVER-IP-ADDR with service ip address obtained in step 5

7) Create nfs server deployment
```shell
kubectl apply -f nfs-server-deployment.yaml
```  
8) Create nfs provider deployment
```shell
cd ../nfsprovider/
kubectl apply -f nfs-provider-deployment.yaml 
```    
9) Finally, create kahu controller service to enable backup and restore controlers
```shell
cd ../controllers/
kubectl apply -f backup_restore_controllers.yaml
```    
10) Verify state of kahu microservices after deployment
```shell
kubectl get pods -n kahu
```  
