---
title: Azure CLI:Azure SQL Database の単一データベースを監視してスケーリングする
description: Azure CLI サンプル スクリプトを使用し、Azure SQL Database の単一データベースを監視してスケーリングします。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: bd2f8005300aeb77a88be2609246f2d760154e36
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232007"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Azure CLI を使用して Azure SQL Database の単一データベースを監視してスケーリングする

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

この Azure CLI サンプル スクリプトは、Azure SQL Database の単一データベースのサイズ情報を照会した後、データベースを別のコンピューティング サイズにスケーリングします。

Azure CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>スクリプトを実行する

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) を使用してデータベースで実行された操作の一覧を取得し、[az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) を使用してデータベースに対する更新操作を取り消します。

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

リソース グループと、それに関連付けられているすべてのリソースを削除するには、次のコマンドを使用します。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| スクリプト | 説明 |
|---|---|
| [az sql server](/cli/azure/sql/server) | サーバーのコマンド。 |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | データベース サイズの使用量に関する情報を表示します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の CLI サンプル スクリプトは、[Azure CLI サンプル スクリプト](../az-cli-script-samples-content-guide.md)のページにあります。
