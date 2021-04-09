---
title: カスタム ロール:SQL Server から SQL Managed Instance へのオンライン移行
titleSuffix: Azure Database Migration Service
description: SQL Server から Azure SQL Managed Instance へオンライン移行するためにカスタム ロールを使用する方法について説明します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 1228234b6a2904c453ec92f3c09a7b3f55604953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363765"
---
# <a name="custom-roles-for-sql-server-to-azure-sql-managed-instance-online-migrations"></a>SQL Server から Azure SQL Managed Instance へオンライン移行するためのカスタム ロール

Azure Database Migration Service では、Azure サービスとの対話にアプリ ID を使用します。 アプリ ID には、サブスクリプション レベルの共同作成者ロール (多くの企業セキュリティ部門では許可されません)、または Azure Database Migration Service に必要な特定のアクセス許可を付与するカスタム ロールの作成が必要です。 Azure Active Directory には 2,000 個のカスタム ロールの制限があるため、そのアプリ ID に特に必要なすべてのアクセス許可を 1 つまたは 2 つのカスタム ロールに結合し、特定のオブジェクトまたはリソース グループ (またはサブスクリプション レベル) のカスタム ロールにアプリ ID に付与することをお勧めします。 カスタム ロールの数が問題にならない場合は、以下で説明するように、リソースの種類ごとにカスタム ロールを分割して、合計 3 つのカスタム ロールを作成できます。

ロール定義 json 文字列の AssignableScopes セクションを使用すると、ポータルの **[ロール割り当ての追加]** UI でアクセス許可を表示する場所を制御できます。 追加のロールで UI が乱雑にならないように、リソース グループまたはリソース レベルでロールを定義することをお勧めします。 この処理で実際のロールの割り当てが実行されないことに注意してください。

## <a name="minimum-number-of-roles"></a>ロールの最小数

現在のところは、アプリ ID に対して少なくとも 2 つのカスタム ロールを作成することをお勧めします。1 つはリソース レベルで、もう 1 つはサブスクリプション レベルです。

> [!NOTE]
> SQL Managed Instance の新しいコードが Azure にデプロイされると、最後のカスタム ロールの要件は最終的に削除される可能性があります。

**アプリ ID のカスタム ロール**。 このロールは、Azure Database Migration Service をホストする *リソース* または *リソース グループ* レベルでの Azure Database Migration Service の移行に必要です (アプリ ID の詳細については、「[リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md)」の記事を参照してください)。

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**アプリ ID のカスタム ロール - サブスクリプション**。 このロールは、SQL Managed Instance をホストする *サブスクリプション* レベルでの Azure Database Migration Service の移行に必要です。

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

上記の json は 3 つのテキスト ファイルに保存する必要があり、AzureRM、AZ PowerShell コマンドレット、または Azure CLI を使用し、**New-AzureRmRoleDefinition (AzureRM)** または **New-AzRoleDefinition (AZ)** を使用してロールを作成できます。

詳細については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

これらのカスタム ロールを作成した後は、ロールの割り当てをユーザーに追加し、アプリ ID を適切なリソースまたはリソース グループに追加する必要があります。

* 移行に使用されるアプリ ID に対して、"DMS ロール - アプリ ID" ロールを、ストレージ アカウント、Azure Database Migration Service インスタンス、および SQL Managed Instance リソース レベルで付与する必要があります。 これは、Azure Database Migration Service をホストするリソースまたはリソース グループ レベルで付与します。
* "DMS ロール - アプリ ID - サブ" ロールは、SQL Managed Instance をホストするサブスクリプション レベルで APP ID に付与する必要があります (リソースまたはリソース グループでの付与は失敗します)。 この要件は、コードの更新がデプロイされるまでの一時的なものです。

## <a name="expanded-number-of-roles"></a>ロール数の拡大

Azure Active Directory のカスタム ロールの数が問題にならない場合は、合計 3 つのロールを作成することをお勧めします。 "DMS ロール - アプリ ID - サブ" ロールは引き続き必要ですが、上記の "DMS ロール - アプリ ID" ロールはリソースの種類ごとに 2 つの異なるロールに分割されます。

**SQL Managed Instance 用の アプリ ID のカスタム ロール**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**ストレージ用のアプリ ID のカスタム ロール**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>ロール割り当て

ユーザー/アプリ ID にロールを割り当てるには、Azure portal を開き、次の手順を実行します。

1. (サブスクリプションで付与する必要があるロールを除いて) リソース グループまたはリソースに移動し、 **[アクセス制御]** に移動し、スクロールして先ほど作成したカスタム ロールを見つけます。

2. 適切なロールを選択し、アプリ ID を選択して、変更を保存します。

  これで、アプリ ID が **[ロールの割り当て]** タブに表示されます。

## <a name="next-steps"></a>次のステップ

* 実際のシナリオに合わせた移行のガイダンスについては、「[Microsoft データベースの移行ガイド](https://datamigration.microsoft.com/)」を参照してください。
