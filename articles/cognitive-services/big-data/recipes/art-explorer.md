---
title: 'レシピ: ビッグ データの Cognitive Services を使用したインテリジェントなアートの探索'
titleSuffix: Azure Cognitive Services
description: このレシピでは、Azure Search と MMLSpark を使用して検索可能なアート データベースを作成する方法について説明します。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94363291"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>レシピ: ビッグ データの Cognitive Services を使用したインテリジェントなアートの探索

この例では、ビッグ データの Cognitive Services を使用して、メトロポリタン美術館 (MET) のオープン アクセス コレクションにインテリジェントな注釈を追加します。 これにより、手動で注釈を付けなくても、Azure Search を使用してインテリジェントな検索エンジンを作成できるようになります。 

## <a name="prerequisites"></a>前提条件

* Computer Vision と Cognitive Search のサブスクリプション キーが必要です。 Computer Vision にサブスクライブし、キーを取得するには、[Cognitive Services アカウントの作成](../../cognitive-services-apis-create-account.md)に関するページの手順に従います。
  > [!NOTE]
  > 価格の情報については、[Azure Cognitive Search](https://azure.microsoft.com/services/search/#pricing) の価格に関するセクションを参照してください。

## <a name="import-libraries"></a>ライブラリのインポート

次のコマンドを実行して、このレシピにライブラリをインポートします。

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>サブスクリプション キーのセットアップ

次のコマンドを実行して、サービス キーの変数をセットアップします。 Computer Vision および Azure Cognitive Search のサブスクリプション キーを挿入します。

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>データの読み取り

次のコマンドを実行して、MET のオープン アクセス コレクションからデータを読み込みます。

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>画像の分析

次のコマンドを実行して、MET のオープン アクセス アートワーク コレクションに対して Computer Vision を使用します。 その結果、アートワークのビジュアル機能を利用できるようになります。

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>検索インデックスの作成

次のコマンドを実行して、結果を Azure Search に書き込み、Computer Vision からの強化されたメタデータを含むアートワークの検索エンジンを作成します。

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>検索インデックスに対するクエリ

次のコマンドを実行して、Azure Search インデックスに対するクエリを実行します。

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>次のステップ

[異常検出に対するビッグ データの Cognitive Services](anomaly-detection.md) の使用方法を学習します。