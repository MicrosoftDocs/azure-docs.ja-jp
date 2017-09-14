---
title: Deploy templates with Visual Studio in Azure Stack | Microsoft Docs
description: Learn how to deploy templates with Visual Studio in Azure Stack.
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e9b467f47f166198d9790f19dbdd3d1d0fd79947
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Deploy templates in Azure Stack using Visual Studio

Use Visual Studio to deploy Azure Resource Manager templates to the Azure Stack development kit.

1. [Install and connect](azure-stack-install-visual-studio.md) to Azure Stack with Visual Studio.
2. Open Visual Studio.
3. Click **File**, click **New**, and in the **New Project** dialog box click **Azure Resource Group**.
4. Enter a **Name** for the new project, and then click **OK**.
5. In the **Select Azure Template** dialog box, change the *Show templates from this location* drop-down to **Azure Stack Quickstart**
6. Click **101-create-storage-account**, and then click **OK**.  
7. In your new project, you can see a list of the templates available by expanding the **Templates** node in the **Solution Explorer** pane.
8. In the **Solution Explorer** pane, right-click the name of your project, click **Deploy**, then click **New Deployment**.
9. In the **Deploy to Resource Group** dialog box, in the **Subscription** drop-down, select your Microsoft Azure Stack subscription.
10. In the **Resource Group** list, choose an existing resource group or create a new one.
11. In the **Resource group location** list, choose a location, and then click **Deploy**.
12. In the **Edit Parameters** dialog box, enter values for the parameters (which vary by template), and then click **Save**.

## <a name="next-steps"></a>Next steps
[Deploy templates with the command line](azure-stack-deploy-template-command-line.md)

[Develop templates for Azure Stack](azure-stack-develop-templates.md)


