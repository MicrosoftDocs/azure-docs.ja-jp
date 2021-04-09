---
title: 別の Azure Storage の場所にあるデータをサンプリングする - Team Data Science Process
description: Azure BLOB コンテナー、SQL Server、Hive テーブルのデータをサンプリングして、小さくても代表的で管理しやすいサイズに縮小します。
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
ms.openlocfilehash: 878a2a47dfaddf1d3399a1dbd98865332838e148
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321750"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Azure BLOB コンテナー、SQL Server、および Hive テーブルのデータをサンプリングする

以下の記事では、3 つの異なる Azure の場所のいずれかに格納されているデータをサンプリングする方法が説明されています。

* [**Azure BLOB コンテナーのデータ**](sample-data-blob.md)は、プログラムでダウンロードした後、サンプルの Python コードを使用してサンプリングします。
* [**SQL Server のデータ**](sample-data-sql-server.md)は、SQL と Python プログラミング言語の両方を使用してサンプリングします。 
* [**Hive テーブルのデータ**](sample-data-hive.md)は、Hive クエリを使用してサンプリングします。

このサンプリング タスクは、 [Team Data Science Process (TDSP)](./index.yml)の 1 ステップです。

**データをサンプリングする理由**

分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。 ダウンサイズにより、データの理解、探索、および特徴エンジニアリングが容易になります。 Cortana Analytics Process におけるサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプをより迅速に作成できるようにすることです。