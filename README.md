# Introduction

This repository contains terraform configurations for deploying SQL Server 2019 Big Data Clusters and Azure Arc Enabled Data Services Controllers to infrastructure 
virtualized via VMware. The "Full stack" solution involves the deployment of four terraform configurations:

- [virtual_machine](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/vmware_vm_pool/modules/virtual_machine/README.md) module for creating 
  virtual machines to underpin a Kubernetes cluster on, this requires that an Ubuntu 18.04 virtual machine template is created, as detailed in the instructions provided
  in this documentation. 

- [kubernetes_cluster](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/kubernetes/modules/kubernetes_cluster/README.md) module for creating am 
  Kubernetes cluster.
  
- [metallb](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/kubernetes/modules/metallb/README.md) for deploying a software load balancer to a
  Kubernetes cluster in order to facilitate LoadBalancer services.

- [px_store](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/kubernetes/modules/px_store/README.md) module for deploying the Portworx 
  storage platform to a Kubernetes cluster. Portworx PX Store is a 100% software defined Kubernetes storage solution that can deployed to Red Hat OpenShift, Kubernetes on-
  premises, Google Anthos, AKS, EKS or GKE. 
  **Note that [Portworx Essentials](https://docs.portworx.com/concepts/portworx-essentials/) - which PX Store comes with, is free to use.**
  
- [px_backup](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/kubernetes/modules/px_backup/README.md) module for backing up Kubernetes clusters,
  the documentation for this module includes a walked through example of how to backup and restore a big data cluster storage pool. PX Backup works with any CSI compliant
  storage plugin that supports snapshots - however, to leverage incremental backups you must use PX Store. PX Backup supports objects level backups and backup destinations of S3 
  in AWS/GCP and on-premises and also Azure blob storage. Further information is provided in the [Portworx PX Backup documentation](https://portworx.com/products/px-backup/).
  
- px_autopilot (coming soon - work in progress) module for configuring PX-Autopilot, PX-Autopilot polls prometheus for events that indicate that persistent volumes or volume
  pools are running out of spare storage capacity, it will then expand persistent volumes and / or volume pools based on whatever PX-Autopilot rules  have been created.
  PX-Autopilot is available with Portworx essentials at no extra cost with volume expansion capabilities, PX-Autopilot volume *pool* expansion capabilities are only available
  with Portworx Enterprise.
  
- [big_data_cluster](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/azure_data_services/modules/big_data_cluster/README.md) module for deploying
  a big data cluster to a kubernetes cluster.
  
- [azure_arc_ds_controller](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/azure_data_services/modules/azure_arc_ds_controller/README.md) module
  for deploying an Azure Arc enabled Data Services Controller to a Kubernetes cluster.
  
**However**, each module can be deployed independantly, meaning - if you are using:

- GKE, EKS, OpenShift or a Kubernetes cluster deployed to (for example) a NUC based home lab, you can use the portworx, Big Data Cluster and Azure Arc Enabled Data
  Services Controller modules.
- Infrastructure you have already provisioned on Hyper-V or Linux KVM, you can still use the kubernetes, portworx, Big Data Cluster and Azure Arc Enabled Data Services
  Controller modules.  

# Prerequisites

- VMware vSphere cluster
- Linux host that can talk to the vSphere endpoint, referred to as **"The build server"** hereafter 
- Terraform and git installed on client machine
- The user under which terraform is executed as is a member of the sudo-ers group on the client machine (use visudo)
- Template virtual machine with Ubuntun 18.04 server as the guest OS
- An Azure account
- Azure CLI installed on the build server
  
# Environment Config Has Been Tested With

- Azure CLI 2.19.1 
- VMware vSphere 7.0.1
- Terraform v0.14.5 with the following providers
  - registry.terraform.io/hashicorp/local v2.0.0
  - registry.terraform.io/hashicorp/null v3.0.0
  - registry.terraform.io/hashicorp/template v2.2.0
  - registry.terraform.io/hashicorp/vsphere v1.24.3
  - registry.terraform.io/hashicorp/azuread v1.4.0
  - registry.terraform.io/hashicorp/azurerm v2.48.0
- Linux client: Ubuntu Server 18.04 LTS
- Virtual machine template guest OS: Ubuntu 18.04
- Kubespray 2.15.0

# Instructions

The following steps are to be performed on the same machine used to copy the public ssh key onto the template virtual machine:

1. Make the Hashicorp package repository trusted:

   `curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -`

2. Add the Hashicorp repository:

   `sudo apt-add-repository "deb [arch=$(dpkg --print-architecture)] https://apt.releases.hashicorp.com $(lsb_release -cs) main"`

3. Install git and terraform:

   `sudo apt-get install terraform git`

4. Git clone this repo:

   `git clone https://github.com/PureStorage-OpenConnect/arc-px-vmware-faststart.git`

5. Choose the configuration(s) to deploy, currently there are three subdirectories containing different `terraform` configurations in module form:

- vmware_vm_pool
  - use the [virtual_machine](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/vmware_vm_pool/modules/virtual_machine/README.md) module to create the virtual machines that underpin your kubernetes cluster - using the template created as per the instuctions provided earlier
    in this README 
  
- kubernetes
  - use the [kubernetes_cluster](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/kubernetes/modules/kubernetes_cluster/README.md) module for creating a kubernetes cluster
  - use the [px_store](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/kubernetes/modules/px_store/README.md) module for deploying portworx to a kubernetes cluster
  - use the [px_backup](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/kubernetes/modules/px_backup/README.md) module for kubernetes data protection
  
- azure_data_services
  - use the [big_data_cluster](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/azure_data_services/modules/big_data_cluster/README.md) module for deploying a big data cluster to the kubernetes cluster
  - use the [azure_arc_ds_controller](https://github.com/PureStorage-OpenConnect/Arc-PX-VMware-Faststart/blob/main/azure_data_services/modules/azure_arc_ds_controller/README.md) module for deploying an Azure Arc enabled Data Services Controller to the kubernetes cluster

Click on the module name link for documentation on how to deploy a specific module, before issuing `terraform apply` for the first time in any directory, always run `terraform init`. 

# Known Issues / Limitations

This configuration does not currently work with a virtual macine template based on Ubuntu versions 20.04/20.10

# Roadmap

Subsequent stages in in the development of this work includes:

- Ubuntu 20.04/20.10 support
- Deployment of a MetalLB load balancer
- Deployment of Azure Data Services with load balancer endpoints

# Feedback

Feedback is welcome and should be submitted via the creation of issues.

# Credits 
Credit goes to both the EMEA Solutions Architects; Eugenio Grosso and Joe Gardiner for helping in putting this work together.
