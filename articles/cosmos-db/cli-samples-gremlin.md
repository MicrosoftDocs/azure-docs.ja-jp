---
title: Azure Cosmos DB Gremlin API 用の Azure CLI サンプル
description: Azure Cosmos DB Gremlin API 用の Azure CLI サンプル
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ff557e5ca0769b68cb74f4b790e3678da7c3c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342115"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API 用の Azure CLI サンプル
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

次の表には、Azure Cosmos DB の Azure CLI スクリプトのサンプルへのリンクが含まれています。 API 固有のサンプルに移動するには、右側のリンクを使用します。 共通サンプルは、すべての API で同じです。 Azure Cosmos DB CLI のすべてのコマンドのリファレンス ページは、[Azure CLI リファレンス](/cli/azure/cosmosdb)で確認できます。 Azure Cosmos DB CLI のサンプル スクリプトについては、[Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)もご覧ください。

これらのサンプルを使用するには、Azure CLI バージョン 2.12.1 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください

## <a name="common-samples"></a>一般的なサンプル

これらのサンプルは、すべての Azure Cosmos DB API に適用されます

|タスク | 説明 |
|---|---|
| [リージョンを追加またはフェールオーバーする](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | リージョンの追加、フェールオーバー優先度の変更、手動フェールオーバーのトリガーを行います。|
| [アカウント キーと接続文字列](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | アカウント キーと読み取り専用キーの表示、キーの再生成、および接続文字列の表示を行います。|
| [IP ファイアウォールを使用してセキュリティ保護する](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| IP ファイアウォールが構成された Cosmos アカウントを作成します。|
| [サービス エンドポイントを使用して新しいアカウントをセキュリティ保護する](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Cosmos アカウントを作成し、サービス エンドポイントを使用してセキュリティ保護します。|
| [サービス エンドポイントを使用して既存のアカウントをセキュリティ保護する](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| サブネットが最終的に構成されるときに、サービス エンドポイントを使用してセキュリティ保護されるように Cosmos アカウントを更新します。|
|||

## <a name="gremlin-api-samples"></a>Gremlin API サンプル

|タスク | 説明 |
|---|---|
| [Azure Cosmos のアカウント、データベース、およびグラフを作成する](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Gremlin API 用の Azure Cosmos DB のアカウント、データベース、およびグラフを作成します。 |
| [Azure Cosmos アカウント、データベース、自動スケーリングのグラフを作成する](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Gremlin API 用に Azure Cosmos DB アカウント、データベース、自動スケーリングのグラフを作成します。 |
| [スループット操作](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | データベースとグラフに対する読み取り、更新、および自動スケーリングと標準スループット間の移行を行います。|
| [リソースが削除されないようにロックする](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。|
|||
