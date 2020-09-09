---
title: Azure CLI のサンプル スクリプト - Batch ジョブを実行する
description: このスクリプトでは、Batch のジョブを作成してそのジョブに一連のタスクを追加します。 ジョブとそのタスクを監視する方法を説明します。
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: d8efce1bc8dc32070b770769104b7fdc38e683cd
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500233"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI の例: Azure Batch でのジョブとタスクの実行

このスクリプトでは、Batch のジョブを作成してそのジョブに一連のタスクを追加します。 ジョブとそのタスクを監視する方法を説明します。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.20 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Batch アカウントを作成します。 |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | コンピューティング ノードのプールを作成します。  |
| [az batch job create](/cli/azure/batch/job#az-batch-job-create) | Batch ジョブを作成します。  |
| [az batch task create](/cli/azure/batch/task#az-batch-task-create) | 指定された Batch ジョブにタスクを追加します。  |
| [az batch job set](/cli/azure/batch/job#az-batch-job-set) | Batch ジョブのプロパティを更新します。  |
| [az batch job show](/cli/azure/batch/job#az-batch-job-show) | 指定された Batch ジョブの詳細を取得します。  |
| [az batch task show](/cli/azure/batch/task#az-batch-task-show) | 指定された Batch ジョブから、タスクの詳細を取得します。  |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
