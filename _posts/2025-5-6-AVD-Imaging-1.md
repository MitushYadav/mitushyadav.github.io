---
layout: post
title: Azure Virtual Desktop Image Creation
date: 2025-05-08
published: true
---

Azure Virtual Desktop(AVD) is a modern Virtual Desktop Infrastructure(VDI) solution offering both Remote Desktop and Remote Apps. It brings together Microsoft 365 and Azure to provide users with multi-session and session-session Windows 11 and Windows 10 experience.

AVD utilizes Azure Virtual Machines(VMs) platform to deliver the session hosts. The necessary brokering and gateway components are managed by Microsoft as part of the offering.

You can create the session hosts using the Azure marketplace images or customize the marketplace images to create your own golden images.

## Golden Image Creation

Golden image creation is not new to VDI, and AVD is no different. The main differentiating factor is the presence of Windows multi-session client OS which can only be run in Azure Virtual Desktop.

Since majority of the organizations I've come across use Windows client OS in their AVD deployments, I'll focus on Windows 10/11 single-session and multi-session in this blog.

Options for creating a golden image are:
- Manual method
- Script based automated workflow
- Azure Image Builder
- AVD Custom Image Templates
- DevOps pipeline workflow

I'll go over each of them and discuss their pros and cons.

### Manual method

Manual method is self-explanatory. You create an Azure VM or a local VM using Azure Marketplace image or a Windows installation source. The configuration and customization is done manually. The application sources are copied over and installed manually as well. Some degree of automation can be present but the overall process is considered to be manual. Finally, the VM is sysprepped and shared/uploaded to Azure Compute Gallery or a standalone image blob.

Pros:
- Very simple to get started
- Very customizable

Cons:
- Lack of standardization can lead to different final images
- Time consuming and error prone

I'd say that you can use this method to get started on image creation for AVD to understand the process better, and automate it eventually.

### Script based automated workflow

In this process, the workflow is automated using a script, generally a Powershell script. Python can be used too but the availability of Az module for Powershell makes it much easier to do in Powershell. This automation can also be done on a local VM and then uploaded to Azure but since most people do it directly on an Azure VM, I'll focus on that.

The primary steps are roughly the same:
1. Create an Azure VM using an Azure marketplace image. Choose multi-session or single session image as the source image depending on the final image type you want.
2. Configure the Windows OS on the VM using registry entries, powershell commands or endpoint management policies.
3. Install required applications in the OS using automated packages, scripts or apps deployed from a deployment solution(e.g. SCCM)
4. Run an OS Optimization tool to optimize and reduce the overall footprint of the OS image.
5. Sysprep the OS and upload the resulting image to Azure Compute Gallery.

Pros:
- Very flexible
- Easily customizable
- Existing scripting/imaging knowledge and experience can be applied to achieve this

Cons:
- Not a native cloud solution
- Not everyone is good at a scripting language. So maintaining it within a team can be challenging

Personally, I've used this approach to great success. The script utilized existing imaging process using SCCM to create the golden image.

Stijn Denruyter has documented a great working script here: [Automate AVD image creation with MEMCM and PowerShell](https://blog.stijndenruyter.be/2021/12/29/automate-avd-image-creation-with-memcm-and-powershell/)


### Azure Image Builder

Azure Image Builder is an Azure native tool which can be used to create custom Windows and Linux images. You can use Azure Powershell, the Azure portal, Azure CLI, and Azure Resource Manager(ARM) templates to automate the image creation
It can also be integrated with DevOps pipelines.

Microsoft Learn documents a step-by-step approach for creating a Windows image using Image Builder here: [Create a Windows VM with VM Image Builder by using PowerShell](https://learn.microsoft.com/en-us/azure/virtual-machines/windows/image-builder-powershell)

Pros:
- Free to use. Pay only for the Azure resources you create.
- Can be used with a GUI in the Azure portal
- Can automatically run new image builds when a new version of the base image becomes available.
- Images can be validated programmatically. 

Cons:
- Each customizations needs to be separate Powershell or shell script.
- The customizations are limited, so most things have to be scripted out.

### AVD Custom Image Templates

Custom image templates feature uses the aforementioned Azure Image Builder and is tailored for AVD.

A guide is provided at Microsoft Learn: [Create an Azure Virtual Desktop image by using VM Image Builder and PowerShell](https://learn.microsoft.com/en-us/azure/virtual-machines/windows/image-builder-virtual-desktop)

Pros:
- Aligned closely with the Azure Image Builder
- Customized for AVD

Cons:
- Same as for Azure Image Builder

### DevOps pipeline workflow

A DevOps workflow can be used for creating images. The approach can differ but the overall process is the same. In fact, most of the options mentioned above can be run with a DevOps pipeline, which is basically a sequence of steps running on a VM.

The approaches can include:
- Use Azure Powershell steps in the pipeline to run the image creation script
- Use Hashicorp Packer to build images.
- Use IaC tools like ARM/Bicep templates or Terraform to create the infra and configuration management tools like Ansible, Chef or Puppet to configure the OS before capturing.

In my opinion, IaC tools like ARM templates or Terraform are not well-suited for image creation workflows. They are better for deploying infra instead of customizing OS. 