---
title: "Azure BLOB コンテナー、SQL Server、Hive テーブルのデータのサンプリング | Microsoft Docs"
description: "さまざまな Azure 環境に格納されているデータを探索する方法について説明します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: fashah;garye;bradsev
ms.openlocfilehash: 1b99ce11709376294f6a2385b9f4ff3388b99ab7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="heading"></a>Azure BLOB コンテナー、SQL Server、Hive テーブルのデータのサンプリング
このドキュメントは、Azure に保存されているデータを収集する方法について取り上げたトピックに関連しています。次の 3 つの保存先が対象となります。

* **データ** をサンプリングするには、プログラムでダウンロードし、サンプル Python コードを使用します。
* **SQL Server データ** をサンプリングするには、SQL と Python プログラミング言語の両方を使用します。 
* **Hive テーブル データ** をサンプリングするには、Hive クエリを使用します。

次の**メニュー**は、これらの各 Azure ストレージ環境のデータをサンプリングする方法を説明するトピックにリンクしています。 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

このサンプリング タスクは、 [Team Data Science Process (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)の 1 ステップです。

**データをサンプリングする理由**

分析しようとしているデータセットが大規模な場合、データをダウンサンプリングして、小規模であっても典型的であり、管理しやすいサイズに減らすことが通常は推奨されます。 これにより、データの理解、探索、および特徴エンジニアリングが容易になります。 Cortana Analytics Process におけるダウンサンプリングの役割は、データ処理機能と機械学習モデルのプロトタイプをより迅速に作成できるようにすることです。

