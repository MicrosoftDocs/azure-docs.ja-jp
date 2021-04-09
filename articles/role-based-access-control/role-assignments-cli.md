---
title: Azure CLI を使用して Azure ロールを割り当てる - Azure RBAC
description: Azure CLI と Azure のロールベースのアクセス制御 (RBAC) を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: ee356f32b6799c6182ec1c9e061a35271a4bbc23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556970"
---
# <a name="assign-azure-roles-using-azure-cli"></a>Azure CLI を使用して Azure ロールを割り当てる

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] この記事では、Azure CLI を使用してロールを割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

ロールを割り当てるには、以下が必要です。

- `Microsoft.Authorization/roleAssignments/write` アクセス許可 ([ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)や[所有者](built-in-roles.md#owner)など)
- [Azure Cloud Shell の Bash](../cloud-shell/overview.md) または [Azure CLI](/cli/azure)

## <a name="steps-to-assign-an-azure-role"></a>Azure ロールを割り当てる手順

ロールの割り当ては、セキュリティ プリンシパル、ロールの定義、スコープの 3 つの要素で構成されています。

### <a name="step-1-determine-who-needs-access"></a>手順 1: アクセスが必要なユーザーを決定する

ロールを、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に割り当てることができます。 ロールを割り当てるには、オブジェクトの一意の ID の指定が必要になることがあります。 ID の形式は `11111111-1111-1111-1111-111111111111` です。 この ID は、Azure portal または Azure CLI を使用して取得できます。

**User**

Azure AD ユーザーの場合は、ユーザー プリンシパル名 (*patlong\@contoso.com* など) またはユーザー オブジェクト ID を取得します。 そのオブジェクト ID を取得するには、[az ad user show](/cli/azure/ad/user#az_ad_user_show) を使用できます。

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**グループ**

Azure AD グループの場合は、グループ オブジェクト ID が必要です。 そのオブジェクト ID を取得するには、[az ad group show](/cli/azure/ad/group#az_ad_group_show) または [az ad group list](/cli/azure/ad/group#az_ad_group_list) を使用できます。

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**サービス プリンシパル**

Azure AD サービス プリンシパル (アプリケーションによって使用される ID) の場合は、サービス プリンシパル オブジェクト ID が必要です。 そのオブジェクト ID を取得するには、[az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) を使用できます。 サービス プリンシパルの場合は、アプリケーション ID **ではなく**、オブジェクト ID を使用します。

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**管理対象 ID**

システム割り当てまたはユーザー割り当てのマネージド ID の場合は、オブジェクト ID が必要です。 そのオブジェクト ID を取得するには、[az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) を使用できます。

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

ユーザー割り当てマネージド ID の一覧を表示するだけの場合は、[az identity list](/cli/azure/identity#az_identity_list) を使用できます。

```azurecli
az identity list
```
    
### <a name="step-2-select-the-appropriate-role"></a>手順 2:適切なロールを選択する

アクセス許可はロールでまとめてグループ化されます。 複数の [Azure 組み込みロール](built-in-roles.md)の一覧から選択することも、独自のカスタム ロールを使用することもできます。 必要最小限の特権を持つアクセス権を付与するのがベスト プラクティスなので、より広範なロールを割り当てることは避けてください。

ロールの一覧を表示し、一意のロール ID を取得するには、[az role definition list](/cli/azure/role/definition#az_role_definition_list) を使用できます。

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

特定のロールの詳細を一覧表示する方法は次のとおりです。

```azurecli
az role definition list --name "{roleName}"
```

詳細については、「[Azure ロールの定義を一覧表示する](role-definitions-list.md#azure-cli)」を参照してください。
 
### <a name="step-3-identify-the-needed-scope"></a>手順 3:必要なスコープを特定する

Azure には、リソース、[リソース グループ](../azure-resource-manager/management/overview.md#resource-groups)、サブスクリプション、[管理グループ](../governance/management-groups/overview.md)という 4 つのレベルのスコープが用意されています。 必要最小限の特権を持つアクセス権を付与するのがベスト プラクティスなので、より広範なスコープのロールを割り当てることは避けてください。 スコープの詳細については、[スコープについての理解](scope-overview.md)に関する記事を参照してください。

**リソースのスコープ**

リソース スコープの場合は、リソースのリソース ID が必要です。 Azure portal でリソースのプロパティを見ることで、リソース ID を検索できます。 リソース ID の形式は次のとおりです。

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**リソース グループ スコープ**

リソース グループ スコープの場合は、リソース グループの名前が必要です。 Azure portal の **[リソース グループ]** ページで、または [az group list](/cli/azure/group#az_group_list) を使用して、名前を検索できます。

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**サブスクリプション スコープ** 

サブスクリプション スコープの場合は、サブスクリプション ID が必要です。 Azure portal の **[サブスクリプション]** ページで、または [az account list](/cli/azure/account#az_account_list) を使用して、ID を検索できます。

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**管理グループ スコープ** 

管理グループ スコープの場合は、管理グループ名が必要です。 Azure portal の **[管理グループ]** ページで、または [az account management-group list](/cli/azure/account/management-group#az_account_management_group_list) を使用して、名前を検索できます。

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-assign-role"></a>手順 4:ロールを割り当てる

ロールを割り当てるには、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) コマンドを使用します。 コマンドは、スコープに応じて、通常、次のいずれかの形式になります。

**リソースのスコープ**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**リソース グループ スコープ**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**サブスクリプション スコープ** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**管理グループ スコープ** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

次に、[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールをリソース グループ スコープでユーザーに割り当てる場合の出力例を示します。

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="assign-role-examples"></a>ロールの割り当ての例

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>ストレージ アカウント リソース スコープ内のすべての BLOB コンテナーにロールを割り当てる

[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールを、*storage12345* という名前のストレージ アカウントに対するリソース スコープで、オブジェクト ID が *55555555-5555-5555-5555-555555555555* のサービス プリンシパルに割り当てます。

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>特定の BLOB コンテナー リソース スコープにロールを割り当てる

[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールを、*blob-container-01* という名前の BLOB コンテナーに対するリソース スコープで、オブジェクト ID が *55555555-5555-5555-5555-555555555555* のサービス プリンシパルに割り当てます。

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>特定の仮想ネットワーク リソース スコープ内のグループにロールを割り当てる

[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールを、*pharma-sales-project-network* という名前の仮想ネットワークに対するリソース スコープで、ID が 22222222-2222-2222-2222-222222222222 の *Ann Mack Team* グループに割り当てます。

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>リソース グループ スコープでユーザーにロールを割り当てる

[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールを、*pharma-sales* リソース グループ スコープで、*patlong\@contoso.com* ユーザーに割り当てます。

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>リソース グループ スコープで一意のロール ID を使用してユーザーにロールを割り当てる

ロール名が変更されるときがあります。たとえば次のような場合です。

- 独自のカスタム ロールを使用していて、名前を変更することにした場合。
- 名前に **(プレビュー)** が含まれるプレビュー ロールを使用している場合。 ロールがリリースされると、ロールの名前が変更されます。

ロールの名前が変更される場合でも、ロールの ID は変わりません。 スクリプトまたはオートメーションを使用してロールの割り当てを作成する場合は、ロール名ではなく一意のロール ID を使用するのがベスト プラクティスです。 そうすれば、ロールの名前が変更されても、スクリプトが動作する可能性が高くなります。

次の例では、*pharma-sales* リソース グループ スコープで、*patlong\@contoso.com* ユーザーに [仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールが付与されます。

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-resource-group-scope"></a>リソース グループ スコープですべての BLOB コンテナーにロールを割り当てる

[ストレージ BLOB データ共同作成者](built-in-roles.md#storage-blob-data-contributor)ロールを、*Example-Storage-rg* リソース グループ スコープで、オブジェクト ID が *55555555-5555-5555-5555-555555555555* のサービス プリンシパルに割り当てます。

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

または、`--scope` パラメーターを使用して、完全修飾リソース グループを指定することもできます。

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>リソース グループ スコープでアプリケーションにロールを割り当てる

[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールを、*pharma-sales* リソース グループ スコープで、サービス プリンシパル オブジェクト ID が 44444444-4444-4444-4444-444444444444 のアプリケーションに割り当てます。

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-new-service-principal-at-a-resource-group-scope"></a>リソース グループ スコープで新しいサービス プリンシパルにロールを割り当てる

新しいサービス プリンシパルを作成し、そのサービス プリンシパルにロールをすぐに割り当てようとすると、場合によってはそのロールの割り当てが失敗することがあります。 たとえば、スクリプトを使用して新しいマネージド ID を作成し、そのサービス プリンシパルにロールを割り当てようとすると、ロールの割り当てが失敗する可能性があります。 このエラーの原因は、レプリケーションの遅延である可能性があります。 サービス プリンシパルは 1 つのリージョンに作成されます。ただし、ロールの割り当ては、サービス プリンシパルがまだレプリケートされていない別のリージョンで発生する可能性があります。 このシナリオに対処するには、ロールの割り当てを作成するときに、プリンシパルの種類を指定する必要があります。

ロールを割り当てるには、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用し、`--assignee-object-id` の値を指定してから、`--assignee-principal-type` を `ServicePrincipal` に設定します。

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

次の例では、*pharma-sales* リソース グループ スコープで、*msi-test* マネージド ID に "[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)" ロールを割り当てます。

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>サブスクリプション スコープでユーザーにロールを割り当てる

[閲覧者](built-in-roles.md#reader)ロールを、サブスクリプション スコープで、*annm\@example.com* ユーザーに割り当てます。

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-group-at-a-subscription-scope"></a>サブスクリプション スコープでグループにロールを割り当てる

[閲覧者](built-in-roles.md#reader)ロールを、サブスクリプション スコープで、ID が 22222222-2222-2222-2222-222222222222 の *Ann Mack Team* グループに割り当てます。

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-subscription-scope"></a>サブスクリプション スコープですべての BLOB コンテナーにロールを割り当てる

[ストレージ BLOB データ閲覧者](built-in-roles.md#storage-blob-data-reader)ロールを、サブスクリプション スコープで、*alain\@example.com* ユーザーに割り当てます。

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>管理グループ スコープでユーザーにロールを割り当てる

[課金データ閲覧者](built-in-roles.md#billing-reader)ロールを、管理グループ スコープで、*alain\@example.com* ユーザーに割り当てます。

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>次のステップ

- [Azure CLI を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-cli.md)
- [Azure CLI を使用して Azure のリソースとリソース グループを管理する](../azure-resource-manager/management/manage-resources-cli.md)
