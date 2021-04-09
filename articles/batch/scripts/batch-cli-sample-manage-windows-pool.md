---
title: Azure CLI のサンプル スクリプト - Batch での Windows プール
description: このスクリプトでは、Azure Batch で Windows コンピューティング ノードのプールを作成して管理するために、Azure CLI で使用できるコマンドの一部を示します。
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: fb18f9d8777c17d31a3ab246603df0d9fa162467
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100941"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>CLI の例: Azure Batch での Windows プールの作成と管理

このスクリプトでは、Azure Batch で Windows コンピューティング ノードのプールを作成して管理するために、Azure CLI で使用できるコマンドの一部を示します。 Windows プールは、クラウド サービスの構成と仮想マシンの構成という 2 つの方法で構成できます。 この例では、クラウド サービスの構成を使用して Windows プールを作成する方法を示します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.20 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

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
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。 |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | コンピューティング ノードのプールを作成します。  |
| [az batch pool set](/cli/azure/batch/pool#az-batch-pool-set) | プールのプロパティを更新します。  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-enable) | プールで自動スケーリングを有効にし、式を適用します。  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | プールのプロパティを表示します。  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-disable) | プールで自動スケーリングを無効にします。 |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |


## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
