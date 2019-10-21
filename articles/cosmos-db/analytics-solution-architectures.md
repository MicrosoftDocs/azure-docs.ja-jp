---
title: Azure Cosmos DB でグローバル分散型分析を使用するソリューション。
description: Azure Cosmos DB でグローバル分散型分析を使用して作成できるソリューションについて学習します。
author: rimman
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: d5c6b8727a24a7ea8ddf05f7983618b55884d3bf
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71341178"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Azure Cosmos DB でグローバル分散型分析を使用するソリューション

この記事では、Azure Cosmos DB でグローバル分散型分析を使用して作成できるソリューションについて説明します。

## <a name="retail-and-consumer-goods"></a>小売/一般消費財

Azure Cosmos DB の Spark サポートを使用して、リアルタイムのおすすめ候補とプランを提供できます。 リアルタイムのパーソナル化と製品のおすすめ候補を使用して、顧客が必要とする項目を検出できるよう支援できます。

* Apache Spark ランタイムによって提供される組み込みの Machine Learning サポートを使用して、製品カタログ全体でリアルタイムのおすすめ候補を生成できます。

* ストリーム データ、購入データ、および顧客データをマイニングしてクリックし、有効期間の値を指定する対象を絞ったおすすめ候補を提供することができます。

* Azure Cosmos DB のグローバル配布機能を使用すると、複数のリージョンに分散されている大量の製品データをミリ秒単位で分析できます。

* 地理的に分散されているユーザーやデータに関する分析情報をすばやく得られます。 適切な広告を適切なユーザーに適切なタイミングで提供することにより、プロモーション変換率を向上させることができます。

* 組み込みの Spark ストリーミング機能を活用して、静的な顧客データと組み合わせてライブ データを強化することができます。 こうすることで、顧客が何をしているかに合わせて、よりパーソナライズされた広告や対象を絞った広告をリアルタイムで配信できます。

次の図は Azure Cosmos DB Spark サポートを使用して価格とプロモーションを最適化する方法を示しています。

![価格とプロモーションを最適化するための Azure Cosmos DB の Spark サポート](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


次の図は Azure Cosmos DB Spark サポートをリアルタイムのおすすめ候補エンジンで使用する方法を示しています。

![リアルタイムのおすすめ候補エンジンでの Azure Cosmos DB Spark サポート](./media/spark-api-introduction/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>製造と IoT

Azure Cosmos DB の組み込みの分析プラットフォームを使用すると、世界規模で何百万ものデバイスからの IoT データをリアルタイムに分析できます。 気象パターンの予測、予測分析、エネルギーの最適化など、最新のイノベーションを行うことができます。

* Azure Cosmos DB を使用すると、リアルタイム資産のメトリックや気象要因などのデータをマイニングし、スマート グリッド分析を適用して、フィールドで接続されているデバイスのパフォーマンスを最適化することができます。 スマート グリッド分析は、運用コストを制御し、グリッドの信頼性を向上させ、パーソナライズされたエネルギー サービスをコンシューマーに提供するための鍵です。

次の図は、IoT デバイスからメトリックを読み取り、スマート グリッド分析を適用するために Azure Cosmos DB の Spark サポートを使用する方法を示しています。

![IoT デバイスからメトリックを読み取るための Azure Cosmos DB の Spark サポート](./media/spark-api-introduction/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>予測的なメンテナンス

* 小規模なドリル マシンで使用される圧縮機のような資産を、深い水中のプラットフォームに維持することは複雑な作業です。 これらの資産は世界各地に配置され、ペタバイト単位のデータを生成します。 Azure Cosmos DB を使用すると、エンドツーエンドの予測データ パイプラインを構築できます。このパイプラインでは、Spark ストリーミングを使用して大量のセンサー テレメトリを処理し、資産パーツを保存し、マッピング データを検出します。

* 機械学習モデルを構築およびデプロイして、資産の故障を発生前に予測し、障害が発生する前にメンテナンス作業を命令することができます。

次の図は Azure Cosmos DB の Spark サポートを使用して予測メンテナンス システムを構築する方法を示しています。

![予測メンテナンス システムを構築するための Azure Cosmos DB の Spark サポート](./media/spark-api-introduction/predictive-maintenance-system.png)

次の図は Azure Cosmos DB の Spark サポートを使用してリアルタイムの車両診断システムを構築する方法を示しています。

![リアルタイムの車両診断システムを構築するための Azure Cosmos DB の Spark サポート](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Gaming

* Azure Cosmos DB では、組み込みの Spark サポートにより、高度な分析および機械学習モデルを数分で簡単にビルド、スケーリング、デプロイできるので、最適なゲーム エクスペリエンスをビルドできます。

* プレーヤー、購入、行動データを分析して、関連するパーソナライズされたプランを作成し、高いコンバージョン率を得ることができます。

* Spark 機械学習を使用して、ゲーム テレメトリ データを分析し、分析情報を得ることができます。 低速の読み込み時間とゲーム中の問題を診断して防止できます。

次の図は Azure Cosmos DB の Spark サポートをゲーム分析で使用する方法を示しています。

![ゲーム分析での Azure Cosmos DB の Spark サポート](./media/spark-api-introduction/gaming-analytics.png)

## <a name="next-steps"></a>次の手順

* Azure Cosmos DB の長所については、[こちら](introduction.md)の概要の記事をご覧ください。
* [MongoDB 用の Azure Cosmos DB API を使ってみる](mongodb-introduction.md)
* [Azure Cosmos DB Cassandra API を使ってみる](cassandra-introduction.md)
* [Azure Cosmos DB Gremlin API を使ってみる](graph-introduction.md)
* [Azure Cosmos DB Table API を使ってみる](table-introduction.md)
