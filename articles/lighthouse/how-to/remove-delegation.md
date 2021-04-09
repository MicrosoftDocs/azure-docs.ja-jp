---
title: 委任へのアクセスを削除する
description: Azure Lighthouse のためにサービス プロバイダーに委任されたリソースへのアクセスを削除する方法について説明します。
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: c53b678ba6e37ece1bcaf2860abceb9eea980532
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555777"
---
# <a name="remove-access-to-a-delegation"></a>委任へのアクセスを削除する

顧客のサブスクリプションまたはリソース グループが、[Azure Lighthouse](../overview.md) のためにサービス プロバイダーに委任された後、必要に応じて委任を削除できます。 委任が削除されると、サービス プロバイダー テナントのユーザーに以前付与されていた、[Azure の委任されたリソース管理](../concepts/azure-delegated-resource-management.md)アクセスは適用されなくなります。

委任の削除は、ユーザーが適切なアクセス許可を持っている限り、顧客テナントまたはサービス プロバイダー テナントのユーザーが行うことができます。

> [!TIP]
> このトピックではサービス プロバイダーと顧客の場合について説明していますが、[複数のテナントを管理するエンタープライズ](../concepts/enterprise.md)も同じプロセスを使用できます。

## <a name="customers"></a>顧客

`Microsoft.Authorization/roleAssignments/write` のアクセス許可を持つ[所有者](../../role-based-access-control/built-in-roles.md#owner)などのロールが割り当てられている、顧客のテナント内のユーザーは、そのサブスクリプション (またはそのサブスクリプション内のリソース グループ) へのサービス プロバイダー アクセスを削除できます。 これを行うには、ユーザーは、Azure portal の [[サービス プロバイダー]](view-manage-service-providers.md#add-or-remove-service-provider-offers) ページに移動し、 **[サービス プロバイダーのオファー]** 画面でオファーを見つけ、そのオファーの行のごみ箱アイコンを選択します。

削除を確定すると、サービス プロバイダーのテナント内のユーザーは、以前に委任されたリソースにアクセスできなくなります。

## <a name="service-providers"></a>サービス プロバイダー

管理テナントのユーザーは、顧客のリソースについての[マネージド サービスの登録割り当て削除ロール](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)が付与されている場合、委任されたリソースへのアクセスを削除できます。 このロールがサービス プロバイダー ユーザーに割り当てられていない場合は、顧客のテナントのユーザーのみが委任を削除できます。

以下の例は、[オンボード プロセス](onboard-customer.md)中にパラメーター ファイルに含めることができる **マネージド サービスの登録割り当ての削除ロール** を付与する割り当てを示しています。

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

このロールは、Azure Marketplace に公開するための [マネージド サービス オファーを作成する](../../marketplace/plan-managed-service-offer.md)ときに、**承認** で選択することもできます。

このアクセス許可を持つユーザーは、次のいずれかの方法で委任を削除できます。

### <a name="azure-portal"></a>Azure portal

1. [[マイ カスタマー] ページ](view-manage-customers.md)に移動します。
2. **[委任]** を選択します。
3. 削除する委任を見つけ、その行に表示されるごみ箱アイコンを選択します。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>次のステップ

- [Azure の委任されたリソース管理](../concepts/azure-delegated-resource-management.md)について学習してください。
- Azure portal の **[マイ カスタマー]** に移動して、[顧客を表示および管理](view-manage-customers.md)します。
- [以前の委任を更新する](update-delegation.md)方法について説明します。
