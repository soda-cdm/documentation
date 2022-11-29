---
title: Kahu - The SODA container data backup and restore project
menuTitle: Kahu User Guide
description: "User guide for the Kahu (Container Data Backup & Restore) project"
weight: 10
disableToc: false
tags: ["user guide", "kahu", "soda-cdm"] 
---
# User Guide
Kahu features can be realised through deployment on kubernetes(k8s) cluster. 
For kahu deployment steps and options, please refer [deployment guide](https://github.com/soda-cdm/documentation/blob/main/kahu/deployment_guide.md)

Follow below steps to experience typical usecase which kahu provides
1. Clone kahu project to get usage examples
```shell
git clone https://github.com/soda-cdm/kahu.git
cd kahu/examples
```

2. Create backup location. In this example, backup location is created using nfs provider
```shell
kubectl create -f backuplocation.yaml
```
## Typical usecases

### Backup and restore applications at namespace scope
1. Create an application with its all resources at "deafult" namespace.

2. Create backup object at namespace level
```shell
kubectl apply -f backup-namespace.yaml
```
3. Verify the state of backup object in the cluster
```shell
kubectl get backups
```
4. Once the backup stage is "Finished", restore an application from backup by creating restore object. Edit restore.yaml to update "backupName" and "namespaceMapping" with desired values. This sample yaml uses "restore-ns" as the namespace to restore resources of "default" namespace
```shell
kubectl apply -f restore.yaml
```
5. Verify the state of restore object in the cluster
```shell
kubectl get restores
```
6. Once the restore stage is "Finished", verify the restore of application resources by checking resources as "restore-ns" namespace
```shell
kubectl get all -n restore-ns
```

### Backup and restore a specific resource
1. Create 2 pods namely pod1234, pod5678 in "default" namespace 

2. Create backup object to backup only pod1234
```shell
kubectl apply -f backup-specific-resource.yaml
```
3. Verify the state of backup object in the cluster
```shell
kubectl get backups
```
4. Once the backup stage is "Finished", restore an application from backup by creating restore object. Edit restore.yaml to update "backupName" and "namespaceMapping" with desired values. This sample yaml uses "restore-ns" as the namespace to restore resources of "default" namespace
```shell
kubectl apply -f restore.yaml
```
5. Verify the state of restore object in the cluster
```shell
kubectl get restores
```
6. Once the restore stage is "Finished", verify the restore of application resources by checking resources as "restore-ns" namespace.
Only pod1234 is expected to be restored back
```shell
kubectl get all -n restore-ns
```
