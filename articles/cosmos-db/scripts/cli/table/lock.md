---
title: Azure Cosmos DB Table API テーブルのリソース ロックを作成する
description: Azure Cosmos DB Table API テーブルのリソース ロックを作成する
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: b6041bf493cf3cb6dcff5f52bdb0950afbbc5108
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87431477"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Azure CLI を使用して Azure Cosmos DB Table API テーブルのリソース ロックを作成する

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用することを選択した場合、このトピックでは、Azure CLI のバージョン 2.9.1 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

> [!IMPORTANT]
> `disableKeyBasedMetadataWriteAccess` プロパティが有効にされている状態で Cosmos DB アカウントが最初にロックされない限り、リソース ロックは、ユーザーが Cosmos DB Table SDK、Azure Storage Table SDK、アカウント キーを介して接続されるツール、または Azure portal に接続して行った変更に対しては機能しません。 このプロパティを有効にする方法の詳細については、[SDK からの変更の防止](../../../role-based-access-control.md#prevent-sdk-changes)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | ロックを作成します。 |
| [az lock list](/cli/azure/lock#az-lock-list) | ロック情報を一覧表示します。 |
| [az lock show](/cli/azure/lock#az-lock-show) | ロックのプロパティを表示します。 |
| [az lock delete](/cli/azure/lock#az-lock-delete) | ロックを削除します。 |

## <a name="next-steps"></a>次のステップ

-[リソースのロックによる予期せぬ変更の防止](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI のドキュメント](/cli/azure/cosmosdb)

-[Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。
