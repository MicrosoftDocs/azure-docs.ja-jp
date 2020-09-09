---
title: チュートリアル:Azure Synapse Analytics の使用を開始する
description: このチュートリアルでは、Azure Synapse Analytics を設定して使用する基本的な手順について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075873"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Azure Synapse Analytics の使用を開始する

このチュートリアルは、Azure Synapse Analytics の主な機能領域に関するステップバイステップ ガイドです。 このチュートリアルは、Azure Synapse Analytics の主要なシナリオに関するガイド ツアーを希望する方にとって理想的な出発点となります。 このチュートリアルの各ステップを実行すると、完全に機能する Synapse ワークスペースが作成されます。このワークスペースでは、SQL、SQL オンデマンド、および Apache Spark を使用してデータの分析を開始できます。

次のことについて学習します。
* Azure サブスクリプションで Synapse ワークスペースをプロビジョニングする
* ADLSGEN2 アカウントでアクセス制御を構成し、Synapse ワークスペースとシームレスに連携させる
* NYCTaxi サンプル データを Synapse ワークスペースに読み込み、SQL、SQL オンデマンド、および Spark で使用できるようにする
* Synapse Studio を使用して SQL スクリプトと Synapse ノートブックを編集して実行する
* SQL テーブルと Spark テーブルにクエリを実行する
* SQL テーブルから Spark Dataframes にデータを読み込む
* Spark Dataframes から SQL テーブルにデータを読み込む
* ADLSGEN2 アカウントの内容を調べる
* Spark と SQL オンデマンドを使用して ADLSGEN2 アカウントで parquet データ ファイルを分析する 
* Synapse ノートブックを 1 時間ごとに自動的に実行するデータ パイプラインを作成する

次に示す "*順番に*" 各ステップを実行すると、さまざまな機能を確認して、コア機能を実行する方法を学ぶことができます。

* [ステップ 1 - Synapse ワークスペースを作成する](get-started-create-workspace.md)
* [ステップ 2 - SQL プールを使用して分析する](get-started-analyze-sql-pool.md)
* [ステップ 3 - Spark を使用して分析する](get-started-analyze-spark.md)
* [ステップ 4 - SQL オンデマンドを使用して分析する](get-started-analyze-sql-on-demand.md)
* [ステップ 5 - ストレージ アカウント内のデータを分析する](get-started-analyze-storage.md)
* [ステップ 6 - パイプラインを使用してオーケストレーションする](get-started-pipelines.md)
* [ステップ 7 - Power BI を使用してデータを視覚化する](get-started-visualize-power-bi.md)
