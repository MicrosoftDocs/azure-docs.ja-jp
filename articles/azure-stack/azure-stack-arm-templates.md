---
title: Use Azure Resource Manager templates in Azure Stack | Microsoft Docs
description: Learn how to use Azure Resource Manager templates in Azure Stack to provision resources.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: helaw
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: ab82107c74620522758b02e679689f6eb5013a7f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/03/2017

---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Use Azure Resource Manager templates in Azure Stack
Azure Resource Manager templates deploy and provision all the resources for your application in a single, coordinated operation. You can also redeploy templates to make changes to the resources in the resource group.

These templates can be deployed with the Microsoft Azure Stack portal, PowerShell, the command line, and Visual Studio.

The following quickstart templates are available on [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Deploy SharePoint (non-high availability)
Use the PowerShell DSC extension to create a SharePoint 2013 farm that includes the following resources:

* A virtual network
* Three storage accounts
* Two external load balancers
* One VM configured as a domain controller for a new forest with a single domain
* One VM configured as a SQL Server 2014 stand-alone server
* One VM configured as a one machine SharePoint 2013 farm

## <a name="deploy-ad-non-high-availability"></a>Deploy AD (non-high availability)
Use the PowerShell DSC extension to create an AD domain controller server that includes the following resources:

* A virtual network
* One storage account
* One external load balancer
* One VM configured as a domain controller for a new forest with a single domain

## <a name="deploy-adsql-non-high-availability"></a>Deploy AD/SQL (non-high availability)
Use the PowerShell DSC extension to create a SQL Server 2014 stand-alone server that includes the following resources:

* A virtual network
* Two storage accounts
* One external load balancer
* One VM configured as a domain controller for a new forest with a single domain
* One VM configured as a SQL Server 2014 stand-alone server

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server
Use the PowerShell DSC extension to configure an existing virtual machine Local Configuration Manager (LCM) and register it to an Azure Automation Account DSC Pull Server.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Create a virtual machine from a user image
Create a virtual machine from a custom user image. This template also deploys a virtual network (with DNS), public IP address, and a network interface.

## <a name="simple-vm"></a>Simple VM
Deploy a Windows VM that includes a virtual network (with DNS), public IP address, and a network interface.

## <a name="cancel-a-running-template-deployment"></a>Cancel a running template deployment
To cancel a running template deployment, use the `Stop-AzureRmResourceGroupDeployment` PowerShell cmdlet.

## <a name="next-steps"></a>Next steps
[Deploy templates with the portal](azure-stack-deploy-template-portal.md)

[Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md)


