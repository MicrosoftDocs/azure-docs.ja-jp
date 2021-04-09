---
title: Azure Blob Storage との間でのデータの移動 - Team Data Science Process
description: Azure Storage Explorer、AzCopy、Python、および SSIS を使用して、Azure Blob Storage 間でデータを移動します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5148084fa22266b1352046c7d8737b9804c5f4d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93311860"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Azure Blob Storage との間のデータの移動

Team Data Science Process では、さまざまなストレージ環境に取り込まれるデータまたは読み込まれるデータを、プロセスの各段階において最も適切な方法で処理または分析する必要があります。

## <a name="different-technologies-for-moving-data"></a>データを移動するためのさまざまなテクノロジ

以下の記事では、さまざまなテクノロジを使用して Azure Blob Storage との間で双方向にデータを移動する方法について説明されています。

* [Azure Storage Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
* [Python](../../storage/blobs/storage-quickstart-blobs-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

最適な方法はシナリオによって異なります。 高度な分析プロセスで使用されるさまざまなデータ サイエンス ワークフローで必要なリソースを確認するには、記事「 [Azure Machine Learning での高度な分析のシナリオ](plan-sample-scenarios.md) 」が役立ちます。

> [!NOTE]
> Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../../storage/blobs/storage-quickstart-blobs-dotnet.md)に関する記事および [Azure BLOB サービス](/rest/api/storageservices/Blob-Service-Concepts)に関するページをご覧ください。
> 
> 

## <a name="using-azure-data-factory"></a>Azure Data Factory の使用

別の方法として、 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用して以下を行うこともできます。 

* Azure BLOB ストレージからデータをダウンロードするパイプラインを作成し、スケジュールを設定する 
* パイプラインを発行済みの Azure Machine Learning Web サービスに渡す 
* 予測分析の結果を受け取る 
* 結果をストレージにアップロードする 

詳細については、「 [Azure Data Factory および Azure Machine Learning を使用して予測パイプラインを作成する](../../data-factory/transform-data-using-machine-learning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
この記事は、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。 データのアップロードまたはダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。

* Azure サブスクリプションを設定するには、 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* ストレージ アカウントの作成と、アカウントとキー情報の取得の手順については、[Azure ストレージ アカウントについて](../../storage/common/storage-account-create.md)のページを参照してください。