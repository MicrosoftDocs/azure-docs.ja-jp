---
title: Azure Storage 環境にデータを読み込む - Team Data Science Process
description: データが格納および処理されるさまざまなターゲット環境にデータを取り込む方法について説明します。
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
ms.openlocfilehash: 46e911ad6bdf5a478d46e425f8700740ece56c6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96452673"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>分析用のストレージ環境にデータを読み込む

Team Data Science Process では、各ステージで最も適切な方法でデータを取り込んだり読み込んだりする必要があります。 データの取り込み先として、Azure Blob Storage、SQL Azure データベース、Azure VM 上の SQL Server、HDInsight (Hadoop)、Azure Synapse Analytics、Azure Machine Learning などがあります。 

以下の記事では、データが格納および処理されるさまざまなターゲット環境にデータを取り込む方法について説明されています。

* [Azure BLOB Storage](move-azure-blob.md) へ/から
* [Azure VM 上の SQL Server](move-sql-server-virtual-machine.md) へ
* [Azure SQL Database](move-sql-azure.md) へ
* [Hive テーブル](move-hive-tables.md)へ
* [SQL パーティション テーブル](parallel-load-sql-partitioned-tables.md)へ
* [オンプレミスの SQL Server](move-sql-azure-adf.md) から

技術的およびビジネス上のニーズと、ご利用のデータの最初の場所、形式、およびサイズに応じて、最適なデータ取り込みプランが決定されます。 最適なプランにいくつかの手順が含まれることは珍しくありません。 この一連のタスクには、データ探索、前処理、クリーニング、サンプリング、モデル トレーニングなどが含まれます。  Azure Data Factory は、データの移動と変換を調整する場合に推奨される Azure リソースです。
