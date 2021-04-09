---
title: 'レシピ: ビッグ データの Cognitive Services を使用した予測メンテナンス'
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、ビッグ データの Cognitive Services を使用して分散異常検出を実行する方法を示します
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a583d74ae0bf421a7a863a65442d250489a2f8f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775393"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>レシピ: ビッグ データの Cognitive Services を使用した予測メンテナンス

このレシピでは、IoT デバイス予測メンテナンスのために Apache Spark で Azure Synapse Analytics および Cognitive Services を使用する方法を説明します。 [CosmosDB と Synapse Link](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) のサンプルに基づいて進めていきます。 単純にするために、このレシピでは、CosmosDB および Synapse Link を通じてストリーミング データを取得するのではなく、CSV ファイルから直接データを読み取ります。 Synapse Link のサンプルを参照することを強くお勧めします。

## <a name="hypothetical-scenario"></a>架空のシナリオ

架空のシナリオは、IoT デバイスが[蒸気タービン](https://en.wikipedia.org/wiki/Steam_turbine)を監視するパワー プラントです。 IoTSignals コレクションには、各タービンの 1 分あたりの回転数 (RPM) とメガワット (MW) のデータがあります。 蒸気タービンからのシグナルは分析され、異常なシグナルが検出されます。

データには、ランダムな頻度で外れ値がある可能性があります。 そのような状況では、回路保護のために、RPM の値が上昇し、MW 出力が低下します。 アイデアは、データが同時に、しかし異なるシグナルで変化するのを確認することです。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [サーバーレス Apache Spark プール](../../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)で構成された [Azure Synapse ワークスペース](../../../synapse-analytics/quickstart-create-workspace.md)

## <a name="setup"></a>セットアップ

### <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](../../cognitive-services-apis-create-account.md) または [Azure CLI](../../cognitive-services-apis-create-account-cli.md) を使用して、Translator のリソースを作成します。 次のこともできます。

- [Azure portal](https://portal.azure.com/) で既存のリソースを表示する。

このリソースのエンドポイントとキーを書き留めておきます。このガイドの後の方で必要になります。

## <a name="enter-your-service-keys"></a>サービス キーの入力

まず、キーと場所を追加します。

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>DataFrame にデータを読み取る

次に、IoTSignals ファイルを DataFrame に読み取ります。 Synapse ワークスペースで新しいノートブックを開き、ファイルから DataFrame を作成します。

```python
df_signals = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Spark での Cognitive Services による異常検出の実行

目標は、いつ問題が発生するかを把握し、予測的なメンテナンスを実行できるように、IoT デバイスからのシグナルが異常な値を出力していたインスタンスを見つけることです。 これを行うために、Spark で Anomaly Detector を使用します。

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

データを調べてみましょう。

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

このセルでは、次のような結果が生成されるはずです。

| timestamp           |   value | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | dev-7      | False       |
| 2020-05-01 18:33:52 |    2976 | dev-7      | False       |
| 2020-05-01 18:33:53 |    2714 | dev-7      | False       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>いずれかのデバイスの異常の視覚化

IoTSignals.csv には、複数の IoT デバイスからのシグナルがあります。 特定のデバイスに注目し、デバイスからの異常な出力を視覚化します。

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

これで、特定のデバイスの異常を表すデータフレームが作成されたので、これらの異常を視覚化できます。

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

成功すると、出力は次のようになります。

![Anomaly Detector のプロット](../media/anomaly-output.png)

## <a name="next-steps"></a>次のステップ

Azure Cognitive Services、Azure Synapse Analytics、および Azure CosmosDB を使用して大規模な予測メンテナンスを行う方法について説明します。 詳細については、[GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) で完全なサンプルを参照してください。
