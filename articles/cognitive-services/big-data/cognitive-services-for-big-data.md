---
title: ビッグ データの Cognitive Services
description: Python、Java、および Scala を使用して大規模なデータセットで Azure Cognitive Services を活用する方法について説明します。 ビッグ データ向けの Cognitive Services を使用すると、継続的に改善されるインテリジェントなモデルを Apache Spark&trade; と SQL の計算に直接埋め込むことができます。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: 0001002636362a74915f9dae21a6beff52baea63
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86201790"
---
# <a name="azure-cognitive-services-for-big-data"></a>ビッグ データ向けの Azure Cognitive Services

![ビッグ データ向けの Azure Cognitive Services](media/cognitive-services-big-data-overview.svg)

ビッグ データ向けの Azure Cognitive Services を使用すると、ユーザーは [Apache Spark&trade;](https://docs.microsoft.com/dotnet/spark/what-is-spark) を使用して、テラバイト単位のデータを Cognitive Services に流すことができます。 ビッグ データ向けの Cognitive Services があれば、データストアを使用する大規模なインテリジェント アプリケーションを簡単に作成できます。

ビッグ データ向けの Cognitive Services を使用すると、継続的に改善されるインテリジェントなモデルを Apache Spark&trade; と SQL の計算に直接埋め込むことができます。 これらのツールにより、開発者は低レベルの細かいネットワーク設定から解放され、スマートな分散アプリケーションの作成に集中できます。

## <a name="features-and-benefits"></a>機能とメリット

ビッグ データ向けの Cognitive Services では、世界中の任意のリージョンのサービスに加えて、[コンテナー化された Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support) も使用できます。 コンテナーは、応答の待機時間が非常に短い、接続性が低いか接続のないデプロイをサポートします。 コンテナー化された Cognitive Services は、ローカルで、または Spark クラスターのワーカー ノードで直接実行できます。Kubernetes のような外部のオーケストレーターで実行することもできます。

## <a name="supported-services"></a>サポートされているサービス

API および SDK 経由でアクセスする [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) を使用すると、開発者は AI またはデータ サイエンスのスキルがなくてもインテリジェントなアプリケーションを構築できます。 Cognitive Services により、見る、聞く、話す、理解する、推論するという能力をアプリケーションに持たせることができます。 Cognitive Services を使用するには、ご使用のアプリケーションがこのサービスにネットワーク経由でデータを送信する必要があります。 このサービスはデータを受信すると、インテリジェントな応答を返します。 ビッグ データ ワークロードについては、次のサービスを使用できます。

### <a name="vision"></a>視覚

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Computer Vision")| Computer Vision サービスを使用すると、イメージを処理して情報を返すための高度なアルゴリズムにアクセスできます。 |
|[Face](https://docs.microsoft.com/azure/cognitive-services/face/ "Face")| Face サービスは、顔属性の検出と認識を有効にする、高度な顔アルゴリズムへのアクセスを提供します。 |

### <a name="speech"></a>音声

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Speech サービス](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Speech サービス")|Speech サービスを使用すると、音声認識、音声合成、音声翻訳、話者認証および識別などの機能にアクセスできます。|

### <a name="decision"></a>決定

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Anomaly Detector](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Anomaly Detector") | Anomaly Detector (プレビュー) サービスを使用すると、時系列データを監視し、その中の異常を検出できます。|

### <a name="language"></a>Language

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Text Analytics")| Text Analytics サービスは、未加工のテキストに、センチメント分析、キー フレーズ抽出、および言語検出のための自然言語処理を提供します。|

### <a name="search"></a>検索

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Bing Image Search")|Bing Image Search サービスは、ユーザーのクエリに関連すると判断されたイメージの表示を返します。|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>ビッグ データ向けの Cognitive Services でサポートされているプログラミング言語

ビッグ データ向けの Cognitive Services は Apache Spark を基に構築されています。 Apache Spark は、Java、Scala、Python、R などのさまざまな言語をサポートする分散コンピューティング ライブラリです。 現在サポートされている言語は次のとおりです。

### <a name="python"></a>Python

PySpark API は、[Microsoft ML for Apache Spark](https://aka.ms/spark) の `mmlspark.cognitive` 名前空間にあります。 詳細については、 [Python デベロッパー API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html) を参照してください。 使用例については、[Python のサンプル](samples-python.md)を参照してください。

### <a name="scala-and-java"></a>Scala および Java

Scala および Java ベースの Spark API は、[Microsoft ML for Apache Spark](https://aka.ms/spark) の `com.microsoft.ml.spark.cognitive` 名前空間にあります。 詳細については、 [Scala デベロッパー API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package) を参照してください。 使用例については、[Scala のサンプル](samples-scala.md)を参照してください。

## <a name="supported-platforms-and-connectors"></a>サポートされているプラットフォームとコネクタ

ビッグ データ向けの Cognitive Services には Apache Spark が必要です。 ビッグ データ向けの Cognitive Services をサポートする Apache Spark プラットフォームは複数あります。

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) は、Microsoft Azure クラウド サービス プラットフォーム用に最適化された Apache Spark ベースの分析プラットフォームです。 Databricks は、ワンクリックでのセットアップ、効率化されたワークフロー、およびデータ サイエンティスト、データ エンジニア、ビジネス アナリストの間のコラボレーションをサポートにする対話型ワークスペースを実現します。

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure Synapse Analytics](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/synapse-analytics) (旧称 SQL Data Warehouse) は、大規模な並列処理を使用するエンタープライズ データ ウェアハウスです。 Synapse Analytics を使用すると、ペタバイト単位のデータに対して複雑なクエリをすばやく実行できます。 Azure Synapse Analytics は、直感的な Jupyter Notebook インターフェイスを使用して Spark ジョブを実行するマネージド Spark プールを提供します。

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/) は、Docker コンテナーと分散アプリケーションを大規模なスケールで調整します。 AKS は、Azure での Kubernetes の使用を簡略化するマネージド Kubernetes オファリングです。 Kubernetes を使用すると、Cognitive Service のスケール、待機時間、およびネットワークをきめ細かく制御できます。 ただし、Apache Spark に慣れていない場合は、Azure Databricks または Synapse Analytics を使用することをお勧めします。

### <a name="data-connectors"></a>データ コネクタ

Spark クラスターを作成したら、次の手順はデータに接続することです。 Apache Spark にはさまざまな種類のデータベース コネクタがあります。 これらのコネクタにより、アプリケーションは、格納されている場所に関係なく、大規模なデータセットを操作できます。 サポートされているデータベースとコネクタの詳細については、[Azure Databricks でサポートされているデータソースの一覧](https://docs.microsoft.com/azure/databricks/data/data-sources/)を参照してください。

## <a name="concepts"></a>概念

### <a name="spark"></a>Spark

[Apache Spark&trade;](http://spark.apache.org/) は、大規模なデータ処理のための統合された分析エンジンです。 その並列処理フレームワークにより、ビッグデータと分析アプリケーションのパフォーマンスが向上します。 Spark は、コアのアプリケーション コードを変更することなく、バッチ処理システムとストリーム処理システムの両方として動作できます。

Spark の基盤となるのは、Apache Spark ワーカー ノード全体に分散された表形式のデータ コレクションである DataFrame です。 Spark の DataFrame は、リレーショナル データベースのテーブルや R/Python のデータ フレームと似ていますが、スケール制限がありません。 DataFrame は、構造化されたデータ ファイル、Hive 内のテーブル、外部データベースなど、多くのソースから構築できます。 データが Spark の DataFrame に入ると、次のことが可能になります。

   - テーブルの結合やフィルターなどの SQL スタイルの計算を実行します。
   - MapReduce スタイルの並列処理を使用して、大規模なデータセットに関数を適用します。
   - Apache Spark の Microsoft Machine Learning を使用して分散機械学習を適用します。
   - ビッグ データ向けの Cognitive Services を使用して、すぐに使えるインテリジェント サービスでデータを強化します。

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Apache Spark の Microsoft Machine Learning (MMLSpark)

[Apache Spark の Microsoft Machine Learning](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) は、Apache Spark 上に構築されたオープンソースの分散機械学習ライブラリ (ML) です。 このパッケージには、ビッグ データ向けの Cognitive Services が含まれています。 さらに MMLSpark には、LightGBM、Vowpal Wabbit、OpenCV、LIME など、Apache Spark 用の他の ML ツールがいくつか含まれています。 MMLSpark を使用すると、任意の Spark データソースから強力な予測モデルと分析モデルを構築できます。

### <a name="http-on-spark"></a>HTTP on Spark

ビッグ データ向けの Cognitive Services は、インテリジェントな Web サービスをビッグ データと統合する方法の一例です。 Web サービスによって世界中の多くのアプリケーションの機能が強化され、ほとんどすべてのサービスは、ハイパーテキスト転送プロトコル (HTTP) を介して通信します。 *任意の* Web サービスを大規模に操作するために、HTTP on Spark が提供されました。 HTTP on Spark を使用すると、テラバイト単位のデータを任意の Web サービスを介して渡すことができます。 内部的にこのテクノロジを使用して、ビッグ データ向けの Cognitive Services が強化されています。

## <a name="developer-samples"></a>開発者向けサンプル

- [レシピ:予測メンテナンス](recipes/anomaly-detection.md)
- [レシピ:インテリジェントなアートの探索](recipes/art-explorer.md)

## <a name="blog-posts"></a>ブログ記事

- [Apache Spark での Cognitive Services の機能の詳細を見る&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Spark のディープ ラーニングと Computer Vision によるユキヒョウの保護](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Microsoft Research Podcast:MMLSpark、Mark Hamilton による AI for Good の強化](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)

## <a name="webinars-and-videos"></a>ウェビナーとビデオ

- [Spark での Azure Cognitive Services:埋め込みインテリジェント サービスを提供するクラスター](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Spark サミットの基調講演:拡張性に優れた AI for Good](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cosmos DB でのビッグ データ向けの Cognitive Services](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)

## <a name="next-steps"></a>次のステップ

- [ビッグ データ向けの Cognitive Services の概要](getting-started.md)
- [単純な Python のサンプル](samples-python.md)
- [単純な Scala のサンプル](samples-scala.md)
