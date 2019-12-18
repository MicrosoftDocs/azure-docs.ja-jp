---
title: ユーザー アクセスを Azure Cosmos DB でのデータ操作のみに制限する
description: アクセスを Azure Cosmos DB でのデータ操作のみに制限する方法について説明します
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980020"
---
# <a name="restrict-user-access-to-data-operations-only"></a>ユーザー アクセスをデータ操作のみに制限する

Azure Cosmos DB では、データベース サービスとの対話を認証するには、次の 2 つの方法があります。
- Azure portal と対話するときに Azure Active Directory ID を使用する。
- API および SDK からの呼び出しを発行するときに Azure Cosmos DB の[キー](secure-access-to-data.md#master-keys)または[リソース トークン](secure-access-to-data.md#resource-tokens)を使用する。

各認証方法では、さまざまな操作のセットにアクセスできますが、いくつかの重複があります。![認証の種類ごとの操作の分割](./media/how-to-restrict-user-data/operations.png)

場合によっては、組織の一部のユーザーに対して、データ操作 (CRUD 要求とクエリ) のみを実行するように制限することができます。 これは、通常、リソースを作成または削除したり、作業中のコンテナーのプロビジョニングされたスループットを変更したりする必要がない開発者の場合に当てはまります。

次の手順を適用してアクセスを制限できます。
1. アクセスを制限するユーザーのカスタム Azure Active Directory ロールを作成します。 カスタム Active Directory ロールには、Azure Cosmos DB の[粒度の細かいアクション](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)を使用した、詳細に設定された操作のアクセス レベルが含まれる必要があります。
1. キーを使用した非データ操作の実行を禁止します。 これを実現するには、これらの操作を Azure Resource Manager の呼び出しのみに制限します。

この記事の次のセクションでは、これらの手順を実行する方法について説明します。

> [!NOTE]
> 次のセクションのコマンドを実行するには、Azure PowerShell モジュール 3.0.0 以降をインストールする必要があります。また、変更しようとしているサブスクリプションに対して [Azure 所有者ロール](../role-based-access-control/built-in-roles.md#owner)を持っている必要があります。

次のセクションの PowerShell スクリプトでは、次のプレースホルダーを実際の環境に固有の値に置き換えます。
- `$MySubscriptionId` - アクセス許可を制限する Azure Cosmos アカウントを含むサブスクリプション ID。 (例: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`)。
- `$MyResourceGroupName` - Azure Cosmos アカウントを含むリソース グループ。 (例: `myresourcegroup`)。
- `$MyAzureCosmosDBAccountName` - Azure Cosmos アカウントの名前。 (例: `mycosmosdbsaccount`)。
- `$MyUserName` - アクセスを制限するユーザーのログイン (username@domain)。 (例: `cosmosdbuser@contoso.com`)。

## <a name="select-your-azure-subscription"></a>Azure サブスクリプションを選択します。

Azure PowerShell のコマンドを実行するには、ログインしてコマンドを実行するためのサブスクリプションを選択する必要があります。

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>カスタム Azure Active Directory ロールの作成

次のスクリプトでは、Azure Cosmos アカウントの "キーのみ" のアクセス権を持つ Azure Active Directory ロールの割り当てを作成します。 ロールは、[Azure リソースのカスタム ロール](../role-based-access-control/custom-roles.md)と [Azure Cosmos DB の粒度の細かいアクション](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)に基づいています。 これらのロールとアクションは、`Microsoft.DocumentDB` Azure Active Directory 名前空間の一部です。

1. 最初に、次の内容を含む `AzureCosmosKeyOnlyAccess.json` という名前の JSON ドキュメントを作成します。

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. 次のコマンドを実行してロールの割り当てを作成し、ユーザーに割り当てます。

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>非データ操作の実行の禁止

次のコマンドを実行して、各キーを使用する機能を削除します。
- リソースの作成、変更、または削除
- コンテナー設定の更新 (インデックス作成ポリシーやスループットなど)。

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>次の手順

- [Cosmos DB のロールベースのアクセス制御](role-based-access-control.md)に関する詳細情報
- [Cosmos DB 内のデータへのセキュリティで保護されたアクセス](secure-access-to-data.md)の概要
