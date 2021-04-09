---
title: Azure Resource Manager テンプレートを使用して Azure カスタム ロールを作成または更新する - Azure RBAC
description: Azure Resource Manager テンプレート (ARM テンプレート) と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して Azure カスタム ロールを作成または更新する方法について説明します。
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 0626a9e36d05ac9cb51f62652dbe6f3133bbc6d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095906"
---
# <a name="create-or-update-azure-custom-roles-using-an-arm-template"></a>ARM テンプレートを使用して Azure カスタム ロールを作成または更新する

[Azure の組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自の[カスタム ロール](custom-roles.md)を作成することができます。 この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、カスタム ロールを作成または更新する方法について説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

カスタム ロールを作成するには、ロール名、アクセス許可、およびロールを使用できる場所を指定します。 この記事では、サブスクリプション スコープ以下で割り当てることができる、リソースのアクセス許可を持つ _Custom Role - RG Reader_ という名前のロールを作成します。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

カスタム ロールを作成するには、以下が必要です。

- [所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、カスタム ロールを作成するためのアクセス許可。

## <a name="review-the-template"></a>テンプレートを確認する

この記事で使用されているテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/create-role-def)からのものです。 テンプレートには、4 つのパラメーターとリソース セクションがあります。 4 つのパラメーターは次のとおりです。

- 既定値が `["Microsoft.Resources/subscriptions/resourceGroups/read"]` であるアクションの配列。
- 空の既定値が指定された `notActions` の配列。
- 既定値が `Custom Role - RG Reader` であるロールの名前。
- 既定値が `Subscription Level Deployment of a Role Definition` であるロールの説明。

このカスタム ロールを割り当てることができるスコープは、現在のサブスクリプションに設定されます。

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

テンプレート内に定義されているリソース:

- [Microsoft.Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

前述のテンプレートをデプロイするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. PowerShell 用の Azure Cloud Shell を開きます。

1. 次のスクリプトをコピーして Cloud Shell に貼り付けます。

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"
    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. デプロイの場所を入力します (`centralus` など)。

1. カスタム ロールのアクションの一覧をコンマ区切りの一覧として入力します (`Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read` など)。

1. 必要に応じて、Enter キーを押して `New-AzDeployment` コマンドを実行します。

    [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) コマンドを実行すると、テンプレートがデプロイされ、カスタム ロールが作成されます。

    次のような出力が表示されます。

    ```azurepowershell-interactive
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions

    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0

    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

カスタム ロールが作成されたことを確認するには、次の手順に従います。

1. [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) コマンドを実行して、カスタム ロールを一覧表示します。

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    次のような出力が表示されます。

    ```azurepowershell-interactive
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. Azure portal で、お使いのサブスクリプションを開きます。

1. 左側のメニューで **[アクセス制御 (IAM)]** を選択します。

1. **[ロール]** タブを選択します。

1. **[種類]** リストを **[CustomRole]** に設定します。

1. **Custom Role - RG Reader** ロールが表示されていることを確認します。

   ![Azure portal 内の新しいカスタム ロール](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="update-a-custom-role"></a>カスタム ロールの更新

カスタム ロールを作成する場合と同じく、テンプレートを使用して既存のカスタム ロールを更新できます。 カスタム ロールを更新するには、更新するロールを指定する必要があります。

ここでは、カスタム ロールを更新するために、以前のクイック スタート テンプレートに対して行う必要がある変更について説明します。

- ロール ID をパラメーターとして含めます。
    ```json
        ...
        "roleDefName": {
          "type": "string",
          "metadata": {
            "description": "ID of the role definition"
          }
        ...
    ```

- ロール定義にロール ID パラメーターを含めます。

    ```json
      ...
      "resources": [
        {
          "type": "Microsoft.Authorization/roleDefinitions",
          "apiVersion": "2018-07-01",
          "name": "[parameters('roleDefName')]",
          "properties": {
            ...
    ```

テンプレートをデプロイする方法の例を次に示します。

```azurepowershell
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
[string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
$actions = $actions.Split(',')
$roleDefName = Read-Host -Prompt "Enter the role ID to update"
$templateFile = "rg-reader-update.json"
New-AzDeployment -Location $location -TemplateFile $templateFile -actions $actions -roleDefName $roleDefName
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

カスタム ロールを削除するには、次の手順に従います。

1. 次のコマンドを実行して、カスタム ロールを削除します。

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. 「**Y**」と入力して、カスタム ロールを削除することを確認します。

## <a name="next-steps"></a>次のステップ

- [Azure ロールの定義について](role-definitions.md)
- [クイックスタート:Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる](quickstart-role-assignments-template.md)
- [ARM テンプレートのドキュメント](../azure-resource-manager/templates/index.yml)
