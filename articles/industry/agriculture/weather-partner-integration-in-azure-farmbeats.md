---
title: 気象パートナーの統合
description: 気象データ プロバイダーが FarmBeats と統合する方法についてDescriptionします。
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147081"
---
# <a name="weather-partner-integration-with-farmbeats"></a>気象パートナーと FarmBeats の統合

この記事では、Azure FarmBeats Connector Docker コンポーネントに関する情報を提供します。 気象データ プロバイダーは、Connector Docker を使用して FarmBeats と統合できます。 その API を使用して、気象データを FarmBeats に送信します。 FarmBeats では、データ融合や、機械学習モデルまたは人工知能モデルの構築に、データを使用できます。

 > [!NOTE]
 > この記事では、Azure Open Datasets と米国海洋大気庁 (NOAA) からの気象データを使用して構築された[リファレンス実装](https://github.com/azurefarmbeats/noaa_docker)を使用します。 また、対応する [Docker イメージ](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)も使用します。

[適切な Docker イメージまたはプログラム](#docker-specifications)を提供し、顧客がアクセスできるコンテナー レジストリで Docker イメージをホストする必要があります。 次の情報を顧客に提供します。

- Docker イメージの URL
- Docker イメージ タグ
- Docker イメージにアクセスするためのキーまたは資格情報
- システムからのデータにアクセスするための顧客固有の API キーまたは資格情報
- VM SKU の詳細 (Docker イメージに固有の VM 要件がある場合は、これらの詳細を提供します。 それ以外の場合は、顧客は Azure でサポートされている VM SKU から選択できます。)

顧客は、この Docker 情報を使用して、FarmBeats インスタンスに気象パートナーを登録します。 顧客が Docker を使用して気象データを FarmBeats に取り込む方法の詳細については、[気象パートナーからのデータの取得](./get-weather-data-from-weather-partner.md)に関するページを参照してください。

## <a name="connector-docker-development"></a>Connector Docker の開発

**REST API ベースの統合**

FarmBeats API には、Swagger の技術文書が含まれています。 API とそれに対応する要求または応答の詳細については、[FarmBeats の Swagger](https://aka.ms/farmbeatsswagger) を参照してください。 

既に FarmBeats をインストールしてある場合は、`https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger` で FarmBeats の Swagger にアクセスします

FarmBeats の Web サイト名に *-api* を追加することに注意してください。 API のエンドポイントは `https://yourfarmbeatswebsitename-api.azurewebsites.net` です

### <a name="datahub-lib"></a>データ ハブ ライブラリ

FarmBeats により、使用できるライブラリが提供されます。 このライブラリは、現在、[リファレンス実装の一部](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)として入手できます。 将来は、複数の言語の SDK として使用できるようになります。

### <a name="authentication"></a>認証

**FarmBeats API による認証**

FarmBeats の場合、ベアラー認証が使用されます。 要求のヘッダー セクションでアクセス トークンを渡すことにより、API にアクセスできます。 次に例を示します。

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

アクセス トークンは、顧客の FarmBeats インスタンスで実行されている Azure Functions アプリに要求できます。 Azure Functions の URL は、引数として Docker プログラムに提供されます。 その URL に対して `GET` 要求を行うことで、アクセス トークンを取得できます。 URL からの応答に、アクセス トークンが含まれています。 

Datahub ライブラリのヘルパー関数を使用して、アクセス トークンを取得します。 詳細については、[NOAA Docker イメージの GitHub ページ](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)を参照してください。

アクセス トークンは数時間だけ有効です。 有効期限が切れた場合は、もう一度要求する必要があります。

**パートナー側の API による認証**

Docker の実行中にパートナー側の API を使用して認証を行うには、顧客はパートナーの登録時に資格情報を提供する必要があります。 次に例を示します。

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API サービスにより、この辞書がシリアル化されて、[キー コンテナー](../../key-vault/general/basic-concepts.md)に格納されます。

気象ジョブを調整するには、[Azure Data Factory](../../data-factory/introduction.md) を使用します。 それにより、Docker コードを実行するためにリソースがスピンアップされます。 また、Docker ジョブが実行される VM にデータを安全にプッシュするメカニズムも、Data Factory により提供されます。 その後、API 資格情報はキー コンテナーに安全に格納されます。 

資格情報は、セキュリティで保護された文字列としてキー コンテナーから読み取られます。 それらは、Docker コンテナーの作業ディレクトリ内の拡張プロパティとして提供されます。 ファイル パスは */mnt/working_dir/activity.json* です。 

Docker コードを使用して、実行時に *activity.json* から資格情報を読み取り、顧客に対するパートナー側 API にアクセスできます。 JSON ファイル内の資格情報は、次のコード例のようになっています。

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
`partnerCredentials` 資格情報は、パートナーの登録時に顧客が指定した方法で使用できます。

FarmBeats ライブラリでヘルパー関数が提供されてます。 これらの関数を使用して、アクティビティのプロパティから資格情報を読み取ります。 詳細については、[NOAA Docker イメージの GitHub ページ](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)を参照してください。

このファイルは、Docker コードの実行中にだけ使用されます。 コードが完了すると、ファイルは削除されます。

Data Factory のパイプラインとアクティビティの仕組みについてくわしくは、[スキーマとデータ型のマッピング](../../data-factory/copy-activity-schema-and-type-mapping.md)に関するページを参照してください。

**HTTP 要求ヘッダー**

次の表では、FarmBeats への API 呼び出しを行うときに指定する必要がある最も一般的な要求ヘッダーを示します。

ヘッダー | 説明と例
--- | ---
Content-Type | 要求の形式。 例: `Content-Type: application/<format>` <br/>FarmBeats Datahub API の場合、形式は JSON です。 例: ` Content-Type: application/json`
承認 | API 呼び出しを行うために必要なアクセス トークンを指定。 例: `Authorization: Bearer <Access-Token>`
Accept | 応答形式。 FarmBeats Datahub API の場合、形式は JSON です。 例: `Accept: application/json`

## <a name="data-format"></a>データ形式

JSON は、任意のデータ構造をシンプルなテキストで表現する、言語に依存しない一般的なデータ形式です。 詳細については、[JSON.org](https://json.org) を参照してください。

## <a name="docker-specifications"></a>Docker の仕様

Docker プログラムには、ブートストラップとジョブという 2 つのコンポーネントが必要です。 1 つのプログラムで複数のジョブを使用できます。

### <a name="bootstrap"></a>ブートストラップ

ブートストラップ コンポーネントは、顧客が FarmBeats で Docker の登録を開始したときに実行される必要があります。 次の引数 (`arg1` と `arg2`) がプログラムに渡されます。

- **FarmBeats API エンドポイント**: API 要求用の FarmBeats API エンドポイント。 このエンドポイントにより、FarmBeats のデプロイに対する API 呼び出しが行われます。
- **Azure Functions URL**: 独自のエンドポイント。 この URL により、FarmBeats API に対するアクセス トークンが提供されます。 この URL で `GET` を呼び出して、アクセス トークンを取得できます。

ブートストラップにより、ユーザーがジョブを実行して気象データを取得するために必要なメタデータが作成されます。 詳細については、[リファレンス実装](https://github.com/azurefarmbeats/noaa_docker)を参照してください。 

*bootstrap_manifest.json* ファイルをカスタマイズすると、必要なメタデータがリファレンス ブートストラップ プログラムによって自動的に作成されます。 ブートストラップ プログラムにより、次のメタデータが作成されます。 

 > [!NOTE]
 > [リファレンス実装](https://github.com/azurefarmbeats/noaa_docker)で説明されているように *bootstrap_manifest.json* ファイルを更新する場合は、次のメタデータを作成する必要はありません。 必要なメタデータは、ブートストラップ プログラムによりマニフェスト ファイルを使用して作成されます。

- /**WeatherDataModel**: WeatherDataModel メタデータは、気象データを表します。 これは、ソースによって提供されるデータ セットに対応します。 たとえば、DailyForecastSimpleModel により、平均温度、湿度、降水の情報が 1 日に 1 回提供される場合があります。 これに対し、DailyForecastAdvancedModel からは、1 時間ごとの細分性でさらに多くの情報が提供される場合があります。 任意の数の気象データ モデルを作成できます。
- /**JobType**: FarmBeats には、拡張可能なジョブ管理システムが用意されています。 気象データ プロバイダーは、さまざまなデータセットと API (たとえば、GetDailyForecasts) を使用します。 これらのデータ セットと API は、JobType を使用して FarmBeats で有効にすることができます。 ジョブの種類が作成された後、顧客は、その種類のジョブをトリガーして、自分がいる場所や気になるファームなどの気象データを取得できます。 詳細については、[FarmBeats の Swagger](https://aka.ms/farmbeatsswagger) で JobType API と Job API を参照してください。

### <a name="jobs"></a>ジョブ

ジョブ コンポーネントは、ブートストラップ プロセスの一環として作成された /JobType のジョブを FarmBeats ユーザーが実行するたびに呼び出されます。 ジョブの Docker 実行コマンドは、作成した /JobType の一部として定義されています。

ジョブにより、ソースからデータがフェッチされて、FarmBeats にプッシュされます。 ブートストラップ プロセスの間に、データの取得に必要なパラメーターが、/JobType の一部として定義される必要があります。

ジョブの一部として、プログラムで、ブートストラップ プロセスの間に作成された /WeatherDataModel に基づいて /WeatherDataLocation を作成する必要があります。 /WeatherDataLocation は、ユーザーがジョブのパラメーターとして指定した場所 (緯度と経度の座標) に対応します。

### <a name="object-details"></a>オブジェクトの詳細

WeatherDataModel | [説明] |
--- | ---
名前  | 気象データ モデルの名前。 |
説明  | モデルについてのわかりやすい説明。 |
Properties  | データ プロバイダーによって定義される追加プロパティ。 |
weatherMeasures > Name  | 気象メジャーの名前。 たとえば、humidity_max。 |
weatherMeasures > DataType  | Double または Enum。 Enum の場合は、measureEnumDefinition が必要です。 |
weatherMeasures > measureEnumDefinition  | DataType が Enum の場合のみ必要。 たとえば、`{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` のように指定します。 |
weatherMeasures > Type  | 気象テレメトリ データの種類。 たとえば、RelativeHumidity。 システムで定義された種類は、AmbientTemperature、NoUnit、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、Pressure、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR です。 さらに種類を追加するには、この記事の「[ExtendedType を追加する](#add-extendedtype)」セクションを参照してください。
weatherMeasures > Unit | 気象テレメトリ データの単位。 システムで定義された単位は、NoUnit、Celsius、Fahrenheit、Kelvin、Rankine、Pascal、Mercury、PSI、MilliMeter、CentiMeter、Meter、Inch、Feet、Mile、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、Degree、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、Percentage、PartsPerMillion、MicroMole、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、Liter、MilliLiter、Seconds、UnixTimestamp、MicroMolePerMeterSquaredPerSecond、InchesPerHour です。 さらに単位を追加するには、この記事の「[ExtendedType を追加する](#add-extendedtype)」セクションを参照してください。
weatherMeasures > AggregationType  | 集計の種類。 指定できる値は、None、Average、Maximum、Minimum、StandardDeviation、Sum、Total です。
weatherMeasures > Depth  | センサーの深さ (センチメートル単位)。 たとえば、地面より 10 cm 下の湿度。
weatherMeasures > Description  | メジャーについてのわかりやすい説明。 

JobType | 説明 |
--- | ---
名前  | ジョブの名前。 たとえば、Get_Daily_Forecast。 顧客は、このジョブを実行して気象データを取得します。|
pipelineDetails > parameters > name  | パラメーターの名前。 |
pipelineDetails > parameters > type | パラメーターの型。 指定できる値は、String、Int、Float、Bool、Array です。 |
pipelineDetails > parameters > isRequired | パラメーターのブール値。 パラメーターが必須の場合、値は true です。 それ以外の場合、値は false です。 既定値は true です。 |
pipelineDetails > parameters > defaultValue | パラメーターの既定値。 |
pipelineDetails > parameters > description | パラメーターの説明。 |
Properties  | 製造元から提供されるその他のプロパティ。
Properties > programRunCommand | Docker の実行コマンド。 顧客が気象ジョブを実行すると、このコマンドが実行されます。 |

WeatherDataLocation | 説明 |
--- | ---
weatherDataModelId  | ブートストラップ プロセスの間に作成された対応する WeatherDataModel の ID。|
location  | 緯度、経度、高度。 |
名前 | オブジェクト名。 |
説明 | 気象データの場所の説明。 |
farmId | (省略可能) ファームの ID。 顧客は、この ID をジョブ パラメーターの一部として指定します。 |
Properties  | 製造元から提供されるその他のプロパティ。

オブジェクトとそのプロパティの詳細については、[FarmBeats の Swagger](https://aka.ms/FarmBeatsSwagger) を参照してください。

> [!NOTE]
> API からは、作成されたインスタンスごとに一意の ID が返されます。 デバイス管理とメタデータ同期のため変換では、この ID をそのままにする必要があります。

**メタデータの同期**

Connector Docker コンポーネントにより、メタデータの更新を送信できる必要があります。 たとえば、気象プロバイダーが新しいパラメーターをデータセットに追加したときや、新しい機能 (新しい 30 日予報など) が追加されたときに、更新を送信する必要があります。

> [!NOTE]
> 気象データ モデルのメタデータの削除はサポートされていません。
>
> メタデータを更新するには、気象データ モデルで `/Get/{ID}` を呼び出す必要があります。 変更されたプロパティを更新した後、`/Put/{ID}` を行って、ユーザーが設定したプロパティを保持します。

## <a name="weather-data-telemetry-specifications"></a>気象データ (テレメトリ) の仕様

気象データは、処理のために Azure Event Hub にプッシュされる正規のメッセージにマップされます。 Azure Event Hubs は、接続されているデバイスやアプリケーションからリアルタイムでデータ (テレメトリ) の取り込みを可能にするサービスです。 

気象データを FarmBeats に送信するには、FarmBeats のイベント ハブにメッセージを送信するクライアントを作成します。 詳細については、[イベント ハブへのテレメトリの送信](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)に関するページを参照してください。

指定されたイベント ハブにクライアントとしてテレメトリを送信する Python コードの例を次に示します。

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

正規のメッセージ形式は次のとおりです。

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

テレメトリ メッセージの例を次に示します。

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>トラブルシューティングとエラーの管理

### <a name="error-logging"></a>エラーのログ記録

パートナー ジョブは、既存のジョブ フレームワークで実行されます。 そのため、エラーは、他の既存の FarmBeats ジョブ (GetFarmData や SensorPlacement など) のエラーと同じようにログに記録されます。 Data Factory パイプライン内で実行される Data Factory アクティビティの場合は、`STDERR` と `STDOUT` の両方にログが記録されます。 どちらのファイルも、FarmBeats リソース グループ内の `datahublogs-xxx` アカウントで使用できます。

データ ハブ ライブラリには、データ ハブのログ全体の一部としてログ記録できるようにするヘルパー関数が用意されています。 詳細については、[NOAA Docker イメージの GitHub ページ](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)を参照してください。

### <a name="troubleshooting-and-support"></a>トラブルシューティングとサポート

顧客が FarmBeats インスタンスで気象データを受信できない場合は、問題のトラブルシューティングを行うためのサポートとメカニズムを提供します。

## <a name="add-extendedtype"></a>ExtendedType を追加する

FarmBeats は、センサーのメジャーの種類や単位の新規追加をサポートしています。 [リファレンス実装](https://github.com/azurefarmbeats/noaa_docker)の *bootstrap_manifest.json* ファイルを更新することにより、新しい単位や種類を追加できます。

新しい WeatherMeasure の種類 (例: PrecipitationDepth) を追加するには、次の手順のようにします。

1. クエリ `filter - key = WeatherMeasureType` を使用して、/ExtendedType に対して `GET` 要求を行います。
2. 返されたオブジェクトの ID をメモします。
3. 返されたオブジェクトのリストに新しい種類を追加します。 次の新しいリストを使用して、/ExtendedType{ID} に対して `PUT` 要求を行います。 入力ペイロードは、前に受信した応答と同じである必要があります。 新しい単位は、値の一覧の末尾に追加する必要があります。

/ExtendedType API の詳細については、[FarmBeats の Swagger](https://aka.ms/FarmBeatsSwagger) を参照してください。

## <a name="next-steps"></a>次のステップ

以上で、FarmBeats と統合する Connector Docker コンポーネントが完成しました。 次に、FarmBeats の Docker イメージを使用して、[気象データを取得する](get-weather-data-from-weather-partner.md)方法を確認してください。 
