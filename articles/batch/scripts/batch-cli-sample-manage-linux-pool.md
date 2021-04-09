---
title: Azure CLI のサンプル スクリプト - Batch での Linux プール
description: このスクリプトでは、Azure Batch で Linux コンピューティング ノードのプールを作成して管理するために使用できる Azure CLI のコマンドの一部を示します。
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b2e0fbf44be5718cf5577f6bc9aea436968e2fc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073561"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI の例: Azure Batch での Linux プールの作成と管理

このスクリプトでは、Azure Batch で Linux コンピューティング ノードのプールを作成して管理するために使用できる Azure CLI のコマンドの一部を示します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.20 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Batch アカウントを作成します。 |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | 使用できるノード エージェント SKU とイメージの情報を一覧表示します。  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | コンピューティング ノードのプールを作成します。  |
| [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) | 指定したプールで実行されている VM の数を変更します。  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | プールのプロパティを表示します。  |
| [az batch node list](/cli/azure/batch/node#az-batch-node-list) | 指定したプール内のすべてのコンピューティング ノードを一覧表示します。  |
| [az batch node reboot](/cli/azure/batch/node#az-batch-node-reboot) | 指定したコンピューティング ノードを再起動します。  |
| [az batch node delete](/cli/azure/batch/node#az-batch-node-delete) | 指定したプールから、一覧表示されているノードを削除します。  |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
