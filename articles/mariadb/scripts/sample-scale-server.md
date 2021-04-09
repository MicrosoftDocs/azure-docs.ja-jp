---
title: CLI スクリプト - サーバーをスケーリングする - Azure Database for MariaDB
description: このサンプル CLI スクリプトでは、Azure Database for MariaDB サーバーのメトリックを照会したうえで、異なるパフォーマンス レベルにスケーリングします。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 71a2ad5b8d09812eb999ab75a3522caf5a8dd20a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664553"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure CLI を使用して Azure Database for MariaDB サーバーの監視とスケーリングを行う
このサンプル CLI スクリプトは、メトリックの照会後、単一の Azure Database for MariaDB サーバーのコンピューティングとストレージをスケーリングします。 コンピューティングはスケールアップまたはスケールダウンすることができます。 ストレージはスケールアップすることのみが可能です。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト
サブスクリプション ID を使用してスクリプトを更新してください。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **注** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | データベースのホストとなる MariaDB サーバーを作成します。 |
| [az mariadb server update](/cli/azure/mariadb/server#az-mariadb-server-update) | MariaDB サーバーのプロパティを更新します。 |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | リソースのメトリック値を一覧表示します。 |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ
- [Azure Database for MariaDB のコンピューティングとストレージ](../concepts-pricing-tiers.md)の詳細について学習します
- さらに他のスクリプトも試す: [Azure Database for MariaDB 用の Azure CLI サンプル](../sample-scripts-azure-cli.md)
- [Azure CLI](/cli/azure) の詳細について学習します
