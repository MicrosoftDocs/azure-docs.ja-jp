---
title: Azure Cosmos DB の Azure CLI サンプル
description: Azure CLI サンプル - Azure Cosmos DB のアカウント、データベース、コンテナー、リージョン、およびファイアウォールを作成し、管理します。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a6348024d4e84c27610f1294f916cca9a851b6b9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034201"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure CLI サンプル

次の表には、Azure Cosmos DB の Azure CLI スクリプトのサンプルへのリンクが含まれています。 Azure Cosmos DB CLI のすべてのコマンドのリファレンス ページは、[Azure CLI リファレンス](/cli/azure/cosmosdb)で確認できます。

| |  |
|---|---|
|**Azure Cosmos DB のアカウント、データベース、およびコンテナーの作成**||
| [SQL API を使用して Azure Cosmos DB アカウントを作成する](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Azure Cosmos DB の単一のアカウント、データベース、およびコンテナーを作成します。 |
| [Cosmos DB の MongoDB 用 API を使用して Azure Cosmos DB アカウントを作成する](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Cosmos DB の単一のアカウント、データベース、およびコレクションを作成します。 |
| [Gremlin API を使用して Azure Cosmos DB アカウントを作成する](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Cosmos DB の単一のアカウント、データベース、およびグラフを作成します。 |
| [Cassandra API を使用して Azure Cosmos DB アカウントを作成する](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Cosmos DB の単一のアカウントとデータベースを作成します。 |
| [Table API を使用して Azure Cosmos DB アカウントを作成する](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Cosmos DB の単一のアカウント、データベース、およびテーブルを作成します。 |
|**Azure Cosmos DB のスケーリング**||
| [コンテナーのスループットのスケーリング](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | コンテナーのプロビジョニング済みスループットを変更します。|
| [複数リージョンへの Azure Cosmos DB データベース アカウントのレプリケートと、フェールオーバーの優先順位の構成](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|指定したフェールオーバー優先順位で、複数リージョンにグローバルにアカウント データをレプリケートします。|
|**Azure Cosmos DB のセキュリティ保護**||
| [アカウント キーの取得](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | アカウントのプライマリおよびセカンダリのマスター書き込みキーと、プライマリおよびセカンダリの読み取り専用キーを取得します。|
| [Azure Cosmos DB の MongoDB 用 API を使用して構成された Cosmos アカウントの接続文字列を取得する](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | MongoDB アプリを Azure Cosmos DB アカウントに接続する接続文字列を取得します。|
| [アカウント キーの再生成](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|アカウントのキーを再生成します。|
| [ファイアウォールの作成](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 承認した一連のマシンやクラウド サービスからアカウントへのアクセスを制限するインバウンド IP アクセス制御ポリシーを作成します。|
|**高可用性、ディザスター リカバリー、バックアップと復元**||
| [フェールオーバー ポリシーの構成](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|アカウントのレプリケート先の各リージョンのフェールオーバー優先順位を設定します。|
|**Azure Cosmos DB とリソースの接続**||
| [Web アプリと Azure Cosmos DB の接続](../app-service/scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Azure Cosmos DB データベースと Azure Web アプリを作成し、接続します。|
|||
