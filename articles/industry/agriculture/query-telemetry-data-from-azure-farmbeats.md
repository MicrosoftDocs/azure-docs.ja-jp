---
title: 取り込まれたテレメトリ データのクエリ
description: この記事では、取り込まれたテレメトリ データにクエリを実行する方法について説明します。
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: b9067e2f78c8098d4a21263ac89caf03da631274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677484"
---
# <a name="query-ingested-telemetry-data"></a>取り込まれたテレメトリ データのクエリ

この記事では、Azure FarmBeats から取り込まれたセンサー データにクエリを実行する方法について説明します。

デバイスやセンサーなどのモノのインターネット (IoT) リソースからデータを取り込むことは、FarmBeats では一般的なシナリオです。 デバイスとセンサーのメタデータを作成してから、その履歴データを正規形式で FarmBeats に取り込みます。 FarmBeats データ ハブでセンサー データを使用できるようになったら、同じようにクエリを実行し、実用的な分析情報を生成したり、モデルをビルドしたりすることができます。

## <a name="before-you-begin"></a>開始する前に

この記事を読み進める前に、FarmBeats がインストールされており、お使いの IoT デバイスから FarmBeats にセンサー テレメトリ データが取り込まれていることを確認してください。

センサー テレメトリ データを取り込む場合は、「[過去のテレメトリ データの取り込み](ingest-historical-telemetry-data-in-azure-farmbeats.md)」を参照してください

また、API を使用して、取り込まれたテレメトリにクエリを実行するので、作業を続行する前に、FarmBeats REST API について理解しておいてください。 FarmBeats API の詳細については、[FarmBeats REST API](rest-api-in-azure-farmbeats.md) に関する記事を参照してください。 **FarmBeats Datahub エンドポイントに対して API 要求を行うことができることを確認してください**。

## <a name="query-ingested-sensor-telemetry-data"></a>取り込まれたセンサー テレメトリ データにクエリを実行する

FarmBeats からテレメトリ データにアクセスしてクエリを実行する場合、次の 2 つの方法があります:

- API と
- Time Series Insights (TSI)。

### <a name="query-using-rest-api"></a>REST API を使用してクエリを実行する

FarmBeats REST API を使用して、取り込まれたセンサー テレメトリ データにクエリを実行するには、以下の手順に従います。

1. 関心があるセンサーを特定します。 これは、/Sensor API で GET 要求を実行することで行うことができます。

> [!NOTE]
> 関心があるセンサー オブジェクトの **id** と **sensorModelId**。

2. 手順 1 でメモした **sensorModelId** について、/SensorModel API で GET/{id} を実行します。 "センサー モデル" には、センサーから取り込まれたテレメトリに関するすべてのメタデータと詳細が含まれています。 たとえば、**センサー モデル** オブジェクト内の **センサー メジャー** には、センサーによって送信されるメジャーおよびその種類と単位に関する詳細が含まれます。 たとえば、次のように入力します。

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
センサー モデルの GET/{id} 呼び出しからの応答をメモしておきます。

3. 次の入力ペイロードを使用して、/Telemetry API で POST 呼び出しを実行します

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. /Telemetry API からの応答は、このようになります。

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
上の例の応答では、クエリが実行されたセンサー テレメトリで、2 つのタイムスタンプのデータが示され、メジャー名 ("moist_soil_last") と報告されたテレメトリの値がその 2 つのタイムスタンプに含まれています。 報告された値の種類と単位を解釈するには、関連付けられているセンサー モデル (手順 2 で説明) を参照する必要があります。

### <a name="query-using-azure-time-series-insights-tsi"></a>Azure Time Series Insights (TSI) を使用してクエリを実行する

FarmBeats では [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) を利用して、IoT スケールでデータを取り込み、格納し、クエリを実行して視覚化します。これは、豊富なコンテキスト情報が付加され、時系列に合わせて最適化されたデータです。

テレメトリ データは EventHub で受信されてから処理され、FarmBeats リソース グループ内の TSI 環境にプッシュされます。 その後、TSI から直接、データにクエリを実行することができます。 詳細については、[TSI のドキュメント](../../time-series-insights/time-series-insights-explorer.md)を参照してください。

TSI でデータを視覚化するには、以下の手順に従います。

1. **[Azure portal]**  >  **[FarmBeats DataHub resource group]\(FarmBeats DataHub リソース グループ\)** の順に移動し、 **[Time Series Insights]** 環境 (tsi-xxxx) > **[データ アクセス ポリシー]** を選択します。 閲覧者または共同作成者のアクセス権を持つユーザーを追加します。
2. **[Time Series Insights]** 環境 (tsi-xxxx) の **[概要]** ページに移動し、 **[Time Series Insights エクスプローラーの URL]** を選択します。 これで、取り込まれたテレメトリを視覚化できるようになります。

TSI では、テレメトリの格納、クエリの実行および視覚化だけでなく、Power BI ダッシュボードとの統合も可能です。 詳細については、[このページ]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)を参照してください。

## <a name="next-steps"></a>次のステップ

これで、Azure FarmBeats インスタンスからのセンサー データにクエリを実行しました。 次に、ファームの[マップを生成する](generate-maps-in-azure-farmbeats.md#generate-maps)方法について説明します。