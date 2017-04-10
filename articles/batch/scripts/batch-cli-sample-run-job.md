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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: d5ef87e6e7092820a65c5736c1942fd5cec57462
ms.lasthandoff: 03/24/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Azure CLI を使用して Azure Batch でジョブを実行する

このスクリプトでは、Batch のジョブを作成してそのジョブに一連のタスクを追加します。 ジョブとそのタスクを監視する方法を説明します。
このスクリプトの実行では、Batch アカウントが既に設定されており、プールとアプリケーションの両方が構成されていると想定しています。 詳細については、これらの各トピックに関連した[サンプル スクリプト](../batch-cli-samples.md)に関するページをご覧ください。

必要に応じて、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページの手順に従って Azure CLI をインストールし、`az login` を実行して Azure にログインします。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "ジョブの実行")]

## <a name="clean-up-job"></a>ジョブのクリーンアップ

上記のサンプル スクリプトを実行したら、次のコマンドを実行して、ジョブとそのすべてのタスクを削除します。 プールは別途削除する必要があることに注意してください。[プールの管理に関するチュートリアル](./batch-cli-sample-manage-pool.md)に関する記事を参照してください。

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを使用して Batch のジョブとタスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Batch アカウントに対して認証します。  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#create) | Batch ジョブを作成します。  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#set) | Batch ジョブのプロパティを更新します。  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#show) | 指定された Batch ジョブの詳細を取得します。  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#create) | 指定された Batch ジョブにタスクを追加します。  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#show) | 指定された Batch ジョブから、タスクの詳細を取得します。  |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Batch CLI サンプル スクリプトは、[Azure Batch CLI のドキュメント](../batch-cli-samples.md)のページにあります。

