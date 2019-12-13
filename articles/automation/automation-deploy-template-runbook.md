---
title: Azure Automation Runbook に Azure Resource Manager テンプレートをデプロイする
description: Azure Storage に格納されている Azure Resource Manager テンプレートを Runbook からデプロイする方法
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: 922a4e8d98405de9e2b8420da4abf0e157011546
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850926"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Azure Automation PowerShell Runbook に Azure Resource Manager テンプレートをデプロイする

[Azure Resource Management テンプレート](../azure-resource-manager/resource-manager-create-first-template.md)を使用して、Azure リソースをデプロイする [Azure Automation PowerShell Runbook](automation-first-runbook-textual-powershell.md) を記述できます。

これにより、Azure リソースのデプロイを自動化できます。 Azure Storage など、セキュリティで保護された一元的な場所に Resource Manager テンプレートを維持することができます。

この記事では、[Azure Storage](../storage/common/storage-introduction.md) に格納されている Resource Manager テンプレートを使用して新しい Azure ストレージ アカウントをデプロイする PowerShell Runbook を作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。
* [Automation アカウント](automation-sec-configure-azure-runas-account.md)。Runbook の保存と Azure リソースの認証に使用します。  このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Resource Manager テンプレートを格納する [Azure Storage アカウント](../storage/common/storage-create-storage-account.md)
* ローカル コンピューターにインストールされている Azure Powershell。 Azure PowerShell の取得方法の詳細については、[Azure PowerShell のインストールと構成の方法](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)に関するページを参照してください。

## <a name="create-the-resource-manager-template"></a>Resource Manager テンプレートの作成

この例では、新しい Azure ストレージ アカウントをデプロイする Resource Manager テンプレートを使用します。

テキスト エディターで、次のテキストをコピーします。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

ファイルを `TemplateTest.json` としてローカルに保存します。

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Resource Manager テンプレートを Azure Storage に保存する

PowerShell を使用して Azure Storage ファイル共有を作成し、`TemplateTest.json` ファイルをアップロードします。
Azure Portal でファイル共有を作成し、ファイルをアップロードする方法については、[Windows での Azure File Storage の概要](../storage/files/storage-dotnet-how-to-use-files.md)に関する記事をご覧ください。

ローカル コンピューターで PowerShell を起動し、次のコマンドを実行して、ファイル共有を作成し、そのファイル共有に Resource Manager テンプレートをアップロードします。

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>PowerShell Runbook スクリプトの作成

`TemplateTest.json` ファイルを Azure Storage から取得し、新しい Azure Storage アカウントを作成するテンプレートをデプロイする PowerShell スクリプトを作成します。

テキスト エディターで、次のテキストを貼り付けます。

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

ファイルを `DeployTemplate.ps1` としてローカルに保存します。

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Runbook を Azure Automation アカウントにインポートし、公開する

PowerShell を使用して Runbook を Azure Automation アカウントにインポートし、その Runbook を公開します。
Azure portal で Runbook をインポートおよび公開する方法については、「[Azure Automation で Runbook を管理する](manage-runbooks.md)」をご覧ください。

`DeployTemplate.ps1` を Automation アカウントに PowerShell Runbook としてインポートするには、次の PowerShell コマンドを実行します。

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Runbook の起動

[Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) コマンドレットを呼び出して Runbook を起動します。

Azure Portal で Runbook を起動する方法については、「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」をご覧ください。

PowerShell コンソールで次のコマンドを実行します。

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Runbook が実行されます。`$job.Status` を実行してその状態を確認できます。

Runbook は、Resource Manager テンプレートを取得し、それを使用して新しい Azure Storage アカウントをデプロイします。
次のコマンドを実行して、新しいストレージ アカウントが作成されたことを確認できます。
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>まとめ

これで完了です。 Azure Automation と Azure Storage、およびすべての Azure リソースをデプロイする Resource Manager テンプレートを使用できます。

## <a name="next-steps"></a>次の手順

* Resource Manager テンプレートについて詳しくは、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。
* Azure Storage の概要については、「[Microsoft Azure Storage の概要](../storage/common/storage-introduction.md)」をご覧ください。
* 役に立つその他の Azure Automation Runbook を探すには、「[Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](automation-runbook-gallery.md)」をご覧ください。
* 役に立つその他の Resource Manager テンプレートを探すには、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)」をご覧ください。


