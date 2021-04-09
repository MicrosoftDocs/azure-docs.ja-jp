---
title: CLI の例 - フェールオーバー グループ - Azure SQL Managed Instance
description: Azure SQL Managed Instance を作成してフェールオーバー グループに追加し、フェールオーバーをテストする Azure CLI スクリプトの例。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: f196db537ef0a64d14930ed6bc67696ee4614c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86528907"
---
# <a name="use-cli-to-add-an-azure-sql-managed-instance-to-a-failover-group"></a>CLI を使用して Azure SQL Managed Instance をフェールオーバー グループに追加する

この Azure CLI サンプルでは、2 つのマネージド インスタンスを作成し、それらをフェールオーバー グループに追加して、プライマリ マネージド インスタンスからセカンダリ マネージド インスタンスへのフェールオーバーをテストします。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>スクリプトを実行する

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。 リソース グループは 2 回削除する必要があります。 最初にリソース グループを削除すると、マネージド インスタンスと仮想クラスターが削除されますが、その後、エラー メッセージ `az group delete : Long running operation failed with status 'Conflict'.` が表示されて失敗します。 az group delete コマンドをもう一度実行すると、残りのすべてのリソースと、リソース グループが削除されます。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | 説明 |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | 仮想ネットワークのコマンド。  |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | 仮想ネットワーク サブネットのコマンド。 |
| [az network nsg](/cli/azure/network/nsg) | ネットワーク セキュリティ グループのコマンド。 |
| [az network route-table](/cli/azure/network/route-table) | ルート テーブルのコマンド。 |
| [az sql mi](/cli/azure/sql/mi) | SQL Managed Instance のコマンド。 |
| [az network public-ip](/cli/azure/network/public-ip) | ネットワーク パブリック IP アドレスのコマンド。 |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | Virtual Network ゲートウェイのコマンド。 |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | SQL Managed Instance のフェールオーバー グループ コマンド。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../../azure-sql/database/az-cli-script-samples-content-guide.md)のページにあります。
