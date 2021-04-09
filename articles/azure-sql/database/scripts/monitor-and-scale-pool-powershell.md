---
title: PowerShell の例 - エラスティック プールを監視してスケーリングする - Azure SQL Database
description: Azure SQL Database のエラスティック プールを監視およびスケールするための Azure PowerShell サンプル スクリプト
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 822674543f07ea95590ed66b9763ffd2bda35190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96493816"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>PowerShell を使用して Azure SQL Database のエラスティック プールを監視およびスケールする
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

この PowerShell のサンプル スクリプトは、エラスティック プールのパフォーマンス メトリックを監視し、そのエラスティック プールを上位のコンピューティング サイズにスケーリングして、パフォーマンス メトリックの 1 つに警告ルールを作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Az PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale an elastic pool in SQL Database")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | データベースまたはエラスティック プールをホストするサーバーを作成します。 |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | エラスティック プールを作成します。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | サーバーにデータベースを作成します。 |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | データベース サイズの使用量に関する情報を表示します。|
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | メトリックベースのアラート ルールを追加または更新します。 |
| [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) | エラスティック プールのプロパティを更新します。 |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | メトリックを今後自動的に監視するアラート ルールを設定します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の PowerShell サンプル スクリプトは、[Azure PowerShell スクリプト](../powershell-script-content-guide.md)のページにあります。
