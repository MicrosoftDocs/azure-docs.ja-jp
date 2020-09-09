---
title: ビッグ データ向けの Cognitive Services の概要
description: Azure Databricks で Cognitive Services を使用して MMLSpark パイプラインを設定し、サンプルを実行します。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 7f4849c75b36b1663416ad1a97e3264fabcdaa67
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86201736"
---
# <a name="getting-started"></a>作業の開始

データのパイプラインを構築するための最初の手順は、環境をセットアップすることです。 環境の準備が整ったら、すばやく簡単にサンプルを実行できます。

この記事では、次の手順を実行して作業を開始します。

1. [Cognitive Services リソースを作成する](#create-a-cognitive-services-resource)
1. [Apache Spark クラスターを作成する](#create-an-apache-spark-cluster)
1. [サンプルを試す](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースの作成

ビッグデータ向けの Cognitive Services を使用するには、まず、ワークフロー用の Cognitive Service を作成する必要があります。 Cognitive Services には主に 2 つの種類があります。具体的には、Azure でホストされるクラウド サービスと、ユーザーが管理するコンテナー化されたサービスです。 より単純なクラウドベースの Cognitive Services から始めることをお勧めします。

### <a name="cloud-services"></a>クラウド サービス

クラウドベースの Cognitive Services は、Azure でホストされているインテリジェントなアルゴリズムです。 これらのサービスは、トレーニングなしですぐに使用でき、インターネット接続のみが必要です。 [Cognitive Service は Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli?tabs=windows) で作成できます。

### <a name="containerized-services-optional"></a>コンテナー化されたサービス (オプション)

アプリケーションまたはワークロードが非常に大規模なデータセットを使用している場合、プライベート ネットワークを必要とする場合、またはクラウドに接続できない場合は、クラウド サービスと通信できない可能性があります。 このような状況で、コンテナー化された Cognitive Services には次のような利点があります。

* **低い接続性**:コンテナー化された Cognitive Services は、クラウド内とクラウド外の両方のコンピューティング環境にデプロイできます。 アプリケーションがクラウドに接続できない場合、コンテナー化された Cognitive Services をアプリケーションにデプロイすることを検討してください。

* **低待機時間**:コンテナー化されたサービスはクラウドとの間のラウンドトリップ通信を必要としないため、応答が返されるときの待機時間が非常に短くなります。

* **プライバシーとデータ セキュリティ**:コンテナー化されたサービスをプライベート ネットワークに展開して、機密データがネットワークから出ないようにすることができます。

* **高いスケーラビリティ**:コンテナー化されたサービスには "レート制限" がなく、ユーザーが管理するコンピューター上で実行されます。 そのため、より大規模なワークロードを処理するために、Cognitive Services を際限なく拡張できます。

コンテナー化された Cognitive Service を作成するには、[こちらガイド](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support?tabs=luis)に従ってください。

## <a name="create-an-apache-spark-cluster"></a>Apache Spark クラスターの作成

[Apache Spark&trade;](http://spark.apache.org/) は、ビッグデータのデータ処理向けに設計された分散コンピューティング フレームワークです。 ユーザーは、Azure Databricks、Azure Synapse Analytics、HDInsight、Azure Kubernetes Services などのサービスと Apache Spark を Azure 内で一緒に使用できます。 ビッグ データ向けの Cognitive Services を使用するには、最初にクラスターを作成する必要があります。 Spark クラスターを既にお持ちの場合は、自由にサンプルを試してみてください。

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks は、Apache Spark ベースの分析プラットフォームです。ワンクリックでのセットアップと効率的なワークフロー、および対話型ワークスペースを提供します。 多くの場合、データ サイエンティスト、エンジニア、およびビジネス アナリスト間の共同作業に使用されます。 Azure Databricks でビッグ データ向けの Cognitive Services を使用するには、次の手順を実行します。

1. [Azure Databricks ワークスペースを作成します](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Databricks に Spark クラスターを作成する](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. ビッグ データ向けの Cognitive Services をインストールする
    * Databricks ワークスペースに新しいライブラリを作成します  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * 次の maven 座標を入力します。`com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc1` リポジトリ: `https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * ライブラリをクラスターにインストールします  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="synapse-analytics-optional"></a>Synapse Analytics (オプション)

必要に応じて、Synapse Analytics を使用して Spark クラスターを作成できます。 Azure Synapse Analytics では、エンタープライズ データ ウェアハウスとビッグ データ分析がまとめられています。 サーバーレスのオンデマンド リソースまたはプロビジョニング済みのリソースを大規模に使用しながら、各自の条件で自由にデータのクエリを実行できます。 Synapse Analytics の使用を開始するには、次の手順を実行します。

1. [Synapse ワークスペースを作成する (プレビュー)](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)。
1. [Azure portal を使用して Apache Spark プール (プレビュー) を作成する](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-portal)。

Synapse Analytics では、ビッグ データ向けの Cognitive Services が既定でインストールされています。

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

コンテナー化された Cognitive Services を使用している場合、Spark をコンテナーと共にデプロイするための一般的なオプションの 1 つが Azure Kubernetes Service です。

Azure Kubernetes Service の利用を開始するには、次の手順を実行します。

1. [Azure portal を使用して Azure Kubernetes Service (AKS) クラスターをデプロイする](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
1. [Apache Spark 2.4.0 Helm グラフをインストールする](https://hub.helm.sh/charts/microsoft/spark)
1. [Helm を使用して Cognitive Service コンテナーをインストールする](https://docs.microsoft.com/azure/cognitive-services/computer-vision/deploy-computer-vision-on-premises)

## <a name="try-a-sample"></a>サンプルを試す

Spark クラスターと環境を設定した後、短いサンプルを実行できます。 このセクションでは、ビッグ データ向けの Cognitive Services を Azure Databricks で使用する方法について説明します。

最初に、Azure Databricks でノートブックを作成できます。 他の Spark クラスター プロバイダーについては、それらのノートブックまたは Spark Submit を使用します。

1. **[Azure Databricks]** メニューから **[新しいノートブック]** を選択して、新しい Databricks ノートブックを作成します。

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. **[Create Notebook] (ノートブックの作成)** ダイアログ ボックスで、名前を入力し、言語として **[Python]** を選択してから、前に作成した Spark クラスターを選択します。

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    **［作成］** を選択します

1. 次のコード スニペットを新しいノートブックに貼り付けます。

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Azure portal の Text Analytics ダッシュボードの **[Keys and Endpoint]\(キーとエンドポイント\)** メニューからご自身のサブスクリプション キーを取得します。
1. Databricks ノートブック コード内のサブスクリプション キーのプレースホルダーを、ご自身のサブスクリプション キーで置き換えます。
1. ノートブック セルの右上にある三角形のプレイ記号を選択して、サンプルを実行します。 必要に応じて、ノートブックの上部にある **[Run All]\(すべて実行\)** を選択して、すべてのセルを実行します。 回答はテーブル内のセルの下に表示されます。

### <a name="expected-results"></a>予想される結果

| text                                      |   センチメント |
|:------------------------------------------|------------:|
| I am so happy today, its sunny!           |   0.978959  |
| I am frustrated by this rush hour traffic |   0.0237956 |
| The cognitive services on spark aint bad  |   0.888896  |

## <a name="next-steps"></a>次のステップ

- [短い Python のサンプル](samples-python.md)
- [短い Scala のサンプル](samples-scala.md)
- [レシピ:予測メンテナンス](recipes/anomaly-detection.md)
- [レシピ:インテリジェントなアートの探索](recipes/art-explorer.md)
