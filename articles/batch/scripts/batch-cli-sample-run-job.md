---
title: "Azure CLI スクリプト サンプル - Batch でのジョブの実行 | Microsoft Docs"
description: "Azure CLI スクリプト サンプル - Batch でのジョブの実行"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: 73d93622d418359be421e043d0af4e4befc6f4b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Azure CLI を使用して Azure Batch でジョブを実行する

このスクリプトでは、Batch のジョブを作成してそのジョブに一連のタスクを追加します。 ジョブとそのタスクを監視する方法を説明します。 最後に、ジョブのタスクに関する情報を Batch サービスで効率的にクエリする方法を示します。

## <a name="prerequisites"></a>前提条件

- Azure CLI をまだインストールしていない場合は、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)の手順に従ってインストールします。
- Batch アカウントをまだお持ちでない場合は、作成します。 アカウント作成のサンプル スクリプトについては、「[Azure CLI で Batch アカウントを作成する](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account)」を参照してください。
- 開始タスクからアプリケーションを実行するように構成していない場合は、そのように構成します。 アプリケーションを作成し、アプリケーション パッケージを Azure にアップロードするサンプル スクリプトについては、「[Azure CLI を使用した Azure Batch へのアプリケーションの追加](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application)」を参照してください。
- ジョブを実行するプールを構成します。 クラウド サービス構成または仮想マシン構成でプールを作成するサンプル スクリプトについては、「[Azure CLI を使用した Azure Batch プールの管理](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool)」を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-job"></a>ジョブのクリーンアップ

上記のサンプル スクリプトを実行したら、次のコマンドを実行して、ジョブとそのすべてのタスクを削除します。 プールは別に削除する必要があることに注意してください。 プールの作成と削除の詳細については、「[Azure CLI を使用した Azure Batch プールの管理](./batch-cli-sample-manage-pool.md)」を参照してください。

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを使用して Batch のジョブとタスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Batch アカウントに対して認証します。  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | Batch ジョブを作成します。  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | Batch ジョブのプロパティを更新します。  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | 指定された Batch ジョブの詳細を取得します。  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | 指定された Batch ジョブにタスクを追加します。  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | 指定された Batch ジョブから、タスクの詳細を取得します。  |
| [az batch task list](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_list) | 指定したジョブに関連するタスクを一覧表示します。  |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Batch CLI サンプル スクリプトは、[Azure Batch CLI のドキュメント](../batch-cli-samples.md)のページにあります。
