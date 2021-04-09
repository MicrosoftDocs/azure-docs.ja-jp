---
title: Azure CLI を使用して Azure カスタム ロールを作成または更新する - Azure RBAC
description: Azure CLI と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure カスタム ロールを一覧表示、作成、更新、または削除する方法を学習します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 31dabcf77f0db76047919fa76d00f1c5ed3c96d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369142"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Azure CLI を使用して Azure カスタム ロールを作成または更新する

> [!IMPORTANT]
> `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure の組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 この記事では、Azure CLIを使用してカスタムロールを一覧表示、作成、更新、または削除する方法について説明します

カスタム ロールの作成方法に関するステップバイステップのチュートリアルが必要な場合は、「[チュートリアル:Azure CLI を使用して Azure カスタム ロールを作成する](tutorial-custom-role-cli.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

カスタム ロールを作成するには、次のものが必要です。

- [所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、カスタム ロールを作成するためのアクセス許可
- [Azure Cloud Shell](../cloud-shell/overview.md) または [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示

割り当てに使用できるカスタム ロールを一覧表示するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用します。 次の例では、現在のサブスクリプションのすべてのカスタム ロールを一覧表示します。

```azurecli
az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
```

```json
[
  {
    "roleName": "My Management Contributor",
    "type": "CustomRole"
  },
  {
    "roleName": "My Service Reader Role",
    "type": "CustomRole"
  },
  {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole"
  }
]
```

## <a name="list-a-custom-role-definition"></a>カスタム ロールの定義の一覧表示

カスタム ロールの定義を一覧表示するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用します。 これは、組み込みロールに使用するコマンドと同じです。

```azurecli
az role definition list --name {roleName}
```

次の例では、"*仮想マシン オペレーター*" ロール定義を一覧表示します。

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

次の例では、"*仮想マシン オペレーター*" ロールのアクションのみを一覧表示します。

```azurecli
az role definition list --name "Virtual Machine Operator" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ]
]
```

## <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、[az role definition create](/cli/azure/role/definition#az-role-definition-create) を使用します。 ロール定義には、JSON 記述を含むファイルへの JSON 記述またはパスを指定できます。

```azurecli
az role definition create --role-definition {roleDefinition}
```

次の例では、"*仮想マシン オペレーター*" というカスタム ロールが作成されます。 このカスタム ロールは、*Microsoft.Compute*、*Microsoft.Storage*、*Microsoft.Network* リソース プロバイダーのすべての読み取り操作に対するアクセス許可を割り当てて、仮想マシンの起動、再起動、監視を行うためのアクセス許可を割り当ています。 このカスタム ロールは、2 つのサブスクリプションで使うことができます。 この例では、入力として JSON ファイルを使用します。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>カスタム ロールの更新

カスタム ロールを更新するには、最初に [az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用して、ロール定義を取得します。 次に、必要に応じてロール定義を変更します。 最後に、[az role definition update](/cli/azure/role/definition#az-role-definition-update) を使用して、更新されたロール定義を保存します。

```azurecli
az role definition update --role-definition {roleDefinition}
```

次の例では、*Microsoft.Insights/diagnosticSettings/* 操作が `Actions` に追加され、管理グループが *仮想マシン オペレーター* の `AssignableScopes` に追加されます。 `AssignableScopes` への管理グループの追加は、現在プレビューの段階です。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>カスタム ロールの削除

カスタム ロールを削除するには、[az role definition delete](/cli/azure/role/definition#az-role-definition-delete) を使用します。 削除するロールを指定するには、ロール名またはロール ID を使用します。 ロール ID を決定するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用します。

```azurecli
az role definition delete --name {roleNameOrId}
```

次の例では、*仮想マシン オペレーター* カスタム ロールが削除されます。

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure CLI を使用して Azure カスタム ロールを作成する](tutorial-custom-role-cli.md)
- [Azure カスタム ロール](custom-roles.md)
- [Azure リソース プロバイダーの操作](resource-provider-operations.md)