---
title: チュートリアル - RBAC と Resource Manager テンプレートを使用して Azure リソースへのアクセス権をユーザーに付与する | Microsoft Docs
description: Azure Resource Manager テンプレートを使用することにより、ロールベースのアクセス制御 (RBAC) を使用して、Azure リソースへのアクセス権をユーザーに付与する方法について説明します。
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: edb20221862e6439b3bc574995f4037cbc95f8f9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668878"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>チュートリアル:RBAC と Resource Manager テンプレートを使用して Azure リソースへのアクセス権をユーザーに付与する

[ロールベースのアクセス制御 (RBAC)](overview.md) は、Azure のリソースに対するアクセスを管理するための手法です。 このチュートリアルでは、リソース グループを作成し、リソース グループ内で仮想マシンを作成および管理するアクセス権をユーザーに付与します。 このチュートリアルでは、Resource Manager テンプレートをデプロイしてアクセス権を付与するプロセスについて説明します。 Resource Manager テンプレートの開発に関する詳細については、[Resource Manager ドキュメント](/azure/azure-resource-manager/)と[テンプレート リファレンス](/azure/templates/microsoft.authorization/allversions
)をご覧ください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * リソース グループをスコープとするユーザーのアクセス権の付与
> * デプロイの検証
> * クリーンアップ

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

ロールの割り当てを追加および削除するには、以下が必要です。

* `Microsoft.Authorization/roleAssignments/write` および `Microsoft.Authorization/roleAssignments/delete` のアクセス許可 ([ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)や[所有者](built-in-roles.md#owner)など)

## <a name="grant-access"></a>アクセス権の付与

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/)からのものです。 その他の Azure の承認に関連するテンプレートは、[ここ](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization)にあります。

テンプレートをデプロイするには、 **[試してみる]** を選択し、Azure Cloud Shell を開いて、シェル ウィンドウに次の PowerShell スクリプトを貼り付けます。 コードを貼り付けるには、シェル ウィンドウを右クリックして、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>デプロイの検証

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 最後の手順で作成したリソース グループを開きます。 既定の名前は、**rg** が付加されたプロジェクト名です。
1. 左側のメニューから **[アクセス制御 (IAM)]** を選択します。
1. **[ロールの割り当て]** を選択します。 
1. **[名前]** で、最後の手順で入力した電子メール アドレスを入力します。 電子メール アドレスを持つユーザーが **[仮想マシン共同作成者]** のロールを割り当てられていることがわかります。

## <a name="clean-up"></a>クリーンアップ

最後の手順で作成したリソース グループを削除するには、 **[試してみる]** を選択し、Azure Cloud Shell を開いて、シェル ウィンドウに次の PowerShell スクリプトを貼り付けます。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:RBAC と Azure PowerShell を使用して Azure リソースへのアクセス権をユーザーに付与する](tutorial-role-assignments-user-powershell.md)