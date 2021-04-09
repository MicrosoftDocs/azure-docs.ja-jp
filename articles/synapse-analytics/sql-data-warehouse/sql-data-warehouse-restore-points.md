---
title: ユーザー定義の復元ポイント
description: 専用 SQL プール (以前の SQL DW) の復元ポイントを作成する方法。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 097a3132208eee98b3f95291e414263e637bc265
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545589"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-formerly-sql-dw"></a>専用 SQL プール (以前の SQL DW) のユーザー定義の復元ポイント

この記事では、PowerShell と Azure portal を使用して、Azure Synapse Analytics で専用 SQL プール (以前の SQL DW) の新しいユーザー定義の復元ポイントを作成する方法について説明します。

## <a name="create-user-defined-restore-points-through-powershell"></a>PowerShell を使用してユーザー定義の復元ポイントを作成する

ユーザー定義の復元ポイントを作成するには、[New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell コマンドレットを使用します。

1. 開始する前に、必ず [Azure PowerShell をインストール](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)してください。
2. PowerShell を開きます。
3. Azure アカウントに接続して、アカウントに関連付けられているすべてのサブスクリプションを一覧表示します。
4. 復元するデータベースを含むサブスクリプションを選択します。
5. データ ウェアハウスの即時コピーに対する復元ポイントを作成します。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. すべての既存の復元ポイントを一覧表示します。

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Azure portal を使用してユーザー定義の復元ポイントを作成する

ユーザー定義の復元ポイントは、Azure portal を使用して作成することもできます。

1. [Azure portal](https://portal.azure.com/) アカウントにサインインします。

2. 復元ポイントを作成する専用 SQL プール (以前の SQL DW) に移動します。

3. 左側のウィンドウで **[概要]** を選択し、 **[+ 新しい復元ポイント]** を選択します。 [新しい復元ポイント] ボタンが有効でない場合は、専用 SQL プール (以前の SQL DW) が一時停止されていないことを確認します。

    ![新しい復元ポイント](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. ユーザー定義の復元ポイントの名前を指定して **[適用]** をクリックします。 ユーザー定義の復元ポイントには、7 日間の既定の保有期間があります。

    ![復元ポイントの名前](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>次のステップ

- [既存の専用 SQL プール (以前の SQL DW) を復元する](sql-data-warehouse-restore-active-paused-dw.md)
- [削除された専用 SQL プール (以前の SQL DW) を復元する](sql-data-warehouse-restore-deleted-dw.md)
- [geo バックアップ専用 SQL プール (旧称 SQL DW) から復元する](sql-data-warehouse-restore-from-geo-backup.md)
