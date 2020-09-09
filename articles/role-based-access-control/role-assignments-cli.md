---
title: Azure CLI を使用して Azure でのロールの割り当てを追加または削除する - Azure RBAC
description: Azure CLI と Azure のロールベースのアクセス制御 (RBAC) を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 95ec9a25f97154d8e2d0e2e5b5f9cd29cf7a9c31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983327"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>Azure CLI を使用して Azure でのロールの割り当てを追加または削除する

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] この記事では、Azure CLI を使用してロールを割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

ロールの割り当てを追加または削除するには、以下が必要です。

- `Microsoft.Authorization/roleAssignments/write` および `Microsoft.Authorization/roleAssignments/delete` のアクセス許可 ([ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)や[所有者](built-in-roles.md#owner)など)
- [Azure Cloud Shell の Bash](/azure/cloud-shell/overview) または [Azure CLI](/cli/azure)

## <a name="get-object-ids"></a>オブジェクト ID を取得する

ロールの割り当てを追加または削除するには、オブジェクトの一意の ID の指定が必要になることがあります。 ID の形式は `11111111-1111-1111-1111-111111111111` です。 この ID は、Azure portal または Azure CLI を使用して取得できます。

### <a name="user"></a>User

Azure AD ユーザーのオブジェクト ID を取得するには、[az ad user show](/cli/azure/ad/user#az-ad-user-show) を使用できます。

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>グループ

Azure AD グループのオブジェクト ID を取得するには、[az ad group show](/cli/azure/ad/group#az-ad-group-show) または [az ad group list](/cli/azure/ad/group#az-ad-group-list) を使用できます。

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Application

Azure AD サービス プリンシパルのオブジェクト ID (アプリケーションによって使用される ID) を取得するには、[az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) を使用できます。 サービス プリンシパルの場合は、アプリケーション ID **ではなく**、オブジェクト ID を使用します。

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>ロールの割り当てを追加する

Azure RBAC でアクセス権を付与するには、ロールの割り当てを追加します。

### <a name="user-at-a-resource-group-scope"></a>リソース グループをスコープとするユーザー

リソース グループのスコープでユーザーにロールの割り当てを追加するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。

```azurecli
az role assignment create --role {roleNameOrId} --assignee {assignee} --resource-group {resourceGroup}
```

次の例では、*pharma-sales* リソース グループのスコープで、*patlong\@contoso.com* ユーザーに "*仮想マシンの共同作成者*" ロールを付与します。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>一意のロール ID の使用

ロール名が変更されるときがあります。たとえば次のような場合です。

- 独自のカスタム ロールを使用していて、名前を変更することにした場合。
- 名前に **(プレビュー)** が含まれるプレビュー ロールを使用している場合。 ロールがリリースされると、ロールの名前が変更されます。

> [!IMPORTANT]
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ロールの名前が変更される場合でも、ロールの ID は変わりません。 スクリプトまたはオートメーションを使用してロールの割り当てを作成する場合は、ロール名ではなく一意のロール ID を使用するのがベスト プラクティスです。 そうすれば、ロールの名前が変更されても、スクリプトが動作する可能性が高くなります。

ロール名ではなく一意のロール ID を使用してロールの割り当てを追加するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。

```azurecli
az role assignment create --role {roleId} --assignee {assignee} --resource-group {resourceGroup}
```

次の例では、*pharma-sales* リソース グループ スコープで、*patlong\@contoso.com* ユーザーに[仮想マシン共同作成者](built-in-roles.md#virtual-machine-contributor)ロールが付与されます。 一意のロール ID を取得するには、[az role definition list](/cli/azure/role/definition#az-role-definition-list) を使用するか、「[Azure 組み込みロール](built-in-roles.md)」を参照してください。

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>サブスクリプションをスコープとするグループ

グループにロールの割り当てを追加するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。 グループのオブジェクト ID を取得する方法については、「[オブジェクト ID を取得する](#get-object-ids)」を参照してください。

```azurecli
az role assignment create --role {roleNameOrId} --assignee-object-id {assigneeObjectId} --resource-group {resourceGroup} --scope /subscriptions/{subscriptionId}
```

次の例では、*Ann Mack Team* グループ (ID 22222222-2222-2222-2222-222222222222) に、サブスクリプション スコープで "*閲覧者*" ロールが割り当てられます。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>リソースをスコープとするグループ

グループにロールの割り当てを追加するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。 グループのオブジェクト ID を取得する方法については、「[オブジェクト ID を取得する](#get-object-ids)」を参照してください。

次の例では、*Ann Mack Team* グループ (ID 22222222-2222-2222-2222-222222222222) に、リソース スコープで *pharma-sales-project-network* という名前の仮想ネットワークに対する "*仮想マシン共同作成者*" ロールが割り当てられます。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>リソース グループをスコープとするアプリケーション

アプリケーションにロールの割り当てを追加するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。 アプリケーションのオブジェクト ID を取得する方法については、「[オブジェクト ID を取得する](#get-object-ids)」を参照してください。

```azurecli
az role assignment create --role {roleNameOrId} --assignee-object-id {assigneeObjectId} --resource-group {resourceGroup}
```

次の例では、*pharma-sales* リソース グループのスコープで、オブジェクト ID 44444444-4444-4444-4444-444444444444 を使ってアプリケーションに "*仮想マシンの共同作成者*" ロールを割り当てています。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>サブスクリプションをスコープとするユーザー

サブスクリプションのスコープでユーザーにロールの割り当てを追加するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。 サブスクリプション ID を取得するには、Azure portal の **[サブスクリプション]** ブレードで確認するか、[az account list](/cli/azure/account#az-account-list) を使用できます。

```azurecli
az role assignment create --role {roleNameOrId} --assignee {assignee} --subscription {subscriptionNameOrId}
```

次の例では、サブスクリプション スコープで *annm\@example.com* ユーザーに*閲覧者*ロールを割り当てます。

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>管理グループをスコープとするユーザー

管理グループのスコープでユーザーにロールの割り当てを追加するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用します。 管理グループ ID を取得するには、Azure portal の **[管理グループ]** ブレードで確認するか、[az account management-group list](/cli/azure/account/management-group#az-account-management-group-list) を使用します。

```azurecli
az role assignment create --role {roleNameOrId} --assignee {assignee} --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

次の例では、管理グループ スコープで *alain\@example.com* ユーザーに*課金データ閲覧者*ロールを割り当てます。

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>新しいサービス プリンシパル

新しいサービス プリンシパルを作成し、そのサービス プリンシパルにロールをすぐに割り当てようとすると、場合によってはそのロールの割り当てが失敗することがあります。 たとえば、スクリプトを使用して新しいマネージド ID を作成し、そのサービス プリンシパルにロールを割り当てようとすると、ロールの割り当てが失敗する可能性があります。 このエラーの原因は、レプリケーションの遅延である可能性があります。 サービス プリンシパルは 1 つのリージョンに作成されます。ただし、ロールの割り当ては、サービス プリンシパルがまだレプリケートされていない別のリージョンで発生する可能性があります。 このシナリオに対処するには、ロールの割り当てを作成するときに、プリンシパルの種類を指定する必要があります。

ロールの割り当てを追加するには、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用し、`--assignee-object-id` の値を指定してから、`--assignee-principal-type` を `ServicePrincipal` に設定します。

```azurecli
az role assignment create --role {roleNameOrId} --assignee-object-id {assigneeObjectId} --assignee-principal-type {assigneePrincipalType} --resource-group {resourceGroup} --scope /subscriptions/{subscriptionId}
```

次の例では、*pharma-sales* リソース グループ スコープで、*msi-test* マネージド ID に "*仮想マシン共同作成者*" ロールを割り当てます。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する

Azure RBAC でアクセス権を削除するには、[az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) を使用してロールの割り当てを削除します。

```azurecli
az role assignment delete --assignee {assignee} --role {roleNameOrId} --resource-group {resourceGroup}
```

次の例では、*pharma-sales* リソース グループの *patlong\@contoso.com* ユーザーから、"*仮想マシンの共同作成者*" ロールの割り当てを削除します。

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

次の例では、サブスクリプション スコープで "*閲覧者*" ロールが *Ann Mack Team* グループ (ID 22222222-2222-2222-2222-222222222222 ) から削除されます。 グループのオブジェクト ID を取得する方法については、「[オブジェクト ID を取得する](#get-object-ids)」を参照してください。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

次の例では、管理グループ スコープで *alain\@example.com* ユーザーから "*課金データ閲覧者*" ロールが削除されます。 管理グループの ID を取得するには、[az account management-group list](/cli/azure/account/management-group#az-account-management-group-list) を使用できます。

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>次のステップ

- [Azure CLI を使用して Azure でのロールの割り当てを一覧表示する](role-assignments-list-cli.md)
- [Azure CLI を使用して Azure のリソースとリソース グループを管理する](../azure-resource-manager/cli-azure-resource-manager.md)
