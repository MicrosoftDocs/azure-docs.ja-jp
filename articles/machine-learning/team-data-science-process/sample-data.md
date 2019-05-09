---
title: 別の Azure ストレージの場所にあるデータをサンプリングする - Team Data Science Process
description: Azure BLOB コンテナー、SQL Server、Hive テーブルのデータをサンプリングして、小さくても代表的で管理しやすいサイズに縮小します。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc91aec80f74488125649cfe807757ba5ae49c9b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466338"
---
# <a name="heading"></a>Azure BLOB コンテナー、SQL Server、Hive テーブルのデータのサンプリング

以下の記事では、3 つの異なる Azure の場所のいずれかに格納されているデータをサンプリングする方法が説明されています。

* [**Azure BLOB コンテナーのデータ**](sample-data-blob.md)は、プログラムでダウンロードした後、サンプルの Python コードを使用してサンプリングします。
* [**SQL Server のデータ**](sample-data-sql-server.md)は、SQL と Python プログラミング言語の両方を使用してサンプリングします。 
* [**Hive テーブルのデータ**](sample-data-hive.md)は、Hive クエリを使用してサンプリングします。

このサンプリング タスクは、 [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)の 1 ステップです。

**データをサンプリングする理由**

分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。 これにより、データの理解、探索、および特徴エンジニアリングが容易になります。 Cortana Analytics Process におけるダウンサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプをより迅速に作成できるようにすることです。

