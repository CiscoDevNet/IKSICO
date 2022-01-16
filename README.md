# Deploying Cloud Native Application and required infrastructure with Intersight Cloud Orchestrator (ICO)
## Contents
        Use Case

        Pre-requisites

        Intersight Target configuration for Intersight and TFCB on prem entities

        Provision infrastructure and deploy sample k8s App

            Step 1: Importing ICO template for setting up IKS Infrastructure

            Step 2: Importing ICO template for App Deployment

            Step 3: Importing ICO template for Decommissioning

            Step 4: Setup IKSGlobal Variables

            Step 5: Setup IKSGlobal Sensitive Variables

            Step 5: Setup IKSPolicy Variables

            Step 6: Setup IKSCluster Variables

            Step 7: Setup IKSApp Variables

            Step 8: Setup IKSRemove Variables

            Step 9: Execute ICO template for IKS Cluster Provisioning

            Step 10: Execute ICO template for App Deployment

        Deprovision
            Execute ICO template for Deprovisioning


### Use Case

* As a DevOps and App developer, use ICO (Intersight Cloud Orchestrator) to provision your own IKS cluster, deploy your apps and view insights in Intersight

This use case addresses the second flow in the below diagram:

![alt text](https://github.com/prathjan/images/blob/main/iksico.png?raw=true)

### Pre-requisites

2. Sign up for a user account on Intersight.com. You will need Premier license as well as IWO and ICO license to complete this use case. 

3. Sign up for a TFCB (Terraform for Cloud Business) at https://app.terraform.io/. Log in and generate the User API Key. You will need this when you create the TF Cloud Target in Intersight.

4. You will need access to a vSphere infrastructure with backend compute and storage provisioned

6. You will need to have some minimal knowledge of Intersight ICO. Please review tutorials on Youtube as well as the following: https://intersight.com/help/saas/features/orchestration/configure#intersight_cloud_orchestrator

7. Review this codeexchange entry that gives you info on how TFCB is leveraged to create your IKS cluster: https://developer.cisco.com/codeexchange/github/repo/CiscoDevNet/intersight-tfb-iks. The ICO workflow addressed in this use case will leverage this codeexchange entry.


### Intersight Target configuration for Intersight and TFCB on prem entities

You will log into your Intersight account and create the following targets. Please refer to Intersight docs for details on how to create these Targets:

    Assist

    vSphere

    TFC Cloud

    TFC Cloud Agent - When you claim the TF Cloud Agent, please make sure you have the following added to your Managed Hosts. This is in addition to other local subnets you may have that hosts your kubernetes cluster like the IPPool that you may configure for your k8s addressing:
    NO_PROXY URL's listed:

            github-releases.githubusercontent.com

            github.com

            app.terraform.io

            registry.terraform.io

            releases.hashicorp.com

            archivist.terraform.io

### Provision infrastructure and deploy sample k8s App


## Step 1: Importing ICO template for setting up IKS Infrastructure

Clone the following github repo to get the ICO template:

https://github.com/CiscoDevNet/IcoTemplates.git

Import the template ** IKSICOInfra.json ** in Intersight:

![alt text](https://github.com/prathjan/images/blob/main/importiksico.png?raw=true)

Review the worflows imported. 

![alt text](https://github.com/prathjan/images/blob/main/iksicosum.png?raw=true)

In short, the following workflows are the ones referenced in the above main workflow:

IKSWorkflow-Pre - Sets up the TFCB workspaces and uploads variables for:

    IKSGlobal - Sets up the global workspace and vars 

    IKSPolicy - Sets up the policy workspace and vars

    IKSCluster - Sets up the cluster workspace and vars

    IKSApp - Sets up the app workspace and vars

    IKSRemove - Removes all IKS cluster resources provisioned and removes the TFCB workspaces

IKSWorkflowBase - Executes global, policy, cluster workspace


## Step 2: Importing ICO template for App Deployment

Clone the following github repo to get the ICO template:

https://github.com/CiscoDevNet/IcoTemplates.git

Import the template ** IKSICOApp.json ** in Intersight:

![alt text](https://github.com/prathjan/images/blob/main/importiksapp.png?raw=true)

Review the worflow imported. 

![alt text](https://github.com/prathjan/images/blob/main/iksicoappsum.png?raw=true)

IKSWorkflow_App - Execute app workspace to deploy the sample app

## Step 3: Importing ICO template for Decommissioning

Clone the following github repo to get the ICO template:

https://github.com/CiscoDevNet/IcoTemplates.git

Import the template ** IKSICODel.json ** in Intersight:

![alt text](https://github.com/prathjan/images/blob/main/importiksdel.png?raw=true)

Review the worflow imported. 

![alt text](https://github.com/prathjan/images/blob/main/iksicodelsum.png?raw=true)

The above workflow will delete the IKS cluster and also delete the TFCB workspaces.

## Step 4: Setup IKSGlobal Variables

Open IKSWorkflow-Pre->IKSWorkflowVars->Add Global Variables. Add the following variables:

    device_name = Name of the Virtual Machine Provider you wish to add. i.e vCenter

    portgroup = Name of the portgroup(s) to be used in this provider. Example: "Management"

    datastore = Name of the datastore to be used with this provider.

    vspherecluster = Name of the cluster you wish to make part of this provider within vCenter.

    resource_pool = Name of the resource pool to be used with this provider.

    organization = Intersight Organization name

    #ip_pool_policy params

    starting_address = Starting IP Address you want for this pool.

    pool_size = Number of IPs you want this pool to contain.

    netmask = Subnet Mask for this pool.

    gateway = Default gateway for this pool.

    primary_dns = Primary DNS Server for this pool.

    #instance type

    cpu = Number of CPU allocated to the virtual machine.

    disk_size = Amount of disk to be assigned to the virtual machine in GiB

    memory = Amount of memory assigned to the virtual machine in MiB.

## Step 5: Setup IKSGlobal Sensitive Variables

Open IKSWorkflow-Pre->IKSWorkflowVars->Add Global Sensitive. Add the following variables:

password = vSphere admin password -> mark as sensitive

## Step 6: Setup IKSPolicy Variables

Open IKSWorkflow-Pre->IKSWorkflowVars->Add Policy Variables. Add the following variables:

globalwsname - workspace of the global WS. In this use case, it is IKSGlobal

api_key - Intersight api_key

secretkey - Intersight secretkey

## Step 7: Setup IKSCluster Variables

Open IKSWorkflow-Pre->IKSWorkflowVars->Add k8s Variables. Add the following variables:

globalwsname - workspace of the global WS. In this use case, it is IKSGlobal

api_key - Intersight api_key

secretkey - Intersight secretkey

## Step 8: Setup IKSApp Variables

ikswsname - IKS workspace name. In this case, enter IKSCluster

## Step 9: Setup IKSRemove Variables

globalwsname - workspace of the global WS. In this use case, it is IKSGlobal

api_key - Intersight api_key

secretkey - Intersight secretkey

## Step 10: Execute ICO template for IKS Infra Setup

## Step 11: Execute ICO template for App Deployment

Deprovisioning 

    Execute ICO template for Deprovisioning


