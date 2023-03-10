---
title: Kahu Development Guide
menuTitle: Development Guide
description: "Development guide for the Kahu (Container Data Backup & Restore) project"
weight: 10
disableToc: false
tags: ["development guide", "kahu", "soda-cdm"] 
---
Development guide, in general helps any opensource enthusiast to do his/her contribution for project development artifacts. This guide helps for contributing to kahu project development.

**Development Steps**

Follow below steps to get started with. Assuming that kahu project is forked by developer using github account
1. Clone kahu project and switch to respective branch
```shell
git clone https://github.com/<developer-github-id>/kahu.git
cd kahu/
git checkout <contribution-branch>
```

2. APIs are versioned and placed inside the project at the location: apis/kahu. If any update is done to this part, use below command to re generate API specification and autogenrated code.
```shell
make manifests
```
Updated APIs can be checked at the location: config/crd under respective version directory.

3. Kahu has grpc interfaces for below inter module communications. 

   Controller Service <-> Metadata Service:  proto file is providerframework/metaservice/proto/metaservice.proto

   Volume Service <-> Volume Provider:  proto file is providers/proto/providerservice.proto

In case of changes needed to any of these interfaces, respective proto file needs to be updated and below command to be executed to regenerate the protobuf files.
```shell
 make generated_files
```

4. For any modification to other part of the code, execute below commands to  build the binaries and generate the docker images.
```shell
 make release-images
```
Build supports configuration of version number (VERSION) and architecture types( PLATFORM = X86/ARM)
By default, architecture selected is X86 and builde version is v0.1.0.
This can be updated and built as follows
```shell
 make release-images VERSION=v1.1.1 PLATFORM=ARM
```

5. Images will be available at the location: _output/release-images. Use below commands to load the image in corresponding cluster nodes
```shell
 docker load -i <image-tar-file>
```

**Verification**

Kahu has end to end testcases which can help to quickly verify that basic usecases are working fine post modifications.
It is based on Ginkgo  which is a testing framework for Go designed to help you write expressive tests.
ginkgo needs to be installed on the environment to run these test cases.

Execute below command and verify the test execution result.
```shell
cd test/e2e
ginkgo
```

Please refer [User Guide](https://github.com/soda-cdm/documentation/blob/main/kahu/user_guide.md) to know more about kahu usecase executions.
