---
title: Kahu Requirements & Design
menuTitle: Design Doc
description: "Requirements and Design for the Kahu (Container Data Backup & Restore) project"
weight: 30
disableToc: false
tags: ["design", "requirements", "kahu", "soda-cdm"] 
---


**Authors:** [Sanil Kumar D](https://github.com/skdwriting), [Xulin](https://github.com/wisererik),  [Sushantha Kumar](https://github.com/sushanthakumar), [Amit Kumar Roushan](https://github.com/amitroushan), [Pravin Ranjan](https://github.com/PravinRanjan10), [Joseph Vazhappilly](https://github.com/joseph-v)

This documentation serves as the design spec for Kahu, a backup and restore project for containerized applications.

## Goal
KAHU is a project under soda cdm organization which deals with backup and restore aspects of container data management.

## Motivation and Background
Data protection is a key aspect for most of the stateful applications. In containerized deployments, due to the nature of applications, this aspect becomes even more important. Backup and restore features can significantly help for the application lifecycle management.

## Non-Goals
Replication and failover feature is not currently considered. May get added to this same project scope in future

## Assumptions and Constraints
Features discussed here are only for containerized applications.

##  Requirement Analysis
### Key Requirements
#### Backup and Restore features

* Support metadata backup of K8S resources at different scope such as cluster, namespace, label selector
* Support data backup of applications in full snapshot way
* Framework level intelligence to support incremental and differential backup
* Restoration from backup at customized scope such as full backup restore, restore to mapped namespaces
* Support to execute pre hook and post hook during backup and restore
* Data backup across providers
* Provider independent backup such as using restic
* CSI based and Non CSI based volume backups.
* Backup and restore across clusters.
* Support at Federation level
* Application aware backup and restore
* Encryption and compression aspects during backups

#### Storage provider framework support
* Dynamic integration of any storage providers for metadata or volume
* Support coexistence of multiple providers during runtime
* Flexible life cycle management for providers with resistartion/unregistration, active/passive states
* Integration of providers with more capabilities to realize the offloading of complete backup/restore operations

#### Automation and Orchestration 
* Scheduled backup
* Backup based on event driven mechanisms
* Backup based on policies
* Backup service plan – Provider independent service plan


##  Architecture Analysis
### High level Architecture

  ![](../resources/Level_0_Arch.png)
  
* Kahu provides APIs to use its backup restore feature. It has set of core services and storage provider framework
* The Operation and Management layer sits on top of kahu and makes use of kahu features. Kahu itself provides an O & M layer with certain features and respective services. At the same time, any third party system can have O&M layer to operate on top of Kahu
* The storage providers represent the pluggable layer to Kahu. Kahu will provide a default set of providers for certain use cases. Any third party can plug its provider to Kahu by implementing kahu provider interfaces
  
### Module level Architecture
  ![](../resources/Level_1_Arch.png)
  
Key components of the ecosystem are as follows
#### External Components:
**Clients**: These are different northbound clients which access KAHU features through its APIS

**3rd Part O&M Layer**: These are the systems which possess their own O&M features and
 internally uses KAHU for backup/restore usecases

**3rd Party Providers:** These are the systems which integrates with KAHU and helps to realise
 backup/restore features with storage backends.

#### KAHU Components:
**KAHU O&M Layer:** This is an O&M layer owned by kahu. This layer provides features, 
customizations on top of kahu's backup/restore features. 

**API Layer:** This layer contains the APIs exposed by KAHU for the north bound clients. These 
APIs are in the form of Custom Resource Definition(CRD) supported by K8S.

**Core Services:** This contains the core services of kahu. This has modules to perform backup 
service and restore service

**O&M Services:** This represents the modules which are responsible for realizing O&M features 
exposed by KAHU O&M layer

**Storage Provider Framework:** This is a framework which allows any 3rd party to implement 
backup/restore related features and integrate with KAHU. This framework contains registration 
mechanisms for any provider, services to perform backup/restore of metadata and volume.

**KAHU Default providers:** Some standard and well known ways of backup/restore can be
 provided by KAHU itself for direct usage. This layer represents a collection of such providers.
 This incudes NFS/S3 providers etc for metadata backup and csi snapshotter for volume snapshotting which can be developed by kahu community


##  Detailed Design
###  Use case View
#### List of Typical Usecases
* Kuberenetes cluster or application admin wants to take the backup of all the resources in the cluster
* Kuberenetes cluster or application admin wants to take the backup of specified applications/resources in the cluster
* Kuberenetes cluster or application admin wants to simply restore as it is from the created backup
* Kuberenetes cluster or application admin wants to partly restore or restore specific resources from the created backup
* Kuberenetes cluster or application admin wants to partly restore or restore specific resources from the created backup
* Kuberenetes cluster or application admin wants to specify some actions in the form of hooks before and after taking the backup
* Kuberenetes cluster or application admin wants to specify some actions in the form of hooks before and after performing the restore

#### Use case context model: To be added
#### Interface model
**North Bound API Definitions:**

[This Google Drive folder](https://drive.google.com/drive/folders/1qIt-CaXVqjJtwbfEWnsgMrXhMABiO4FC) contains APIs exposed by KAHU through K8S CRDs


  
**Provider Interfaces:**
```go
service Identity {

  rpc GetProviderInfo(GetProviderInfoRequest)
      returns (GetProviderInfoResponse) {}

  rpc GetProviderCapabilities(GetProviderCapabilitiesRequest)
      returns (GetProviderCapabilitiesResponse) {}

  rpc Probe(ProbeRequest)
      returns (ProbeResponse) {}
      
}
```
```go
service MetaBackup {

  rpc Upload(stream UploadRequest)
      returns (Empty) {}

  rpc ObjectExists(ObjectExistsRequest)
      returns (ObjectExistsResponse) {}

  rpc Download(DownloadRequest)
      returns (stream DownloadResponse) {}

  rpc Delete(DeleteRequest)
      returns (Empty) {}
      
}
```

###  Data View
####  Data Model
  ![](../resources/Data_Model.png)

######  Key CRD models:

**Provider CRDs:** These CRDs represent the attributes of providers which include metadata service provider and volume service provider. These CRDs are created during provider registration.

**BackupLocation CRD:** This represents the provider with its custom configuration.

**Backup CRD:** This represents the backup object specification.

**Restore CRD:** This represents the restore object specification.

####  Development and Deployment Context
##### Code Model  

This project uses below repo to maintain code
https://github.com/soda-cdm/kahu

###### Code Structure
  ![](../resources/Code_Structure.png)

##### Debug Model: To be added

##### Build and Package: To be added


##### Deployment

**Deployment use case 1**: On single node cluster
  ![](../resources/Single_Node_Deployment.png)

**Deployment use case 2**: On multi node cluster
  ![](../resources/Multi_Node_Deployment.png)

##### Execution View: To be added

### Sequence Diagrams

**Create backup:**

  ![](../resources/Create_Backup_Sequence.png)
  
  
**Delete backup:**

  ![](../resources/Delete_Backup_Sequence.png)  
  
  
  **Metadata Provider Registration:**
  
  ![](../resources/Metadata_Provider_Registration_Sequence.png)  
  
  
  **Volume Provider Registration:**
  
  ![](../resources/Volume_Provider_Registration_Sequence.png)    
  
  
  **Hooks Execution:**
  
  ![](../resources/Hooks_Execution_Sequence.png)
  
  
  ### Design Alternatives and other notes
  
  
