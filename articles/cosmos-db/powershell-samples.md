---
title: Azure Cosmos DB Core (SQL) API 用の Azure PowerShell サンプル
description: Azure Cosmos DB Core (SQL) API で一般的なタスクを実行するための Azure PowerShell サンプルを入手します
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 01/20/2021
ms.author: mjbrown
ms.openlocfilehash: ab9904127d085113ba09bf6fcd3616842dade14d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503309"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-core-sql-api"></a>Azure Cosmos DB Core (SQL) API 用の Azure PowerShell サンプル
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

次の表では、Azure Cosmos DB でよく使用される Azure PowerShell スクリプトへのリンクが示されています。 API 固有のサンプルに移動するには、右側のリンクを使用します。 共通サンプルは、すべての API で同じです。 Azure Cosmos DB PowerShell のすべてのコマンドレットのリファレンス ページについては、[Azure PowerShell リファレンス](/powershell/module/az.cosmosdb)を参照してください。 `Az.CosmosDB` モジュールは、`Az` モジュールの一部になりました。 Az モジュールの最新バージョンを[ダウンロードおよびインストール](/powershell/azure/install-az-ps)して、Azure Cosmos DB コマンドレットを取得します。 [PowerShell ギャラリー](https://www.powershellgallery.com/packages/Az/5.4.0)から最新バージョンを取得することもできます。 [GitHub の Cosmos DB PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)の GitHub リポジトリから、これらの Cosmos DB 用 PowerShell サンプルをフォークすることもできます。

他の API 用の PowerShell コマンドレットについては、[Cassandra 用の PowerShell サンプル](powershell-samples-cassandra.md)、[MongoDB API 用の PowerShell サンプル](powershell-samples-mongodb.md)、[Gremlin 用の PowerShell サンプル](powershell-samples-gremlin.md)、[Table 用の PowerShell サンプル](powershell-samples-table.md)に関する記述を参照してください

## <a name="common-samples"></a>一般的なサンプル

|タスク | 説明 |
|---|---|
|[アカウントの更新](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB アカウントの既定の一貫性レベルを更新します。 |
|[アカウントのリージョンの更新](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB アカウントのリージョンを更新します。 |
|[フェールオーバー優先度の変更またはフェールオーバーのトリガー](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのリージョン内フェールオーバー優先度を変更したり、手動フェールオーバーをトリガーしたりします。 |
|[アカウント キーまたは接続文字列](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| プライマリ キーとセカンダリ キー、接続文字列を取得します。または Azure Cosmos DB アカウントのアカウント キーを再生成します。 |
|[IP ファイアウォールを使用した Cosmos アカウントの作成](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| IP ファイアウォールを有効にして Azure Cosmos DB アカウントを作成します。 |
|||

## <a name="core-sql-api-samples"></a>Core (SQL) API サンプル

|タスク | 説明 |
|---|---|
|[アカウント、データベース、およびコンテナーの作成](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos DB アカウント、データベース、コンテナーを作成する |
|[アカウント、データベース、自動スケーリングするコンテナーを作成する](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos DB アカウント、データベース、自動スケーリングするコンテナーを作成します。 |
|[大きいパーティション キーを含むコンテナーの作成](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 大きいパーティション キーを含むコンテナーを作成します。 |
|[インデックス ポリシーを持たないコンテナーの作成](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | インデックス ポリシーが無効になっている Azure Cosmos コンテナーを作成します。|
|[データベースまたはコンテナーの一覧表示または取得](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコンテナーを一覧表示または取得します。 |
|[スループット操作](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコンテナーに対するスループット操作には、取得、更新、および自動スケーリングと標準スループット間の移行が含まれます。 |
|[リソースが削除されないようにロックする](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||
