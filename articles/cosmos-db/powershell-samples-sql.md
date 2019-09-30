---
title: Azure Cosmos DB 用 Azure PowerShell サンプル - SQL (Core) API
description: Azure Cosmos DB 用 Azure PowerShell サンプル - SQL (Core) API
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: d3d2bea0eafa0a15424cad288f03392fec8ea9eb
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155380"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure Cosmos DB 用 Azure PowerShell サンプル - SQL (Core) API

次の表には、Azure Cosmos DB の SQL (Core) API に一般的に使用される Azure PowerShell スクリプトへのリンクが含まれています。 これらの Cosmos DB 用 PowerShell サンプルを GitHub リポジトリからフォークしたい場合は、[GitHub の Cosmos DB PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)にアクセスしてください。

その他の Cosmos DB SQL (Core) API 用 PowerShell サンプルとドキュメントについては、「[PowerShell を使用して Azure Cosmos DB SQL API リソースを管理する](manage-with-powershell.md)」を参照してください。 その他、Cosmos DB の各種 API 向けの PowerShell サンプルについては、[Cassandra API](powershell-samples-cassandra.md)、[MongoDB API](powershell-samples-mongodb.md)、[Gremlin API](powershell-samples-gremlin.md)、[Table API](powershell-samples-table.md) を参照してください。

| | |
|---|---|
|[アカウント、データベース、およびコンテナーの作成](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、データベース、コンテナーを作成します。 |
|[大きいパーティション キーを含むコンテナーの作成](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 大きいパーティション キーを含むコンテナーを作成します。 |
|[データベースまたはコンテナーの一覧表示または取得](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコンテナーを一覧表示または取得します。 |
|[RU/秒の取得](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコンテナーの RU/秒を取得します。 |
|[RU/秒の更新](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコンテナーの RU/秒を更新します。 |
|[インデックス ポリシーを持たないコンテナーの作成](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | インデックス ポリシーが無効になっている Azure Cosmos コンテナーを作成します。|
|[アカウントの更新またはリージョンの追加](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos アカウントにリージョンを追加します。 他のアカウント プロパティを変更する際にも使用できますが、リージョンに対する変更とは別に行う必要があります。 |
|[フェールオーバー優先度の変更またはフェールオーバーのトリガー](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのリージョン内フェールオーバー優先度を変更したり、手動フェールオーバーをトリガーしたりします。 |
|[アカウント キーまたは接続文字列](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| プライマリ キーとセカンダリ キー、接続文字列を取得します。または Azure Cosmos アカウントのアカウント キーを再生成します。 |
|[IP ファイアウォールを使用した Cosmos アカウントの作成](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| IP ファイアウォールを有効にして Azure Cosmos アカウントを作成します。 |
|||
