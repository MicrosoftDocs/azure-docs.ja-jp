---
title: "PowerShell とテンプレートを使用してリソースをデプロイする | Microsoft Docs"
description: "Azure Resource Manager と Azure PowerShell を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: ea42f634fabbbaf13f0d97c71a9cb8d1d8102dea
ms.lasthandoff: 03/16/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [ポータル](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

このトピックでは、Azure PowerShell と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。 テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。 テンプレートがストレージ アカウントに存在する場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

## <a name="deploy"></a>デプロイ
* デプロイを速やかに開始するには、次のコマンドを使用してインライン パラメーターを含むローカル テンプレートをデプロイします。

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {your-subscription-ID}
  New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
  ```

  デプロイが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のような結果を含むメッセージが表示されます。

  ```powershell
  ProvisioningState       : Succeeded
  ```

* `Set-AzureRmContext` コマンドレットは、既定のサブスクリプション以外のサブスクリプションを使用する場合にのみ必要です。 すべてのサブスクリプションとその ID を表示するには、次のコマンドを使用します。

  ```powershell
  Get-AzureRmSubscription
  ```

* 外部テンプレートをデプロイするには、**TemplateUri** パラメーターを使用します。

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
  ```

* パラメーター値をファイルで渡すには、**TemplateParameterFile** パラメーターを使用します。

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json -TemplateParameterFile c:\MyTemplates\storage.parameters.json
  ```

  パラメーター ファイルは次の形式にする必要があります。

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
        "storageNamePrefix": {
            "value": "contoso"
        },
        "storageSKU": {
            "value": "Standard_GRS"
        }
     }
   }
   ```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

完全モードを使用するには、**モード** パラメーターを使用します。

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="deploy-private-template-with-sas-token"></a>SAS トークンを使用してプライベートのテンプレートをデプロイする
SAS トークンを使用したデプロイの際に、テンプレートをストレージ アカウントに追加し、リンクできます。

> [!IMPORTANT]
> 次の手順に従うことにより、テンプレートを含む BLOB はアカウントの所有者だけがアクセスできるようになります。 ただし、BLOB の SAS トークンを作成すると、その URI を持つ誰もが BLOB にアクセスできるようになります。 もし別のユーザーが URI を傍受した場合、そのユーザーは、テンプレートにアクセスできます。 SAS トークンの使用はテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。
> 
> 

### <a name="add-private-template-to-storage-account"></a>ストレージ アカウントにプライベートのテンプレートを追加する
次の例では、プライベート ストレージ アカウントのコンテナーを設定し、テンプレートをアップロードします。
   
```powershell
New-AzureRmResourceGroup -Name ManageGroup -Location "South Central US"
New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}
New-AzureStorageContainer -Name templates -Permission Off
Set-AzureStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

### <a name="provide-sas-token-during-deployment"></a>デプロイ時に SAS トークンを指定する
ストレージ アカウントにプライベートのテンプレートをデプロイするため、SAS トークンを生成してテンプレートの URI に含めます。 デプロイの完了に必要な時間を確保できるように有効期限を設定します。
   
```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}
$templateuri = New-AzureStorageBlobSASToken -Container templates -Blob storage.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

リンクされたテンプレートでの SAS トークン使用例については、「 [Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

## <a name="parameters"></a>parameters
   
PowerShell コマンドのパラメーターのいずれかと名前が同じであるパラメーターがテンプレートに含まれている場合、そのパラメーターに値を指定するように求められます。 Azure PowerShell では、ご使用のテンプレートのパラメーターは、接尾辞 **FromTemplate** を付けて表します。 たとえば、テンプレート内の **ResourceGroupName** という名前のパラメーターは、[New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment) コマンドレットの **ResourceGroupName** パラメーターと競合します。 **ResourceGroupNameFromTemplate** に値を指定するように求められます。 一般的に、このような混乱を防ぐために、デプロイ処理に使用したパラメーターと同じ名前をパラメーターに付けないことが推奨されます。

同じデプロイ操作で、インライン パラメーターとローカル パラメーター ファイルを使用することができます。 たとえば、一部の値をローカル パラメーター ファイルで指定し、その他の値をデプロイ中にインラインで追加します。 ローカル パラメーター ファイルとインラインの両方でパラメーターの値を指定すると、インラインの値が優先されます。

ただし、外部パラメーター ファイルを使用する場合、他の値をインラインまたはローカル ファイルから渡すことはできません。 **TemplateParameterUri** パラメーターでパラメーター ファイルを指定すると、すべてのインライン パラメーターが無視されます。 すべてのパラメーター値を外部ファイル内で指定します。 パラメーター ファイルに含めることができない機密性の高い値がテンプレートに含まれている場合は、その値をキー コンテナーに追加するか、すべてのパラメーター値をインラインで動的に指定してください。

## <a name="debug"></a>デバッグ

デプロイ エラーのトラブルシューティングに役立つ可能性がある追加情報を記録する場合は、**DeploymentDebugLogLevel** パラメーターを使用します。 デプロイ操作と共に要求の内容、応答の内容、またはその両方を記録するように指定できます。
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

失敗したデプロイ操作に関する詳細情報を取得するには、以下を使用します。

```powershell
(Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。

## <a name="complete-deployment-script"></a>デプロイメント スクリプトの完了

次の例は、[テンプレートのエクスポート](resource-manager-export-template.md)機能により生成されたテンプレートをデプロイするための PowerShell スクリプトを示しています。

```powershell
<#
 .SYNOPSIS
    Deploys a template to Azure

 .DESCRIPTION
    Deploys an Azure Resource Manager template

 .PARAMETER subscriptionId
    The subscription id where the template will be deployed.

 .PARAMETER resourceGroupName
    The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

 .PARAMETER resourceGroupLocation
    Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

 .PARAMETER deploymentName
    The deployment name.

 .PARAMETER templateFilePath
    Optional, path to the template file. Defaults to template.json.

 .PARAMETER parametersFilePath
    Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
#>

param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "template.json",

 [string]
 $parametersFilePath = "parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @();
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
``` 

## <a name="next-steps"></a>次のステップ
* .NET クライアント ライブラリを使用したリソースのデプロイの例については、「[Deploy resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」 (.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ) を参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
* ソリューションを別の環境にデプロイする方法については、「 [Microsoft Azure の開発環境とテスト環境](solution-dev-test-environments.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。
* デプロイの自動化についての 4 回シリーズの解説については、「[Automating application deployments to Azure Virtual Machines (Azure Virtual Machines へのアプリケーションのデプロイの自動化)](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 このシリーズでは、アプリケーションのアーキテクチャ、アクセスとセキュリティ、可用性と拡張性、およびアプリケーションのデプロイについて説明しています。


