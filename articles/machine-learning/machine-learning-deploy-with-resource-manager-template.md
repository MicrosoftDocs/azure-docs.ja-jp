---
title: "Azure Resource Manager を使用した Machine Learning ワークスペースのデプロイ | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して Azure Machine Learning ワークスペースをデプロイする方法"
services: machine-learning
documentationcenter: 
author: ahgyger
manager: haining
editor: garye
ms.assetid: 4955ac4d-ff99-4908-aa27-69b6bfcc8e85
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/15/2017
ms.author: ahgyger
translationtype: Human Translation
ms.sourcegitcommit: 66fb3dc316ce25aea4dff4add5c25b7f0f56ad7a
ms.openlocfilehash: cf00c0c11f1572c0cca066ac1c0aac2a0f350393
ms.lasthandoff: 01/31/2017


---
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Azure Resource Manager を使用した Machine Learning ワークスペースのデプロイ
## <a name="introduction"></a>はじめに
Azure Resource Manager デプロイ テンプレートは、検証および再試行メカニズムで相互接続されたコンポーネントをデプロイできるように、スケーラブルな方法を提供し、時間を節約します。 Azure Machine Learning ワークスペースを設定するには、たとえば、Azure ストレージ アカウントを構成してから、ワークスペースをデプロイする必要があります。 この作業を何百ものワークスペースに対して手動で行うことを想像してください。 代わりに Azure Resource Manager テンプレートを使用すれば、もっと簡単に Azure Machine Learning ワークスペースとそのすべての依存関係をデプロイできます。 この記事では、このプロセスを順を追って説明します。 Azure Resource Manager の概要については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。

## <a name="step-by-step-create-a-machine-learning-workspace"></a>詳細な手順: Machine Learning ワークスペースの作成
Azure リソース グループを作成し、Resource Manager テンプレートを使用して、新しい Azure ストレージ アカウントと新しい Azure Machine Learning ワークスペースをデプロイします。 デプロイが完了したら、作成されたワークスペースに関する重要な情報 (プライマリ キー、workspaceID、およびワークスペースへの URL) を出力します。

### <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの作成
Machine Learning ワークスペースには Azure ストレージ アカウントが必要です。このアカウントには、自身にリンクされているデータセットが格納されます。
次のテンプレートでは、リソース グループの名前を使用して、ストレージ アカウント名とワークスペース名を生成します。  また、ワークスペースを作成するときに、ストレージ アカウント名をプロパティとして使用します。

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
このテンプレートを mlworkspace.json ファイルとして c:\temp\ に保存します。

### <a name="deploy-the-resource-group-based-on-the-template"></a>テンプレートに基づいてリソース グループをデプロイ
* PowerShell を開きます
* Azure Resource Manager と Azure サービス管理をインストールします  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   この手順では、残りの手順を完了するのに必要なモジュールをダウンロードしてインストールします。 これは、PowerShell コマンドを実行している環境で一度だけ実行する必要があります。   

* Azure に対して認証します  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
この手順は、セッションごとに繰り返し実行する必要があります。 認証されると、サブスクリプション情報が表示されます。

![Azure アカウント][1]

これで Azure にアクセスできました。次はリソース グループを作成します。

* リソース グループの作成

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

リソース グループが正しくプロビジョニングされていることを確認します。 **ProvisioningState** が "Succeeded" になっているはずです。
リソース グループ名は、ストレージ アカウント名を生成するときにテンプレートによって使用されます。 ストレージ アカウント名の長さは 3 ～ 24 文字で、使用できるのは数字と小文字のみです。

![リソース グループ][2]

* リソース グループのデプロイメントを使用して、新しい Machine Learning ワークスペースをデプロイします。

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

デプロイが完了すると、デプロイしたワークスペースのプロパティに簡単にアクセスできます。 たとえば、プライマリ キー トークンにアクセスできます。

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

既存のワークスペースのトークンを取得するもう&1; つの方法として、Invoke-AzureRmResourceAction コマンドを使用します。 たとえば、すべてのワークスペースのプライマリ トークンとセカンダリ トークンを一覧表示できます。

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
ワークスペースをプロビジョニングしたら、 [Azure Machine Learning の PowerShell モジュール](http://aka.ms/amlps)を使用して、Azure Machine Learning Studio タスクを自動化することもできます。

## <a name="next-steps"></a>次のステップ
* [Azure Resource Manager テンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)について確認します。 
* [Azure クイックスタート テンプレート リポジトリ](https://github.com/Azure/azure-quickstart-templates)を確認します。 
* [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39)のビデオを見ます。 

<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->

