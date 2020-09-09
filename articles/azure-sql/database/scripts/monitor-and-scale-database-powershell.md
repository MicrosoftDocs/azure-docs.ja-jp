---
title: PowerShell を使用して Azure SQL Database の単一データベースを監視およびスケーリングする
description: Azure PowerShell サンプル スクリプトを使用し、Azure SQL Database の単一データベースを監視し、スケーリングします。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3c4695bb9dfeb9f0122d39dd335c9b733fe039b2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085046"
---
# <a name="use-powershell-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>PowerShell を使用して Azure SQL Database の単一データベースを監視およびスケーリングする

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

この PowerShell のサンプル スクリプトは、単一データベースのパフォーマンス メトリックを監視し、そのデータベースを上位のコンピューティング サイズにスケーリングして、パフォーマンス メトリックの 1 つに警告ルールを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Az PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=15-16 "Monitor and scale single database")]

> [!NOTE]
> 詳細なメトリックの一覧については、[サポートされるメトリック](../../../azure-monitor/platform/metrics-supported.md#microsoftsqlserversdatabases)を参照してください。
> [!TIP]
> [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) を使用してデータベース操作の状態を取得し、[Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity) を使用してデータベースの更新操作を取り消します。

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 単一データベースまたはエラスティック プールをホストするサーバーを作成します。 |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | データベース サイズの使用量に関する情報を表示します。|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | データベースのプロパティを更新するか、エラスティック プールに対して、エラスティック プールから、またはエラスティック プール間でデータベースを移動します。 |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | メトリックを今後自動的に監視するアラート ルールを設定します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の PowerShell サンプル スクリプトは、[Azure PowerShell スクリプト](../powershell-script-content-guide.md)のページにあります。
