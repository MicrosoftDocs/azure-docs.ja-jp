---
title: RBAC と Azure CLI を使用して Azure リソースへのアクセスを管理する | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) と Azure CLI を使用してユーザー、グループ、アプリケーションの Azure リソースへのアクセスを管理する方法について説明します。 具体的には、アクセス権の一覧表示、付与、削除などを取り上げます。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1b898f42fa6f66fba7c84daa67769249642bd986
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996492"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>RBAC と Azure CLI を使用して Azure リソースへのアクセスを管理する

[ロールベースのアクセス制御 (RBAC)](overview.md) は、Azure のリソースに対するアクセスを管理するための手法です。 この記事では、RBAC と Azure CLI を使用してユーザー、グループ、アプリケーションのアクセス権を管理する方法を説明します。

## <a name="prerequisites"></a>前提条件

アクセスを管理するには、次のいずれかが必要です。

* [Azure Cloud Shell の Bash](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>ロールの一覧表示

使用可能なすべてのロール定義を一覧表示するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用します。

```azurecli
az role definition list
```

次の例では、使用可能なすべてのロール定義の名前と説明を一覧表示します。

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

次の例では、すべての組み込みのロール定義を一覧表示します。

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>ロール定義を一覧表示する

ロール定義を一覧表示するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用します。

```azurecli
az role definition list --name <role_name>
```

次の例では、"*共同作成者*" ロール定義を一覧表示します。

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>ロールのアクションの表示

次の例では、"*共同作成者*" ロールの *actions* および *notActions* を一覧表示します。

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

次の例では、"*仮想マシンの共同作成者*" ロールの動作を一覧表示します。

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>アクセス権の表示

RBAC でアクセス権を一覧表示するには、ロールの割り当てを一覧表示します。

### <a name="list-role-assignments-for-a-user"></a>ユーザーのロールの割り当ての表示

特定のユーザーのロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。

```azurecli
az role assignment list --assignee <assignee>
```

既定では、サブスクリプションをスコープとする直接割り当てのみが表示されます。 リソースまたはグループをスコープとする割り当てを表示する場合は `--all` を使用し、継承された割り当てを表示する場合は `--include-inherited` を使用します。

次の例では、*patlong\@contoso.com* ユーザーに直接割り当てられているロールの割り当てを一覧表示します。

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>リソース グループをスコープとするロールの割り当てを一覧表示する

リソース グループ スコープに存在するロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。

```azurecli
az role assignment list --resource-group <resource_group>
```

次の例では、*pharma-sales* リソース グループに対するロールの割り当てを一覧表示します。

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>サブスクリプションをスコープとするロールの割り当てを一覧表示する

サブスクリプション スコープに存在するすべてのロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。 サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで確認するか、[az account list](/cli/azure/account#az-account-list) を使用できます。

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>管理グループをスコープとするロールの割り当てを一覧表示する

管理グループ スコープに存在するすべてのロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) を使用します。 管理グループ ID を取得するには、Azure portal の **[管理グループ]** ブレードで確認するか、[az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list) を使用します。

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="grant-access"></a>アクセス権の付与

RBAC でアクセス権を付与するには、ロールの割り当てを作成します。

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>リソース グループをスコープとするユーザーのロールの割り当てを作成する

リソース グループ スコープでユーザーにアクセス権を付与するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

次の例では、*pharma-sales* リソース グループのスコープで、*patlong\@contoso.com* ユーザーに "*仮想マシンの共同作成者*" ロールを付与します。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>一意のロール ID を使用してロールの割り当てを作成する

ロール名が変更されるときがあります。たとえば次のような場合です。

- 独自のカスタム ロールを使用していて、名前を変更することにした場合。
- 名前に **(プレビュー)** が含まれるプレビュー ロールを使用している場合。 ロールがリリースされると、ロールの名前が変更されます。

> [!IMPORTANT]
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ロールの名前が変更される場合でも、ロールの ID は変わりません。 スクリプトまたはオートメーションを使用してロールの割り当てを作成する場合は、ロール名ではなく一意のロール ID を使用するのがベスト プラクティスです。 そうすれば、ロールの名前が変更されても、スクリプトが動作する可能性が高くなります。

ロール名ではなく一意のロール ID を使用してロールの割り当てを作成するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

次の例では、*pharma-sales* リソース グループ スコープで、*patlong\@contoso.com* ユーザーに[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールが付与されます。 一意のロール ID を取得するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用するか、「[Azure リソースの組み込みロール](built-in-roles.md)」を参照してください。

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>グループのロールの割り当てを作成する

グループにアクセス権を付与するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。 グループの ID を取得するには、[az ad group list](/cli/azure/ad/group#az-ad-group-list) または [az ad group show](/cli/azure/ad/group#az-ad-group-show) を使用できます。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

次の例では、*Ann Mack Team* グループ (ID 22222222-2222-2222-2222-222222222222) に、サブスクリプション スコープで "*閲覧者*" ロールが割り当てられます。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

次の例では、*Ann Mack Team* グループ (ID 22222222-2222-2222-2222-222222222222) に、リソース スコープで *pharma-sales-project-network* という名前の仮想ネットワークに対する "*仮想マシン共同作成者*" ロールが割り当てられます。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>リソース グループをスコープとするアプリケーションのロールの割り当てを作成する

アプリケーションにアクセス権を付与するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。 アプリケーションのオブジェクト ID を取得するには、[az ad app list](/cli/azure/ad/app#az-ad-app-list) または [az ad app show](/cli/azure/ad/app#az-ad-app-show) を使用できます。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

次の例では、*pharma-sales* リソース グループのスコープで、オブジェクト ID 44444444-4444-4444-4444-444444444444 を使ってアプリケーションに "*仮想マシンの共同作成者*" ロールを割り当てています。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>サブスクリプションをスコープとするユーザーのロールの割り当てを作成する

サブスクリプション スコープでユーザーにアクセス権を付与するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。 サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで確認するか、[az account list](/cli/azure/account#az-account-list) を使用できます。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

次の例では、*annm\@example.com* ユーザーに、サブスクリプション スコープで "*閲覧者*" ロールが割り当てられます。

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>管理グループをスコープとするユーザーのロールの割り当てを作成する

管理グループ スコープでユーザーにアクセス権を付与するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。 管理グループ ID を取得するには、Azure portal の **[管理グループ]** ブレードで確認するか、[az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list) を使用します。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

次の例では、*alain\@example.com* ユーザーに、管理グループ スコープで "*課金データ閲覧者*" ロールが割り当てられます。

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="remove-access"></a>アクセス権の削除

RBAC でアクセス権を削除するには、[az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) を使用してロールの割り当てを削除します。

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

次の例では、*pharma-sales* リソース グループの *patlong\@contoso.com* ユーザーから、"*仮想マシンの共同作成者*" ロールの割り当てを削除します。

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

次の例では、サブスクリプション スコープで "*閲覧者*" ロールが *Ann Mack Team* グループ (ID 22222222-2222-2222-2222-222222222222 ) から削除されます。 グループの ID を取得するには、[az ad group list](/cli/azure/ad/group#az-ad-group-list) または [az ad group show](/cli/azure/ad/group#az-ad-group-show) を使用できます。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

次の例では、管理グループ スコープで *alain\@example.com* ユーザーから "*課金データ閲覧者*" ロールが削除されます。 管理グループの ID を取得するには、[az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list) を使用できます。

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>次の手順

- [チュートリアル:Azure CLI を使用して Azure リソースのカスタム ロールを作成する](tutorial-custom-role-cli.md)
- [Azure CLI を使用して Azure のリソースとリソース グループを管理する](../azure-resource-manager/cli-azure-resource-manager.md)
