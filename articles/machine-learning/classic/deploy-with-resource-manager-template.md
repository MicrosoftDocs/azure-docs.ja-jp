---
title: 'ML Studio (classic): Azure Resource Manager を使用してワークスペースをデプロイする - Azure'
description: Azure Resource Manager テンプレートを使用して Azure Machine Learning Studio (クラシック) 用のワークスペースをデプロイする方法です
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-azurepowershell
ms.date: 02/05/2018
ms.openlocfilehash: ee0bee4857d410781561e6f7f6a7eae0509d1138
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520036"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Azure Resource Manager を使用して Azure Machine Learning Studio (クラシック) ワークスペースをデプロイする

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Azure Resource Manager デプロイ テンプレートは、検証および再試行メカニズムで相互接続されたコンポーネントをデプロイできるように、スケーラブルな方法を提供し、時間を節約します。 Azure Machine Learning Studio (クラシック) ワークスペースを設定するには、たとえば、Azure ストレージ アカウントを構成してから、ワークスペースをデプロイする必要があります。 この作業を何百ものワークスペースに対して手動で行うことを想像してください。 代わりに Azure Resource Manager テンプレートを使用すれば、もっと簡単に Studio (クラシック) ワークスペースとそのすべての依存関係をデプロイできます。 この記事では、このプロセスを順を追って説明します。 Azure Resource Manager の概要については、「[Azure Resource Manager の概要](../../azure-resource-manager/management/overview.md)」を参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>詳細な手順: Machine Learning ワークスペースの作成
Azure リソース グループを作成し、Resource Manager テンプレートを使用して、新しい Azure ストレージ アカウントと新しい Azure Machine Learning Studio (クラシック) ワークスペースをデプロイします。 デプロイが完了したら、作成されたワークスペースに関する重要な情報 (プライマリ キー、workspaceID、およびワークスペースへの URL) を出力します。

### <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの作成

Machine Learning ワークスペースには Azure ストレージ アカウントが必要です。このアカウントには、自身にリンクされているデータセットが格納されます。
次のテンプレートでは、リソース グループの名前を使用して、ストレージ アカウント名とワークスペース名を生成します。  また、ワークスペースを作成するときに、ストレージ アカウント名をプロパティとして使用します。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   この手順では、残りの手順を完了するのに必要なモジュールをダウンロードしてインストールします。 これは、PowerShell コマンドを実行している環境で一度だけ実行する必要があります。

* Azure に対して認証します

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
この手順は、セッションごとに繰り返し実行する必要があります。 認証されると、サブスクリプション情報が表示されます。

![Azure アカウント](./media/deploy-with-resource-manager-template/azuresubscription.png)

これで Azure にアクセスできました。次はリソース グループを作成します。

* リソース グループを作成する

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

リソース グループが正しくプロビジョニングされていることを確認します。 **ProvisioningState** が "Succeeded" になっているはずです。
リソース グループ名は、ストレージ アカウント名を生成するときにテンプレートによって使用されます。 ストレージ アカウント名の長さは 3 ～ 24 文字で、使用できるのは数字と小文字のみです。

![リソース グループ](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* リソース グループのデプロイメントを使用して、新しい Machine Learning ワークスペースをデプロイします。

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

デプロイが完了すると、デプロイしたワークスペースのプロパティに簡単にアクセスできます。 たとえば、プライマリ キー トークンにアクセスできます。

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

既存のワークスペースのトークンを取得するもう 1 つの方法として、Invoke-AzResourceAction コマンドを使用します。 たとえば、すべてのワークスペースのプライマリ トークンとセカンダリ トークンを一覧表示できます。

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
ワークスペースをプロビジョニングしたら、[Azure Machine Learning Studio (クラシック) 用の PowerShell モジュール](https://aka.ms/amlps)を使用して、Azure Machine Learning Studio (クラシック) タスクを自動化することもできます。

## <a name="next-steps"></a>次のステップ

* [Azure Resource Manager テンプレートの作成](../../azure-resource-manager/templates/template-syntax.md)について確認します。
* [Azure クイックスタート テンプレート リポジトリ](https://github.com/Azure/azure-quickstart-templates)を確認します。
* [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39)のビデオを見ます。
* [Resource Manager テンプレート リファレンス ヘルプ](/azure/templates/microsoft.machinelearning/allversions)を参照してください

<!--Link references-->