---
title: Azure Cosmos DB アカウントを復元するためのアクセス許可を構成する
description: 継続的バックアップ アカウントの復元アクセス許可を切り分けて、特定のロールまたはプリンシパルに制限する方法について説明します。 ここでは、Azure portal または CLI を使用して組み込みロールを割り当てるか、カスタム ロールを定義する方法を示します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8b3ce2c195dc2fa3dd703306e731aa5b807b78b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100648605"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを復元するためのアクセス許可を管理する
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB のポイントインタイム リストア機能 (継続的バックアップ モード) は、現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Cosmos DB を使用すると、継続的バックアップ (プレビュー) アカウントの復元のアクセス許可を切り分けて、特定のロールまたはプリンシパルに制限することができます。 アカウントの所有者は復元をトリガーし、他のプリンシパルにロールを割り当てて復元操作を実行できます。 これらのアクセス許可は、次の図に示すように、サブスクリプション スコープで、またはソース アカウント スコープでよりきめ細かく適用できます。

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="復元操作を実行するために必要なロールの一覧。" lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

スコープは、アクセス権があるリソースのセットです。スコープの詳細については、[Azure RBAC](../role-based-access-control/scope-overview.md) のドキュメントを参照してください。 Azure Cosmos DB で適用できるスコープは、ほとんどのユース ケースの場合、ソースのサブスクリプションとデータベース アカウントです。 復元操作を実行するプリンシパルには、宛先リソース グループに対する書き込みアクセス許可が必要です。

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Azure portal を使用して復元のためのロールを割り当てる

復元を実行するには、ユーザーまたはプリンシパルに、復元するためのアクセス許可 ("*復元/アクション*" アクセス許可) と、新しいアカウントをプロビジョニングするためのアクセス許可 ("*書き込み*" アクセス許可) が必要です。  これらのアクセス許可を付与するために、所有者は `CosmosRestoreOperator` と `Cosmos DB Operator` の組み込みロールをプリンシパルに割り当てることができます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. サブスクリプションに移動して、 **[アクセス制御 (IAM)]** タブに進み、 **[追加]**  >  **[ロールの割り当ての追加]** の順に選択します。

1. **[ロールの割り当ての追加]** ウィンドウで、 **[ロール]** フィールドに **CosmosRestoreOperator** ロールを選択します。 次の図に示すように、 **[アクセスの割り当て先]** フィールドで **[User, group, or a service principal]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択し、ユーザーの名前またはメール ID を検索します。

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="CosmosRestoreOperator と Cosmos DB オペレーターのロールを割り当てる。" border="true":::

1. **[保存]** を選択して、"*復元/アクション*" アクセス許可を付与します。

1. **Cosmos DB オペレーター** ロールで手順 3 を繰り返して、書き込みアクセス許可を付与します。 このロールを Azure portal から割り当てると、サブスクリプション全体に対する復元アクセス許可が付与されます。

## <a name="permission-scopes"></a>アクセス許可のスコープ

|Scope  |例  |
|---------|---------|
|サブスクリプション | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Resource group | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|CosmosDB 復元可能アカウント リソース | /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

復元可能アカウント リソースは、CLI では `az cosmosdb restorable-database-account list --name <accountname>` コマンド、または PowerShell では `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` コマンドレットの出力から抽出できます。 出力の name 属性は、復元可能アカウントの `instanceID` を表します。 詳細については、[PowerShell](continuous-backup-restore-powershell.md) または [CLI](continuous-backup-restore-command-line.md) に関する記事を参照してください。

## <a name="permissions"></a>アクセス許可

次のアクセス許可は、継続的バックアップ モードのアカウントの復元に関連するさまざまなアクティビティの実行に必要です。

|権限  |影響  |最小スコープ  |最大スコープ  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | これらのアクセス許可は、ARM テンプレートのデプロイで、復元されたアカウントを作成するために必要です。 このロールを設定する方法については、次のサンプルのアクセス許可 [RestorableAction](#custom-restorable-action) を参照してください。 | 適用できません | 適用なし  |
|`Microsoft.DocumentDB/databaseAccounts/write` | このアクセス許可は、アカウントをリソース グループに復元するために必要です。 | 復元されたアカウントが作成されるリソース グループ。 | 復元されたアカウントが作成されるサブスクリプション。 |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` |このアクセス許可は、ソースの復元可能データベース アカウント スコープで、それに対して復元操作を実行できるようにするために必要です。  | 復元するソース アカウントに属する *RestorableDatabaseAccount* リソース。 この値は、復元可能データベース アカウント リソースの `ID` プロパティによっても示されます。 復元可能なアカウントの例: */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>* | 復元可能データベース アカウントを含むサブスクリプション。 リソース グループをスコープとして選択することはできません。  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` |このアクセス許可は、ソースの復元可能データベース アカウント スコープで、復元できるデータベース アカウントを一覧表示するために必要です。  | 復元するソース アカウントに属する *RestorableDatabaseAccount* リソース。 この値は、復元可能データベース アカウント リソースの `ID` プロパティによっても示されます。 復元可能なアカウントの例: */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>*| 復元可能データベース アカウントを含むサブスクリプション。 リソース グループをスコープとして選択することはできません。  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` | このアクセス許可は、ソースの復元可能アカウント スコープで、復元可能アカウントのデータベースとコンテナーの一覧などの復元可能リソースを読み取ることができるようにするために必要です。  | 復元するソース アカウントに属する *RestorableDatabaseAccount* リソース。 この値は、復元可能データベース アカウント リソースの `ID` プロパティによっても示されます。 復元可能なアカウントの例: */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>*| 復元可能データベース アカウントを含むサブスクリプション。 リソース グループをスコープとして選択することはできません。 |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>さまざまなスコープで復元するための Azure CLI でのロールの割り当てのシナリオ

アクセス許可を持つロールをさまざまなスコープに割り当てることで、サブスクリプション内または特定のアカウントで復元操作を実行できるユーザーをきめ細かく制御できます。

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>サブスクリプション内の任意の復元可能アカウントから復元する機能を割り当てる

`CosmosRestoreOperator` 組み込みロールをサブスクリプション レベルで割り当てます。

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>特定のアカウントから復元する機能を割り当てる

* 特定のリソース グループに対するユーザー書き込みアクションを割り当てます。 このアクションは、リソース グループに新しいアカウントを作成するために必要です。

* 復元する必要がある特定の復元可能データベース アカウントに *CosmosRestoreOperator* 組み込みロールを割り当てます。 次のコマンドでは、*RestorableDatabaseAccount* のスコープは、`az cosmosdb restorable-database-account` (CLI を使用している場合) または `Get-AzCosmosDBRestorableDatabaseAccount` (PowerShell を使用している場合) の出力の `ID` プロパティから取得されます。

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>リソース グループ内の任意のソース アカウントから復元する機能を割り当てる
この操作は現在サポートされていません。

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>CLI を使用した復元操作用のカスタム ロールの作成

サブスクリプションの所有者は、他の任意の Azure AD ID に、復元するためのアクセス許可を付与できます。 復元アクセス許可は、アクション `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` に基づいています。そして、これは復元アクセス許可に含まれている必要があります。 このロールが含まれている *CosmosRestoreOperator* という名前の組み込みロールがあります。 この組み込みロールを使用してアクセス許可を割り当てることも、カスタム ロールを作成することもできます。

下の RestorableAction はカスタム ロールを表しています。 このロールは明示的に作成する必要があります。 次の JSON テンプレートでは、復元アクセス許可を持つカスタム ロール *RestorableAction* が作成されます。

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

次に、以下のテンプレート デプロイ コマンドを使用し、ARM テンプレートを使用して復元アクセス許可を持つロールを作成します。

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>次のステップ

* [Azure portal](continuous-backup-restore-portal.md)、[PowerShell](continuous-backup-restore-powershell.md)、[CLI](continuous-backup-restore-command-line.md)、または [Azure Resource Manager](continuous-backup-restore-template.md) を使用して、継続的バックアップを構成および管理する
* [継続的バックアップ モードのリソース モデル](continuous-backup-restore-resource-model.md)
