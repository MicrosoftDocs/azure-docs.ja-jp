---
title: Azure Cosmos DB Table API 用の Azure PowerShell サンプル
description: Azure Cosmos DB Table API で一般的なタスクを実行するための Azure PowerShell サンプルを入手します
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: a72b329586d25b5eb7014e0fba65e7e6f8d37598
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503336"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API 用の Azure PowerShell サンプル
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

次の表では、Azure Cosmos DB でよく使用される Azure PowerShell スクリプトへのリンクが示されています。 API 固有のサンプルに移動するには、右側のリンクを使用します。 共通サンプルは、すべての API で同じです。 Azure Cosmos DB PowerShell のすべてのコマンドレットのリファレンス ページについては、[Azure PowerShell リファレンス](/powershell/module/az.cosmosdb)を参照してください。 `Az.CosmosDB` モジュールは、`Az` モジュールの一部になりました。 Az モジュールの最新バージョンを[ダウンロードおよびインストール](/powershell/azure/install-az-ps)して、Azure Cosmos DB コマンドレットを取得します。 [PowerShell ギャラリー](https://www.powershellgallery.com/packages/Az/5.4.0)から最新バージョンを取得することもできます。 [GitHub の Cosmos DB PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)の GitHub リポジトリから、これらの Cosmos DB 用 PowerShell サンプルをフォークすることもできます。

## <a name="common-samples"></a>一般的なサンプル

|タスク | 説明 |
|---|---|
|[アカウントの更新](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB アカウントの既定の一貫性レベルを更新します。 |
|[アカウントのリージョンの更新](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB アカウントのリージョンを更新します。 |
|[フェールオーバー優先度の変更またはフェールオーバーのトリガー](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのリージョン内フェールオーバー優先度を変更したり、手動フェールオーバーをトリガーしたりします。 |
|[アカウント キーまたは接続文字列](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| プライマリ キーとセカンダリ キー、接続文字列を取得します。または Azure Cosmos DB アカウントのアカウント キーを再生成します。 |
|[IP ファイアウォールを使用した Cosmos アカウントの作成](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| IP ファイアウォールを有効にして Azure Cosmos DB アカウントを作成します。 |
|||

## <a name="table-api-samples"></a>Table API サンプル

|タスク | 説明 |
|---|---|
|[アカウントとテーブルの作成](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントとテーブルを作成します。 |
|[アカウントと自動スケーリングするテーブルを作成する](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントと自動スケーリングするテーブルを作成します。 |
|[テーブルの一覧表示または取得](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| テーブルを一覧表示または取得します。 |
|[スループット操作](scripts/powershell/table/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| テーブルに対するスループット操作には、取得、更新、および自動スケーリングと標準スループット間の移行が含まれます。 |
|[リソースが削除されないようにロックする](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||
