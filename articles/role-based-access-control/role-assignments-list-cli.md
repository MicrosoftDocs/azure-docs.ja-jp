---
title: Azure CLI を使用して Azure ロールの割り当てを一覧表示する - Azure RBAC
description: Azure CLI と Azure ロールベースのアクセス制御 (RBAC) を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID がアクセスできるリソースを決定する方法について説明します。
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
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cc64e314a8acb035736df0521987cb78a7297326
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556927"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Azure CLI を使用して Azure ロールの割り当てを一覧表示する

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] この記事では、Azure CLI を使用してロールの割り当てを一覧表示する方法を説明します。

> [!NOTE]
> 組織で、[Azure の委任されたリソース管理](../lighthouse/concepts/azure-delegated-resource-management.md)を使用するサービス プロバイダーに管理機能を外部委託している場合、そのサービス プロバイダーによって承認されているロールの割り当てはここに表示されません。

## <a name="prerequisites"></a>前提条件

- [Azure Cloud Shell の Bash](../cloud-shell/overview.md) または [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>ユーザーのロールの割り当ての表示

特定のユーザーのロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。

```azurecli
az role assignment list --assignee {assignee}
```

既定では、現在のサブスクリプションのロールの割り当てのみが表示されます。 現在のサブスクリプションとその下のロールの割り当てを表示するには、`--all` パラメーターを追加します。 継承されたロールの割り当てを表示するには、`--include-inherited` パラメーターを追加します。

次の例では、*patlong\@contoso.com* ユーザーに直接割り当てられているロールの割り当てを一覧表示します。

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>リソース グループに対するロールの割り当ての一覧表示

リソース グループ スコープに存在するロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。

```azurecli
az role assignment list --resource-group {resourceGroup}
```

次の例では、*pharma-sales* リソース グループに対するロールの割り当てを一覧表示します。

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>サブスクリプションのロールの割り当ての一覧表示

サブスクリプション スコープに存在するすべてのロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。 サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで確認するか、[az account list](/cli/azure/account#az-account-list) を使用できます。

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

例:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>管理グループに対するロールの割り当ての一覧表示

管理グループ スコープに存在するすべてのロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。 管理グループ ID を取得するには、Azure portal の **[管理グループ]** ブレードで確認するか、[az account management-group list](/cli/azure/account/management-group#az-account-management-group-list) を使用します。

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

例:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>マネージド ID のロールの割り当ての一覧表示

1. システム割り当てまたはユーザー割り当てのマネージド ID のプリンシパル ID を取得します。

    ユーザー割り当てのマネージド ID のプリンシパル ID を取得するには、[az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) または [az identity list](/cli/azure/identity#az-identity-list) を使用します。

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    システム割り当てのマネージド ID のプリンシパル ID を取得するには、[az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) を使用します。

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. ロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。

    既定では、現在のサブスクリプションのロールの割り当てのみが表示されます。 現在のサブスクリプションとその下のロールの割り当てを表示するには、`--all` パラメーターを追加します。 継承されたロールの割り当てを表示するには、`--include-inherited` パラメーターを追加します。

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>次のステップ

- [Azure CLI を使用して Azure ロールを割り当てる](role-assignments-cli.md)