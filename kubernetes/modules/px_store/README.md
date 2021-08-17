# Overview

This module deploys Portworx PX Store to the target kubernetes cluster and creates a storage class. PX Store is a fully software defined storage solution for kubernetes and
platforms based on kubernetes.

# Usage

## Apply

Execute the following command from the `Arc-PX-VMware-Faststart/kubernetes` directory
```
terraform apply -target=module.px_store --auto-approve 
```

## Destroy

```
terraform destroy -target=module.px_store --auto-approve 
```

# Dependencies

- Context in .kube/config file of the home directory of the current user with which kubectl can connect to a kubernetes cluster
- Portworx spec created as per the section below

# Portworx Spec Creation

1. Log into [Portworx Central](https://central.portworx.com/specGen/wizard)

2. Select te Portworx Essentials radio button

<img style="float: left; margin: 0px 15px 15px 0px;" src="https://github.com/PureStorage-OpenConnect/arc-px-vmware-faststart/blob/main/images/px_store/px1.PNG?raw=true">

3. Enter the Kubernetes version in the Kubernetes version box, tis configuration uses version 1.19.7 by defualt

<img style="float: left; margin: 0px 15px 15px 0px;" src="https://github.com/PureStorage-OpenConnect/arc-px-vmware-faststart/blob/main/images/px_store/px2.PNG?raw=true">

4. Click the "On Premises" radio button and place ticks in the boxes for:
- auto create journal device
- skip KVDB device

   Finally, hit next 

<img style="float: left; margin: 0px 15px 15px 0px;" src="https://github.com/PureStorage-OpenConnect/arc-px-vmware-faststart/blob/main/images/px_store/px3.PNG?raw=true">

5. Accept the networking defaults by clicking on next

<img style="float: left; margin: 0px 15px 15px 0px;" src="https://github.com/PureStorage-OpenConnect/arc-px-vmware-faststart/blob/main/images/px_store/px4.PNG?raw=true">

6. On the Customize tab check "Enable stork" and "Enable CSI" and then hit next

<img style="float: left; margin: 0px 15px 15px 0px;" src="https://github.com/PureStorage-OpenConnect/arc-px-vmware-faststart/blob/main/images/px_store/px5.PNG?raw=true">

**Note:** 
- The use of PX-Security mandates that Portworx is installed with the CSI option
- The use of PX-Backup mandates that Portworx is installed with Stork
- Installing Portworx with the Monitoring option also installs many of the components (Prometheus etc) required by PX-Autopilot, i.e. it allows you to get PX-Autopilot up and running faster.

7. Accept the agreement for using Portworx

<img style="float: left; margin: 0px 15px 15px 0px;" src="https://github.com/PureStorage-OpenConnect/arc-px-vmware-faststart/blob/main/images/px_store/px6.PNG?raw=true">

8. Enter a spec name and label 

<img style="float: left; margin: 0px 15px 15px 0px;" src="https://github.com/PureStorage-OpenConnect/arc-px-vmware-faststart/blob/main/images/px_store/px7.PNG?raw=true">



# Variables

The minimum set of variables that need to be configured consists of those with no default values.

| Name                        | Data type | Description / Notes                                                 | Mandatory (Y/N) | Default Value                   |
|-----------------------------|-----------|---------------------------------------------------------------------|-----------------|---------------------------------|
| use_csi                     | boolean   | Set to true if the Portworx spec has been created with              |        Y        | false                           |
| px_repl_factor              | number    | Number of persistent volume replicas to create for HA purposes      |        Y        | 2                               |
| px_spec                     | string    | URL for Portworx spec YAML manifest file                            |        Y        | **No default value**            |
| use_stork                   | boolean   | Determines whether the storage aware schedule should be used       |        Y        | true                            |

**Note:** that pod / persistent volume co-location capabilities of Stork are primarily intended to be of use when:

- hyper-converged infrastructure is used
- the Kubernetes cluster hosting the storage cluster has worker nodes in different data centers or public cloud availability zones

# Known Issues / Limitations

Destroy provisioner yet be implemented for the null resource that deploys Portworx to the kubernetes cluster. 

[Back to root module](https://github.com/PureStorage-OpenConnect/arc-px-vmware-faststart/blob/main/README.md)
