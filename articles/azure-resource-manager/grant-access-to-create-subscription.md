---
title: Azure Enterprise サブスクリプションを作成する権限を付与する | Microsoft Docs
description: ユーザーまたはサービス プリンシパルがプログラムで Azure Enterprise サブスクリプションを作成できるようにする方法について説明します。
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 7a2397328f715dbf63246e8d4aaa789b5986b3b4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112565"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Azure Enterprise サブスクリプションを作成する権限を付与する (プレビュー)

[Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)を結んでいる Azure の顧客は、自分のアカウントに課金されるサブスクリプションを別のユーザーまたはサービス プリンシパルが作成することを許可できます。 この記事では、[ロール ベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) を使用して、サブスクリプションの作成機能を共有する方法と、サブスクリプションの作成を監査する方法について説明します。 共有するアカウントに所有者ロールが必要です。

サブスクリプションを作成するには、「[Azure Enterprise サブスクリプションをプログラムで作成する (プレビュー)](programmatically-create-subscription.md)」を参照してください。

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>RBAC を使用して登録アカウントへのアクセスを委任する

特定のアカウントに対してサブスクリプションを作成する権限を別のユーザーまたはサービス プリンシパルに付与するには、[登録アカウントのスコープで RBAC 所有者のロールを付与](../active-directory/role-based-access-control-manage-access-rest.md)します。 次の例では、`<userObjectId>` の `principalId` (SignUpEngineering@contoso.com の) のテナントであるユーザーに 登録アカウントの所有者ロールを付与します。 登録アカウント ID とプリンシパル ID を検索するには、「[Azure Enterprise サブスクリプションをプログラムで作成する (プレビュー)](programmatically-create-subscription.md)」を参照してください。

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```

登録アカウントのスコープで所有者ロールが付与されると、ロール割り当ての情報が Azure から返されます。

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

登録アカウントへの所有者アクセス権限を他のユーザーに付与するには、[New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) を使用します。

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

登録アカウントへの所有者アクセス権限を他のユーザーに付与するには、[az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) を使用します。

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

ユーザーが、登録アカウントの RBAC 所有者になると、そのアカウント内にサブスクリプションをプログラミングによって作成できます。 委任されたユーザーによって作成されたサブスクリプションには、元のアカウント所有者がサービス管理者として設定されたままになりますが、既定で委任されたユーザーも所有者として設定されています。 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>アクティビティ ログを使用してサブスクリプションを作成した監査

この API を使用して作成されたサブスクリプションを追跡するには、[Tenant Activity Log API](/rest/api/monitor/tenantactivitylogs) を使用します。 現時点では、PowerShell、CLI、または Azure Portal を使用してサブスクリプションの作成を追跡することはできません。

1. Azure AD テナントのテナント管理者は、[アクセス権限を昇格](../active-directory/role-based-access-control-tenant-admin-access.md)してから、スコープ内の監査ユーザーに閲覧者ロールを割り当てます`/providers/microsoft.insights/eventtypes/management`。
1. 監査ユーザーは、[Tenant Activity Log API](/rest/api/monitor/tenantactivitylogs) を呼び出して、サブスクリプションの作成活動を確認します。 例:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> コマンドラインからこの API を簡単に呼び出すには、[ARMClient](https://github.com/projectkudu/ARMClient) を使用します。

## <a name="next-steps"></a>次の手順

* これで、ユーザーまたはサービス プリンシパルにサブスクリプションを作成する権限が与えられたので、その ID を使って [Azure Enterprise サブスクリプションをプログラムで作成](programmatically-create-subscription.md)できます。
* .NET を使用してサブスクリプションを作成する例については、[GitHub のサンプル コード](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)を参照してください。
* Azure Resource Manager とその API については、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。
* 管理グループを使用して大量のサブスクリプションを管理する方法については、「[Azure 管理グループのリソースを整理する](management-groups-overview.md)」を参照してください。
* 大規模組織でのサブスクリプション管理に関する包括的なベスト プラクティス ガイダンスについては、「[Azure エンタープライズ スキャフォールディング - 規範的なサブスクリプション ガバナンス](/azure/architecture/cloud-adoption-guide/subscription-governance)」を参照してください。
