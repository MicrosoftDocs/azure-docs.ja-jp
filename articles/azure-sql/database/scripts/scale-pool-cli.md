---
title: Azure CLI:エラスティック プールのスケール設定
description: Azure CLI サンプル スクリプトを使用して、Azure SQL Database のエラスティック プールをスケーリングします。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 30d462dec8c65252afbde0c4d2eee171891a64ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747314"
---
# <a name="use-the-azure-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Azure CLI を使用して Azure SQL Database のエラスティック プールをスケーリングする

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

この Azure CLI サンプル スクリプトは、Azure SQL Database にエラスティック プールを作成し、プールされたデータベースを移動して、エラスティック プールのコンピューティング サイズを変更します。

Azure CLI をローカルにインストールして使用することを選択した場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>スクリプトを実行する

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | 説明 |
|---|---|
| [az sql server](/cli/azure/sql/server) | サーバーのコマンド。 |
| [az sql db](/cli/azure/sql/db) | データベースのコマンド。 |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | エラスティック プールのコマンド。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../az-cli-script-samples-content-guide.md)のページにあります。
