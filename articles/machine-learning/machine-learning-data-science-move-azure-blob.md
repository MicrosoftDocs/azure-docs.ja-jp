---
title: "Azure Blob Storage との間のデータの移動 | Microsoft Docs"
description: "Azure Blob ストレージとの間のデータの移動"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;sachouks
translationtype: Human Translation
ms.sourcegitcommit: 9b32c0d9c3bc19a187873eebd6ab21036ee06db8
ms.openlocfilehash: 1e773a1332d14109e9f50fb7db17528d061a98f2
ms.lasthandoff: 12/07/2016


---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Azure Blob Storage との間のデータの移動
[!INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

最適な方法はシナリオによって異なります。 高度な分析プロセスで使用されるさまざまなデータ サイエンス ワークフローで必要なリソースを確認するには、記事「 [Azure Machine Learning での高度な分析のシナリオ](machine-learning-data-science-plan-sample-scenarios.md) 」が役立ちます。

> [!NOTE]
> Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../storage/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。
> 
> 

別の方法として、 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用して以下を行うこともできます。 

* Azure BLOB ストレージからデータをダウンロードするパイプラインを作成し、スケジュールを設定する 
* パイプラインを発行済みの Azure Machine Learning Web サービスに渡す 
* 予測分析の結果を受け取る 
* 結果をストレージにアップロードする 

詳細については、「 [Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する](../data-factory/data-factory-azure-ml-batch-execution-activity.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。 データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

* Azure サブスクリプションを設定するには、 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* ストレージ アカウントの作成と、アカウントとキー情報の取得の手順については、「 [Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。


