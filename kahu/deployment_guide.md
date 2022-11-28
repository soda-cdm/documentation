# Deployment Guide
Kahu features can be realised through deployment on kubernetes(k8s) cluster. 
If you do not have a k8s cluster, there are several ways to install one . Below are some of the options

[kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

[kind](https://kind.sigs.k8s.io/docs/user/quick-start/)

## Deployment options
### Install from source using  deployment yamls : 
In this method, below steps to be followed
1) Clone kahu repository to get necessary files
```
git clone https://github.com/soda-cdm/kahu.git
```

2) Deploy kahu custom resource definitions (CRD)
```
cd kahu/config/crd/v1beta1/bases/
kubectl apply -f .
```

