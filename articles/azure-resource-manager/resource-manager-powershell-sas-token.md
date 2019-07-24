---
title: SAS トークンと PowerShell を使用した Azure テンプレートのデプロイ | Microsoft Docs
description: Azure Resource Manager と Azure PowerShell を使用して、SAS トークンで保護されているテンプレートのリソースを Azure にデプロイします。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: 818aa63ced56d7cf382536f10bb6150199ab74e9
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268942"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>SAS トークンと Azure PowerShell を使用してプライベートの Resource Manager テンプレートをデプロイする

テンプレートがストレージ アカウントに存在する場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。 このトピックでは、Azure PowerShell と Resource Manager テンプレートを使用して、デプロイ時に SAS トークンを設定する方法について説明します。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-private-template-to-storage-account"></a>ストレージ アカウントにプライベートのテンプレートを追加する

SAS トークンを使用したデプロイの際に、テンプレートをストレージ アカウントに追加し、リンクできます。

> [!IMPORTANT]
> 次の手順に従うことにより、テンプレートを含む BLOB はアカウントの所有者だけがアクセスできるようになります。 ただし、BLOB の SAS トークンを作成すると、その URI を持つ誰もが BLOB にアクセスできるようになります。 もし別のユーザーが URI を傍受した場合、そのユーザーは、テンプレートにアクセスできます。 SAS トークンの使用はテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。
> 
> 

次の例では、プライベート ストレージ アカウントのコンテナーを設定し、テンプレートをアップロードします。
   
```powershell
# create a storage account for templates
New-AzResourceGroup -Name ManageGroup -Location "South Central US"
New-AzStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzStorageContainer -Name templates -Permission Off
Set-AzStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>デプロイ時に SAS トークンを指定する
ストレージ アカウントにプライベートのテンプレートをデプロイするため、SAS トークンを生成してテンプレートの URI に含めます。 デプロイの完了に必要な時間を確保できるように有効期限を設定します。
   
```powershell
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

リンクされたテンプレートでの SAS トークン使用例については、「 [Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。


## <a name="next-steps"></a>次の手順
* テンプレートのデプロイ方法については、「[Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](resource-group-template-deploy.md)」を参照してください。
* テンプレートのデプロイ用の完全なサンプル スクリプトについては、[Resource Manager テンプレート スクリプトのデプロイ](resource-manager-samples-powershell-deploy.md)に関するページを参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
