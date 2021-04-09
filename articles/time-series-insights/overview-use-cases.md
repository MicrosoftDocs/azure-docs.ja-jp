---
title: Gen2 のユース ケース - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 のユース ケースについて説明します。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7d97958c5fd1274495da88c064b63e59e354f691
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606965"
---
# <a name="azure-time-series-insights-gen2-use-cases"></a>Azure Time Series Insights Gen2 のユース ケース

この記事では、Azure Time Series Insights Gen2 のいくつかの一般的なユース ケースの概要を説明します。 この記事に記載されているレコメンデーションは、Azure Time Series Insights Gen2 を使用したアプリケーションおよびソリューションを開発するための出発点として利用できます。

具体的にこの記事では、以下の質問に回答します。

* Azure Time Series Insights Gen2 の一般的なユース ケースとはどのようなものですか?
* [データ探索や視覚的な異常検出](#data-exploration-and-visual-anomaly-detection)に、Azure Time Series Insights Gen2 を使用する利点は何ですか?
* [運用分析やプロセス効率](#operational-analysis-and-driving-process-efficiency)に、Azure Time Series Insights Gen2 を使用する利点は何ですか?
* [高度な分析](#advanced-analytics)に、Azure Time Series Insights Gen2 を使用する利点は何ですか?

以下のセクションで、これらの使用シナリオの概要を説明します。

## <a name="introduction"></a>はじめに

Azure Time Series Insights Gen2 は、エンドツーエンドの、サービスとしてのプラットフォームのオファリングです。 豊富なコンテキスト情報を付加され、時系列に合わせて最適化された IoT スケールのデータの収集、処理、格納、分析、クエリに使用されます。 アドホックなデータ探索と運用分析に最適です。 Azure Time Series Insights Gen2 は、産業用 IoT デプロイの広範なニーズを満たす、個別に拡張可能なカスタマイズされたサービス オファリングです。

## <a name="data-exploration-and-visual-anomaly-detection"></a>データ探索と視覚的な異常検出

数十億件ものイベントを瞬時に調査して分析し、データの異常を特定し、潜んでいる傾向を明らかにします。 Azure Time Series Insights Gen2 は、IoT と DevOps の分析ワークロードでほぼリアルタイムのパフォーマンスを発揮します。

[![データ エクスプローラー](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

分析情報を最短時間で取得できることが Azure Time Series Insights Gen2 の特筆すべき機能の 1 つであることに、ほとんどのお客様が納得します。

* Azure Time Series Insights Gen2 では、事前のデータ準備は不要です。
* 高速に動作し、Azure IoT Hub や Azure Event Hubs インスタンスの数十億件ものイベントに数分で接続します。
* 接続したら、数十億件のイベントを視覚化して分析し、データの異常を特定し、潜んでいる傾向を明らかにします。

Azure Time Series Insights Gen2 は、直感的で使い方は簡単です。 コードを全く記述しなくてもデータを操作できます。 新しい言語を学ぶ必要もありません。Azure Time Series Insights Gen2 は、SQL を熟知した、詳しい知識があるユーザー向けの、詳細なテキストベースのクエリ言語を備えています。 初心者向けに、選択してクリックの探索も用意されています。

お客様は、スピードを活かして、資産関連の問題をすばやく診断できます。 DevOps 分析を実行して、IoT ソリューションのバグの根本原因を把握できます。 また、データ サイエンス イニシアティブの一貫として、さらなる調査に向け、フラグを設定する領域を特定することもできます。

Azure Time Series Insights Gen2 に格納されたデータを操作する方法は主に 3 つあります。

* 1 つ目の最も簡単な開始方法は、Azure Time Series Insights Gen2 エクスプローラーを使用することです。 それを使用することで、1 か所ですべての IoT データをすばやく可視化できます。 データの異常の特定に役立つヒートマップなどのツールを備えています。 パースペクティブ ビューも提供されています。 それを使用して、単一のダッシュボードで 1 つまたは複数の Azure Time Series Insights Gen2 環境からの最大 4 つのビューを比較できます。 ダッシュボードでは、すべての場所の時系列データを表示できます。 [Azure Time Series Insights Gen2 エクスプローラー](./concepts-ux-panels.md)について参照してください。 環境を計画するには、[Azure Time Series Insights Gen2 の計画](./how-to-plan-your-environment.md)に関するページを参照してください。

* 2 つ目の開始方法は、JavaScript SDK を使用して、Web アプリケーションに強力なチャートやグラフをすばやく埋め込むことです。 数行のコードで、強力なクエリを作成できます。 それらを使用して、折れ線グラフ、円グラフ、棒グラフ、ヒートマップ、データ グリッドなどのデータを入力します。 SDK を使用することで、これらのすべての要素がすぐに使用できる状態で存在します。 さらに、SDK は、Azure Time Series Insights Gen2 クエリ API を抽象化します。 それらを使用して、SQL に似た述語を作成して、ダッシュボードに表示するデータをクエリできます。 プレゼンテーション レイヤーのハイブリッド ソリューションに対して、Azure Time Series Insights Gen2 は、パラメーター化 URL を提供します。 それらは、データを深く探索するための Azure Time Series Insights Gen2 エクスプローラーとのシームレスな接続ポイントを提供します。

  * JavaScript SDK の詳細については、[JS クライアント ライブラリ](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)と[クライアントの例](https://github.com/Microsoft/tsiclient)のドキュメントを参照してください。

  * URL と新しい UI の共有の詳細について、[Azure Time Series Insights Gen2 エクスプローラーでのデータ視覚化](./concepts-ux-panels.md)に関するページを確認してください。

* 3 つ目の開始方法は、Azure Time Series Insights Gen2 に格納されているデータをクエリするために強力な API を使用することです。 Azure Time Series Insights Gen2 には、`from`、`to`、`first`、`last` などのテンポラル演算子があります。 `average`、`sum`、`min`、`max`、`time-weighted average`、`time-weighted sum` などの集計および変換があります。また、フィルター処理、算術演算子、ブール演算子、スカラー関数なども使用できます。これらすべての演算子により、ダウンストリーム アプリケーションではデータ内の興味のある傾向やパターンをすばやく見つけることができます。 それらを使用して、異常を検出するための独自の視覚化を設定します。

## <a name="operational-analysis-and-driving-process-efficiency"></a>運用分析とプロセス効率の向上

Azure Time Series Insights Gen2 を使用すると、大規模に機器の正常性、使用状況、パフォーマンスを監視したり、運用効率を測定したりすることができます。 Azure Time Series Insights Gen2 を使用すると、取り込みやクエリのパフォーマンスを損なうことなく予測不能な各種 IoT ワークロードを管理するのに役立ちます。

[![IoT デバイス、アプリケーション データ、ストリーム処理、操作効率性、インテリジェンス、Azure Time Series Insights Gen2 の分析情報と高度な分析のスクリーンショット。](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

運用プロセスからのデータのストリーミングと継続的処理により、適切なテクノロジやソリューションと組み合わせた場合に、あらゆるビジネスを適切に変換できます。 多くの場合、これらのソリューションは、複数のシステムの組み合わせになります。 これらにより、特に IoT 領域内の、絶えず変更され、共通のパターンを共有するデータの探索と分析が可能になります。

これらのパターンは、多くの場合、さまざまなロケールにまたがるデバイスとセンサーから、数十億のイベントを取り込む IoT 対応プラットフォームから始まります。 これらのシステムでは、ストリーミング データを処理および分析して、リアルタイムでインサイトおよびアクションを導き出します。 データは通常、ほぼリアルタイムの分析とバッチ分析のために、ウォームおよびコールド ストレージにアーカイブされます。

収集されたデータは、ダウンストリーム クエリおよび分析シナリオのために、一連の処理を経て、クレンジングされ、コンテキスト化されます。 Azure では、資産メンテナンスや製造などの IoT シナリオに適用できる豊富なサービスを提供しています。 これらのサービスには、Azure Time Series Insights Gen2、IoT Hub、Event Hubs、Azure Stream Analytics、Azure Functions、Azure Logic Apps、Azure Databricks、Azure Machine Learning、および Power BI が含まれます。

次のように、ソリューション アーキテクチャを実現できます。

* クラス最高のセキュリティ、スループット、待機時間で IoT Hub または Event Hubs 経由でデータを取り込みます。
* データの処理と計算を実行します。 Stream Analytics、Logic Apps、および Azure Functions などのサービスから取り込まれたデータをフィルターします。 使用するサービスは、特定のデータ処理ニーズによって異なります。
* 処理パイプラインから計算されたシグナルは、保存と分析のため、Azure Time Series Insights Gen2 にプッシュされます。

Azure Time Series Insights Gen2 は、履歴データに対してほぼリアルタイムのデータ探索と資産ベースの洞察を提供します。 Azure Time Series Insights Gen2 を Azure HDInsight に接続することで、ビジネス ニーズに応じて、MapReduce および Hive ジョブを、Azure Time Series Insights Gen2 に格納されたデータに対して実行できます。 Azure Time Series Insights Gen2 に格納されたデータは、Azure Time Series Insights Gen2 のパブリック サーフェス クエリ API を介して、Power BI およびその他のカスタマー アプリケーションから使用できます。 このデータは、さまざまなビジネスおよびオペレーション インテリジェンス シナリオで使用できます。

## <a name="advanced-analytics"></a>高度な分析

Machine Learning や Azure Databricks などの高度な分析サービスと統合します。 Azure Time Series Insights Gen2 は数百万のデバイスから生データを取り込みます。 Azure 分析サービスのスイートによって、シームレスに使用できるコンテキスト データを追加します。

[![分析](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

高度な分析と機械学習では、大量のデータを使用し、処理します。 このデータは、データドリブンの意思決定を行い、予測分析の実行に使われます。 IoT のユース ケースでは、高度な分析アルゴリズムが、数百万のデバイスから収集されたデータから学習します。 これらのデバイスは、毎秒複数回データを転送します。 IoT デバイスから収集されたデータは生データです。 デバイスの場所やセンサー読み取りの単位などのコンテキスト情報が不足しています。 そのため、生データを高度な分析に直接使用することは困難です。

Azure Time Series Insights Gen2 では、2 つの簡単なコスト効率の高い方法で IoT データと高度な分析のギャップを埋めます。

* まず、Azure Time Series Insights Gen2 は、IoT Hub を使用して数百万のデバイスから生のテレメトリ データを収集します。 コンテキスト情報によってデータを強化し、parquet 形式にデータを変換します。 この形式では、Machine Learning、Azure Databricks、およびサード パーティ アプリケーションなどの他の高度な分析サービスと簡単に統合できます。

    Azure Time Series Insights Gen2 は、組織全体のすべてのデータの信頼できるソースとして使用できます。 使用するダウンストリーム分析ワークロード用の中央リポジトリを作成します。 Azure Time Series Insights Gen2 は、ほぼリアルタイムのストレージ サービスであるため、高度な分析モデルが、受信する IoT テレメトリ データから継続的に学習できます。 そのため、モデルはより正確な予測ができます。

* 2 つ目に、機械学習と予測モデルの出力を Azure Time Series Insights Gen2 に取り込めば、その結果を視覚化して格納できます。 この手順により、組織はモデルを最適化し、調整できます。 Azure Time Series Insights Gen2 により、トレーニング済みモデルの出力と同じ平面上にストリーミング テレメトリ データを簡単に視覚化できます。 このように、データ サイエンス チームが異常を特定し、パターンを識別するために役立ちます。

## <a name="next-steps"></a>次のステップ

* [Azure Time Series Insights Gen2 エクスプローラー](./concepts-ux-panels.md)について参照してください。
* 環境を計画するには、[Azure Time Series Insights Gen2 のベスト プラクティス](./how-to-plan-your-environment.md)に関するページを参照してください。
* [クライアントの例](https://github.com/Microsoft/tsiclient)のドキュメントを参照してください。
