---
title: Azure Automation PowerShell Runbook に Azure Resource Manager テンプレートをデプロイする
description: この記事では、Azure Storage に格納されている Azure Resource Manager テンプレートを PowerShell Runbook からデプロイする方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: d9b443d1840840d3d6202140da235589c73453cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051398"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>PowerShell Runbook に Azure Resource Manager テンプレートをデプロイする

[Azure Resource Management テンプレート](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)を使用して、Azure リソースをデプロイする [Azure Automation PowerShell Runbook](./learn/automation-tutorial-runbook-textual-powershell.md) を記述できます。 このテンプレートを使用すると、Azure Automation を使用して Azure リソースのデプロイを自動化できます。 Azure Storage など、セキュリティで保護された一元的な場所に Resource Manager テンプレートを維持することができます。

この記事では、[Azure Storage](../storage/common/storage-introduction.md) に格納されている Resource Manager テンプレートを使用して新しい Azure ストレージ アカウントをデプロイする PowerShell Runbook を作成します。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。
* [Automation アカウント](./automation-security-overview.md)。Runbook の保存と Azure リソースの認証に使用します。 このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Resource Manager テンプレートを格納する [Azure Storage アカウント](../storage/common/storage-account-create.md)。
* ローカル コンピューターにインストールされている Azure PowerShell。 Azure PowerShell の取得方法の詳細については、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

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

ファイルを **TemplateTest.json** という名前でローカル環境に保存します。

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Resource Manager テンプレートを Azure Storage に保存する

PowerShell を使用して Azure Storage ファイル共有を作成し、**TemplateTest.json** ファイルをアップロードします。 Azure Portal でファイル共有を作成し、ファイルをアップロードする方法については、[Windows での Azure File Storage の概要](../storage/files/storage-dotnet-how-to-use-files.md)に関する記事をご覧ください。

ローカル コンピューターで PowerShell を起動し、次のコマンドを実行して、ファイル共有を作成し、そのファイル共有に Resource Manager テンプレートをアップロードします。

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>PowerShell Runbook スクリプトの作成

**TemplateTest.json** ファイルを Azure Storage から取得し、新しい Azure Storage アカウントを作成するテンプレートをデプロイする PowerShell スクリプトを作成します。

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
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
```

ファイルを **DeployTemplate.ps1** という名前でローカル環境に保存します。

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Runbook を Azure Automation アカウントにインポートし、公開する

PowerShell を使用して Runbook を Azure Automation アカウントにインポートし、その Runbook を公開します。 Azure portal で Runbook をインポートおよび公開する方法については、「[Azure Automation で Runbook を管理する](manage-runbooks.md)」をご覧ください。

**DeployTemplate.ps1** を Automation アカウントに PowerShell Runbook としてインポートするには、次の PowerShell コマンドを実行します。

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
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Runbook の起動

[Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) コマンドレットを呼び出して Runbook を起動します。 Azure Portal で Runbook を起動する方法については、「[Azure Automation での Runbook の開始](./start-runbooks.md)」をご覧ください。

PowerShell コンソールで次のコマンドを実行します。

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json'
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Runbook の実行後、ジョブ オブジェクト `$job.Status` のプロパティ値を取得することでその状態を確認できます。

Runbook は、Resource Manager テンプレートを取得し、それを使用して新しい Azure Storage アカウントをデプロイします。 次のコマンドを実行して、新しいストレージ アカウントが作成されたことを確認できます。

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>次のステップ

* Resource Manager テンプレートについて詳しくは、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」をご覧ください。
* Azure Storage の概要については、「[Microsoft Azure Storage の概要](../storage/common/storage-introduction.md)」をご覧ください。
* 役に立つその他の Azure Automation Runbook を探すには、[Azure Automation 用の Runbook とモジュールの使用](automation-runbook-gallery.md)に関するページをご覧ください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation#automation)」をご覧ください。
