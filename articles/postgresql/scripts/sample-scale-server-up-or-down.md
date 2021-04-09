---
title: Azure CLI スクリプト - Azure Database for PostgreSQL のスケーリングと監視
description: Azure CLI スクリプト サンプル - Azure Database for PostgreSQL サーバーのメトリックを照会したうえで、異なるパフォーマンス レベルにスケーリングします。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 6bbf5f3a0a7d32425f80687de10444ee0819b9df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660459"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Azure CLI での単一の PostgreSQL サーバーの監視とスケーリング
このサンプル CLI スクリプトは、メトリックの照会後、単一の Azure Database for PostgreSQL サーバーのコンピューティングとストレージをスケーリングします。 コンピューティングはスケールアップまたはスケールダウンすることができます。 ストレージはスケールアップすることのみが可能です。 

> [!IMPORTANT] 
> ストレージはスケールアップのみ可能で、スケールダウンすることはできません。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト
サブスクリプション ID を使用してスクリプトを更新してください。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **注** |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | データベースをホストする PostgreSQL サーバーを作成します。 |
| [az postgres server update](/cli/azure/postgres/server#az-postgres-server-update) | PostgreSQL サーバーのプロパティを更新します。 |
| [az monitor metrics list](/cli/azure/monitor/metrics) | リソースのメトリック値を一覧表示します。 |
| [az group delete](/cli/azure/group) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ
- [Azure Database for PostgreSQL のコンピューティングとストレージ](../concepts-pricing-tiers.md)の詳細について学習します
- さらに他のスクリプトも試す: [Azure Database for PostgreSQL 用 Azure CLI サンプル](../sample-scripts-azure-cli.md)
- [Azure CLI](/cli/azure) の詳細について学習します
