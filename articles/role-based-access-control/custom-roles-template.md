---
title: Azure Resource Manager テンプレートを使用して Azure カスタム ロールを作成する - Azure RBAC
description: Azure Resource Manager テンプレートと Azure ロールベースのアクセス制御 (Azure RBAC) を使用して Azure カスタム ロールを作成する方法について説明します。
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85397998"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Azure カスタム ロールを作成する

[Azure の組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自の[カスタム ロール](custom-roles.md)を作成することができます。 この記事では、Azure Resource Manager テンプレートを使用して、カスタム ロールを作成する方法について説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>前提条件

カスタム ロールを作成するには、以下が必要です。

- [所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、カスタム ロールを作成するためのアクセス許可

## <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、ロール名、アクセス許可、およびロールを使用できる場所を指定します。 この記事では、サブスクリプション スコープ以下で割り当てることができる、リソースのアクセス許可を持つ "Custom Role - RG Reader" という名前のロールを作成します。

### <a name="review-the-template"></a>テンプレートを確認する

この記事で使用されているテンプレートは [Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def)からのものです。 テンプレートには、4 つのパラメーターとリソース セクションがあります。 4 つのパラメーターは次のとおりです。

- 既定値の ["Microsoft.Resources/subscriptions/resourceGroups/read"] が指定されたアクションの配列
- 空の既定値が指定された notActions の配列
- 既定値の "Custom Role - RG Reader" が指定されたロール名
- 既定値の "Subscription Level Deployment of a Role Definition" (ロールの定義のサブスクリプション レベルのデプロイ) が指定されたロールの説明

テンプレート内に定義されているリソース:

- [Microsoft.Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

このカスタム ロールを割り当てることができるスコープは、現在のサブスクリプションに設定されます。

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>テンプレートのデプロイ

前述のテンプレートをデプロイするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. PowerShell 用の Azure Cloud Shell を開きます。

1. 次のスクリプトをコピーして Cloud Shell に貼り付けます。

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. デプロイの場所を入力します (*centralus* など)。

1. カスタム ロールのアクションの一覧をコンマ区切りの一覧として入力します (*Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read* など)。

1. 必要に応じて、Enter キーを押し、New-AzDeployment コマンドを実行します。

    [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) コマンドを実行すると、テンプレートがデプロイされ、カスタム ロールが作成されます。

    次のような出力が表示されます。

    ```azurepowershell
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

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    次のような出力が表示されます。

    ```azurepowershell
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

1. 左側のメニューで **[アクセス制御 (IAM)]** をクリックします。

1. **[ロール]** タブをクリックします。

1. **[種類]** リストを **[CustomRole]** に設定します。

1. **Custom Role - RG Reader** ロールが表示されていることを確認します。

   ![Azure portal 内の新しいカスタム ロール](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

カスタム ロールを削除するには、次の手順に従います。

1. 次のコマンドを実行して、カスタム ロールを削除します。

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. 「**Y**」と入力して、カスタム ロールを削除することを確認します。

## <a name="next-steps"></a>次のステップ

- [Azure ロールの定義について](role-definitions.md)
- [クイック スタート: Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加する](quickstart-role-assignments-template.md)
- [ARM テンプレートのドキュメント](../azure-resource-manager/templates/index.yml)
