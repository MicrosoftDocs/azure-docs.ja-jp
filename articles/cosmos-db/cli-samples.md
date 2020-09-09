---
title: Azure Cosmos DB の Azure CLI サンプル
description: Azure Cosmos DB の Azure CLI サンプル
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 954215f04525e850151fdad93af6e7272b41b3df
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498465"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure CLI サンプル

次の表には、Azure Cosmos DB の Azure CLI スクリプトのサンプルへのリンクが含まれています。 API 固有のサンプルに移動するには、右側のリンクを使用します。 共通サンプルは、すべての API で同じです。 Azure Cosmos DB CLI のすべてのコマンドのリファレンス ページは、[Azure CLI リファレンス](/cli/azure/cosmosdb)で確認できます。 Azure Cosmos DB CLI のサンプル スクリプトについては、[Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)もご覧ください。

これらのサンプルを使用するには、Azure CLI バージョン 2.9.1 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください

## <a name="common-samples"></a>一般的なサンプル

これらのサンプルは、すべての Azure Cosmos DB API に適用されます

|タスク | 説明 |
|---|---|
| [リージョンを追加またはフェールオーバーする](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | リージョンの追加、フェールオーバー優先度の変更、手動フェールオーバーのトリガーを行います。|
| [アカウント キーと接続文字列](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | アカウント キーと読み取り専用キーの表示、キーの再生成、および接続文字列の表示を行います。|
| [IP ファイアウォールを使用してセキュリティ保護する](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| IP ファイアウォールが構成された Cosmos アカウントを作成します。|
| [サービス エンドポイントを使用して新しいアカウントをセキュリティ保護する](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Cosmos アカウントを作成し、サービス エンドポイントを使用してセキュリティ保護します。|
| [サービス エンドポイントを使用して既存のアカウントをセキュリティ保護する](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| サブネットが最終的に構成されるときに、サービス エンドポイントを使用してセキュリティ保護されるように Cosmos アカウントを更新します。|
|||

## <a name="core-sql-api-samples"></a>Core (SQL) API サンプル

|タスク | 説明 |
|---|---|
| [Azure Cosmos アカウント、データベース、およびコンテナーを作成する](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Core (SQL) API 用の Azure Cosmos DB アカウント、データベース、コンテナーを作成します。 |
| [Azure Cosmos アカウント、データベース、自動スケーリングのコンテナーを作成する](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Core (SQL) API 用に Azure Cosmos DB アカウント、データベース、自動スケーリングのコンテナーを作成します。 |
| [スループットを変更する](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | データベースとコンテナーの RU/秒を更新します。|
| [リソースが削除されないようにロックする](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。|
|||

## <a name="mongodb-api-samples"></a>MongoDB API サンプル

|タスク | 説明 |
|---|---|
| [Azure Cosmos アカウント、データベース、およびコレクションを作成する](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| MongoDB API 用の Azure Cosmos DB アカウント、データベース、およびコレクションを作成します。 |
| [Azure Cosmos アカウント、自動スケーリングのデータベース、共有スループットの 2 つのコレクションを作成する](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| MongoDB API 用に Azure Cosmos DB アカウント、自動スケーリングのデータベース、共有スループットの 2 つのコレクションを作成します。 |
| [スループットを変更する](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | データベースとコレクションの RU/秒を更新します。|
| [リソースが削除されないようにロックする](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。|
|||

## <a name="cassandra-api-samples"></a>Cassandra API サンプル

|タスク | 説明 |
|---|---|
| [Azure Cosmos のアカウント、キースペース、およびテーブルを作成する](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cassandra API 用の Azure Cosmos DB のアカウント、キースペース、およびテーブルを作成します。 |
| [Azure Cosmos アカウント、キースペース、自動スケーリングのテーブルを作成する](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cassandra API 用に Azure Cosmos DB アカウント、キースペース、自動スケーリングのテーブルを作成します。 |
| [スループットを変更する](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | キースペースとテーブルに対する RU/秒を更新します。|
| [リソースが削除されないようにロックする](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。|
|||

## <a name="gremlin-api-samples"></a>Gremlin API サンプル

|タスク | 説明 |
|---|---|
| [Azure Cosmos のアカウント、データベース、およびグラフを作成する](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Gremlin API 用の Azure Cosmos DB のアカウント、データベース、およびグラフを作成します。 |
| [Azure Cosmos アカウント、データベース、自動スケーリングのグラフを作成する](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Gremlin API 用に Azure Cosmos DB アカウント、データベース、自動スケーリングのグラフを作成します。 |
| [スループットを変更する](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | データベースとグラフの RU/秒を更新します。|
| [リソースが削除されないようにロックする](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。|
|||

## <a name="table-api-samples"></a>Table API サンプル

|タスク | 説明 |
|---|---|
| [Azure Cosmos アカウントとテーブルを作成する](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Table API 用の Azure Cosmos DB アカウントとテーブルを作成します。 |
| [Azure Cosmos アカウントと自動スケーリングのテーブルを作成する](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Table API 用に Azure Cosmos DB アカウントと自動スケーリングのテーブルを作成します。 |
| [スループットを変更する](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | テーブルの RU/秒を更新します。|
| [リソースが削除されないようにロックする](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。|
|||
