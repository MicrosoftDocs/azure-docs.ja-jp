---
title: 管理操作をキャンセルする
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance の管理操作をキャンセルする方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 342491178d55dacbdc68e6c9042623d381dff898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861546"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Azure SQL Managed Instance の管理操作のキャンセル
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance には、新しいマネージド インスタンスをデプロイしたりインスタンスのプロパティを更新したりする場合などに一部の[管理操作](management-operations-overview.md)をキャンセルする機能が用意されています。 

## <a name="overview"></a>概要

 すべての管理操作は次のように分類できます。

- インスタンスのデプロイ (新しいインスタンスの作成)。
- インスタンスの更新 (仮想コアや予約済み記憶域などの、インスタンスのプロパティの変更)。
- インスタンスの削除。

[管理操作の進行状況と状態を監視](management-operations-monitor.md)し、必要に応じてその一部をキャンセルすることができます。 

次の表に、各管理操作、キャンセル可能かどうか、および一般的な所要時間をまとめてあります。

カテゴリ  |Operation  |キャンセル可能  |推定キャンセル時間  |
|---------|---------|---------|---------|
|デプロイ |インスタンスの作成 |はい |操作の 90% は 5 分以内に完了。 |
|更新 |インスタンスのストレージのスケールアップとスケールダウン (General Purpose) |いいえ |  |
|更新 |インスタンスのストレージのスケールアップとスケールダウン (Business Critical) |はい |操作の 90% は 5 分以内に完了。 |
|更新 |インスタンスのコンピューティング (仮想コア) のスケールアップとスケールダウン (General Purpose) |はい |操作の 90% は 5 分以内に完了。 |
|更新 |インスタンスのコンピューティング (仮想コア) のスケールアップとスケールダウン (Business Critical) |はい |操作の 90% は 5 分以内に完了。 |
|更新 |インスタンスのサービス レベルの変更 (General Purpose から Business Critical、またはその逆へ) |はい |操作の 90% は 5 分以内に完了。 |
|削除 |インスタンスの削除 |いいえ |  |
|削除 |(ユーザーによって開始された操作としての) 仮想クラスターの削除 |いいえ |  |

## <a name="cancel-management-operation"></a>管理操作をキャンセルする

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して管理操作をキャンセルするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にアクセスします。
1. SQL Managed Instance の **[概要]** ブレードにアクセスします。 
1. 進行中の操作の横にある **[通知]** ボックスを選択して、 **[実行中の操作]** ページを開きます。 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="[実行中の操作] ボックスを選択して、[実行中の操作] ページを開きます。":::

1. ページの下部にある **[操作のキャンセル]** を選択します。 

   操作をキャンセルするには、:::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="[操作のキャンセル]"::: をクリックします。

1. 操作をキャンセルすることを確認します。 


キャンセル要求が成功すると、管理操作はキャンセルされ、エラーが生成されます。 キャンセルが成功したか失敗したかを示す通知が表示されます。

![キャンセル操作の結果](./media/management-operations-cancel/canceling-operation-result.png)


キャンセル要求が失敗するか、[キャンセル] ボタンがアクティブでない場合、それは管理操作がキャンセルできない状態に入っており、すぐに完了することを意味します。  管理操作は、完了するまで実行が継続されます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell がまだインストールされていない場合は、「[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)」を参照してください。

管理操作をキャンセルするには、管理操作名を指定する必要があります。 したがって、最初に get コマンドを使用して操作一覧を取得してから、特定の操作をキャンセルします。

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

コマンドの詳細については、[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation) と [Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation) を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI をまだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

管理操作をキャンセルするには、管理操作名を指定する必要があります。 そのため、最初に get コマンドを使用して操作一覧を取得してから特定の操作を取り消します。

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

コマンドの詳細については、[az sql mi op](/cli/azure/sql/mi/op) を参照してください。

---

## <a name="canceled-deployment-request"></a>デプロイ要求のキャンセル

API バージョン 2020-02-02 では、インスタンス作成要求が受け入れられるとすぐに、デプロイ プロセスの進行状況に関係なくインスタンスはリソースとして存在し始めます (マネージド インスタンスの状態は **Provisioning**)。 インスタンスのデプロイ要求 (新しいインスタンスの作成) をキャンセルした場合、マネージド インスタンスの状態は **Provisioning** から **FailedToCreate** に移ります。

作成に失敗したインスタンスは、リソースとして引き続き存在しますが次のようになります。 

- 課金されません
- リソース制限 (サブネットまたは仮想コア クォータ) にはカウントされません


> [!NOTE]
> リソースまたはマネージド インスタンスの一覧のノイズを最小限に抑えるには、デプロイに失敗したインスタンスまたはデプロイがキャンセルされたインスタンスを削除します。 


## <a name="next-steps"></a>次のステップ

- 最初のマネージド インスタンスを作成する方法については、[クイック スタート ガイド](instance-create-quickstart.md)を参照してください。
- 機能と比較の一覧については、[共通の SQL 機能](../database/features-comparison.md)に関するページを参照してください。
- VNet の構成の詳細については、[SQL Managed Instance VNet の構成](connectivity-architecture-overview.md)に関するページを参照してください。
- マネージド インスタンスを作成し、バックアップ ファイルからデータベースを復元するためのクイック スタートについては、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- Azure Database Migration Service を使用して移行する方法のチュートリアルについては、[Database Migration Service を使用した SQL Managed Instance の移行](../../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。
