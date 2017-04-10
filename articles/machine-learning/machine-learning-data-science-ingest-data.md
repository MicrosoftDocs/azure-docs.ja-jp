---
title: "分析用の Azure Storage 環境にデータを読み込む | Microsoft Docs"
description: "Azure Blob ストレージとの間のデータの移動"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: e899487e9445955cea3a9387c73ea7c5dca37ddc
ms.openlocfilehash: 7fbf3bfedca8fa57a5e9428c9399558992b4acbd


---
# <a name="load-data-into-storage-environments-for-analytics"></a>分析用のストレージ環境にデータを読み込む
Team Data Science Process では、さまざまなストレージ環境に取り込まれるデータまたは読み込まれるデータを、プロセスの各段階において最も適切な方法で処理または分析する必要があります。 処理に通常使用されるデータの取り込み先として、Azure Blob Storage、SQL Azure データベース、Azure VM 上の SQL Server、HDInsight (Hadoop)、Azure Machine Learning などがあります。 

[!INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

この **メニュー** は、データを保存および処理するこれらのターゲット環境にデータを取り込む方法について説明するトピックにリンクしています。

分析の目的を達成するためにデータを取り込む必要があるターゲット環境は、技術上およびビジネス上のニーズと、データの最初の保存場所、形式、およびサイズによって決まります。 予測モデルを構築するために必要なさまざまなタスクを実行するために、複数の環境間でデータを移動する必要があるシナリオも珍しくありません。 この一連のタスクには、データ探索、前処理、クリーニング、サンプリング、モデル トレーニングなどが含まれます。




<!--HONumber=Jan17_HO5-->


