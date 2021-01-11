---
title: 気象パートナーの統合
description: この記事では、気象データ プロバイダーが FarmBeats と統合する方法について説明します
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: a2677b5343b2d65a39e7c9f6d5006db599c1ac73
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496997"
---
# <a name="weather-partner-integration"></a>気象パートナーの統合

この記事では、気象データ プロバイダーが API を利用して Azure FarmBeats と統合し、気象データを FarmBeats に送信するために開発できる FarmBeats **コネクタ** Docker コンポーネントに関する情報を提供します。 データは、FarmBeats で使用できるようになると、データ融合や機械学習および人工知能モデルの構築に使用できるようになります。

 > [!NOTE]
 > このドキュメントでは、Azure Open Datasets の NOAA を使用して構築されたリファレンス実装を使用します。これは、[https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) で入手できます。
 > 対応する Docker イメージは、[https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa) で入手できます

気象パートナーは、Docker イメージまたはプログラムを (以下で説明する仕様と合わせて) 提供し、顧客がアクセスできるコンテナー レジストリで Docker イメージをホストする必要があります。 気象パートナーは、次の情報を顧客に提供する必要があります。

- Docker イメージ URL
- Docker イメージ タグ
- Docker イメージにアクセスするためのキーと資格情報
- 気象パートナーのシステムからデータにアクセスするための顧客固有の API キーと資格情報
- VM SKU の詳細 (パートナーの Docker に特定の VM 要件がある場合は、パートナーがこれを提供できます。それ以外の場合は、顧客が Azure でサポートされる VM SKU から選択できます)

顧客は、上記の Docker 情報を使用して、FarmBeats インスタンスに気象パートナーを登録します。 顧客が Docker を使用して気象データを FarmBeats に取り込む方法の詳細については、[気象データの取得](https://docs.microsoft.com/azure/industry/agriculture/get-weather-data-from-weather-partner)に関するガイドを参照してください

## <a name="connector-docker-development"></a>コネクタ Docker の開発

**REST API ベースの統合**

FarmBeats API には、Swagger の技術文書が含まれています。 すべての API とそれに対応する要求または応答については、[FarmBeats Swagger](https://aka.ms/farmbeatsswagger) に関するページを参照してください。 

FarmBeats がインストールされている場合、`https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger` で FarmBeats swagger にアクセスできます

"-api" は、FarmBeats Web サイト名に追加されることに注意してください。
API エンドポイントは、`https://yourfarmbeatswebsitename-api.azurewebsites.net` です

### <a name="datahub-lib"></a>データ ハブ ライブラリ

FarmBeats では、気象パートナーが使用できるライブラリを提供します。 このライブラリは、現在、[こちら](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)からリファレンス実装の一部として入手できます。 将来、同じものが複数の言語の SDK として提供される予定です。

### <a name="authentication"></a>認証

**FarmBeats API による認証**

FarmBeats ではベアラー認証が使用され、次のように要求のヘッダー セクションでアクセス トークンを提供することによって API にアクセスできます。

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

アクセス トークンは、顧客の FarmBeats インスタンスで実行されている Azure 関数から要求できます。 Azure 関数の URL は、Docker プログラムに引数として提供され、アクセス トークンは、URL で GET 要求を行うことによって取得できます。 URL からの応答には、アクセス トークンが含まれます。 データ ハブ ライブラリには、パートナーがアクセス トークンを取得できるヘルパー関数が用意されています。 詳細については、[こちら](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)をご覧ください。

アクセス トークンは、数時間のみ有効であるため、有効期限が切れると、再要求する必要があります。

**パートナー側の API による認証**

顧客は、Docker の実行中にパートナー側の API を使用して認証できるようにするには、次のとおり、パートナーの登録時に資格情報を提供する必要があります。

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API サービスは、この辞書をシリアル化し、[KeyVault](https://docs.microsoft.com/azure/key-vault/basic-concepts) に格納します。

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) は、気象ジョブの調整に使用され、Docker コードを実行するためにリソースをスピンアップします。 また、Docker ジョブが実行される VM にデータを安全にプッシュするメカニズムも提供します。 現在 KeyVault に安全に格納されている API 資格情報は、KeyVault から安全な文字列として読み取られ、Docker コンテナーの作業ディレクトリで拡張プロパティ activity.json (ファイルへのパスは、"/mnt/working_dir/activity.json") として使用できるようになります。Docker コードでは、顧客に代わってパートナー側の API にアクセスするために、実行時にこのファイルから資格情報を読み取ることができます。 資格情報は、ファイル内で次のように使用できます。

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
"partnerCredentials" は、パートナー登録時に顧客によって提供されたとおりの方法で使用可能になることに注意してください

FarmBeats ライブラリには、パートナーがアクティビティ プロパティから資格情報を読み取ることができるようにするヘルパー関数が用意されています。 詳細については、[こちら](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)をご覧ください。

ファイルの有効期間は、Docker コードの実行中のみで、Docker の実行終了後に削除されます。

ADF パイプラインとアクティビティのしくみの詳細については、[https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping) を参照してください。

**HTTP 要求ヘッダー**

FarmBeats への API 呼び出しを行うときに指定する必要がある、最も一般的な要求ヘッダーを次に示します。

**ヘッダー** | **説明と例**
--- | ---
Content-Type | 要求の形式 (Content-Type: application/<format>)。 FarmBeats Datahub API の場合、形式は JSON です。 Content-Type, application/json
承認 | API 呼び出しを行うために必要なアクセス トークンを指定します。 Authorization:Bearer <Access-Token>
Accept | 応答形式。 FarmBeats Datahub API の場合、形式は JSON です。 Accept: application/json

## <a name="data-format"></a>データ形式

JSON は、任意のデータ構造をシンプルなテキストで表現する、言語に依存しない一般的なデータ形式です。 詳細については、[json.org](http://json.org) を参照してください。

## <a name="docker-specifications"></a>Docker の仕様

Docker プログラムには、**ブートストラップ**と**ジョブ**の 2 つのコンポーネントが必要です。 複数のジョブを含めることができます。

### <a name="bootstrap"></a>ブートストラップ

このコンポーネントは、顧客が FarmBeats で Docker 登録を開始したときに実行される必要があります。 このプログラムに渡される引数 (arg1, arg2) は、次のとおりです。

- FarmBeats API エンドポイント: API 要求の FarmBeats API エンドポイント: これは、FarmBeats デプロイに対して API 呼び出しを行うためのエンドポイントです。
- Azure Function URL: これは、FarmBeats API のアクセス トークンが提供される、データ プロバイダー独自の個人用エンドポイントです。 この URL で GET を呼び出すだけで、応答でアクセス トークンがフェッチされます。

ブートストラップの責任は、ユーザーがジョブを実行して気象データを取得できるように、必要なメタデータを作成することです。 リファレンス実装については、[こちら](https://github.com/azurefarmbeats/noaa_docker)を参照してください。 bootstrap_manifest.json は、必要に応じて更新することができ、リファレンス ブートストラップ プログラムにより、必要なメタデータが自動的に作成されます。

このプロセスの一環として、次のメタデータが作成されます。 

 > [!NOTE]
 > **ご注意ください**。[リファレンス実装](https://github.com/azurefarmbeats/noaa_docker)で説明されているとおりに bootstrap_manifest.json ファイルを更新する場合、ブートストラップにより、マニフェスト ファイルに従って次のメタデータが作成されるため、同じものを作成する必要はありません。

- /**WeatherDataModel**: WeatherDataModel は、気象データを表すためのモデルで、ソースから提供されるさまざまなデータに対応します。 たとえば、DailyForecastSimpleModel は、1 日に 1 回、平均気温、湿度、降水量の情報を提供するのに対して、DailyForecastAdvancedModel は、1 時間単位でより多くの情報を提供します。 任意の数の WeatherDataModels を作成できます。
- /**JobType**: FarmBeats には、拡張可能なジョブ管理システムが用意されています。 気象データ プロバイダーには、さまざまなデータセットおよび API (たとえば、GetDailyForecasts) があります。FarmBeats では、それらを JobType として有効にすることができます。 JobType が作成されると、顧客は、その種類のジョブをトリガーして、自分の場所または関心のあるファームの気象データを取得できます ([FarmBeats Swagger](https://aka.ms/farmbeatsswagger) 内の JobType および Job API を参照)。

### <a name="jobs"></a>ジョブ

このコンポーネントは、FarmBeats ユーザーが、ブートストラップ プロセスの一環として作成された /JobType のジョブを実行するたびに呼び出されます。 ジョブの Docker 実行コマンドは、作成した **/JobType** の一部として定義されます。
- ジョブの責任は、ソースからデータをフェッチし、それを FarmBeats にプッシュすることです。 データの取得に必要なパラメーターは、ブートストラップ プロセスの /JobType の一部として定義される必要があります。
- プログラムでは、ジョブの一部として、ブートストラップ プロセスの一環として作成された /WeatherDataModel に基づいて **/WeatherDataLocation** を作成する必要があります。 **/WeatherDataLocation** は、ユーザーがジョブのパラメーターとして提供する場所に対応します。

### <a name="details-of-the-objects"></a>オブジェクトの詳細

  WeatherDataModel | [説明] |
  --- | ---
  Name  | 気象データ モデルの名前 |
  Description  | そのモデルについてのわかりやすい説明を入力します。 |
  Properties  | データ プロバイダーによって定義される追加プロパティ。 |
  weatherMeasures > Name  | 気象メジャーの名前。 例: humidity_max |
  weatherMeasures > DataType  | Double または Enum。 Enum の場合、measureEnumDefinition は必須 |
  weatherMeasures > measureEnumDefinition  | DataType が Enum の場合のみ必須。 例: { "NoRain":0, "Snow":1, "Drizzle":2, "Rain":3 } |
  weatherMeasures > Type  | 気象テレメトリ データの種類。 例: "RelativeHumidity"。 システム定義の種類は次のとおりです。AmbientTemperature、NoUnit、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、Pressure、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR. さらに追加するには、/ExtendedType API または以下の「[種類と単位の追加に関するセクション](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype)」を参照してください
  weatherMeasures > Unit | 気象テレメトリ データの単位。 システム定義の単位は次のとおりです。NoUnit、Celsius、Fahrenheit、Kelvin、Rankine、Pascal、Mercury、PSI、MilliMeter、CentiMeter、Meter、Inch、Feet、Mile、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、Degree、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、Percentage、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、Liter、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond、InchesPerHour。 さらに追加するには、/ExtendedType API または以下の「[種類と単位の追加に関するセクション](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype)」を参照してください。
  weatherMeasures > AggregationType  | None、Average、Maximum、Minimum、StandardDeviation、Sum、Total のいずれか
  weatherMeasures > Depth  | センサーの深さ (センチメートル単位)。 たとえば、地面より 10 cm 下の湿度。
  weatherMeasures > Description  | そのメジャーについてのわかりやすい説明を入力します。 |
  **JobType** | **説明** |
  Name  | ジョブの名前 (例: Get_Daily_Forecast)。顧客が気象データを取得するために実行するジョブ|
  pipelineDetails > parameters > name  | パラメーターの名前 |
  pipelineDetails > parameters > type | String、Int、Float、Bool、Array |
  pipelineDetails > parameters > isRequired | boolean、必須パラメーターの場合は true、それ以外の場合は false、既定は true |
  pipelineDetails > parameters > defaultValue | パラメーターの既定値 |
  pipelineDetails > parameters > description | パラメーターの説明 |
  Properties  | 製造元から提供されるその他のプロパティ。
  Properties > **programRunCommand** | Docker 実行コマンド - このコマンドは、顧客が気象ジョブを実行するときに実行されます。 |
  **WeatherDataLocation** | **説明** |
  weatherDataModelId  | ブートストラップで作成された対応する WeatherDataModel の ID|
  location  | 緯度、経度、高度を表します |
  Name | オブジェクトの名前 |
  Description | 説明 |
  farmId | ファームの**オプションの** ID - ジョブ パラメーターの一部として顧客によって提供されます |
  Properties  | 製造元から提供されるその他のプロパティ。

 各オブジェクトとそのプロパティの詳細については、[Swagger](https://aka.ms/FarmBeatsSwagger) に関するページを参照してください。

 > [!NOTE]
 > API からは、作成されたインスタンスごとに一意の ID が返されます。 デバイスの管理とメタデータの同期のため、Translator はこの ID を保持する必要があります。

**メタデータの同期**

コネクタ Docker には、メタデータの更新を送信する機能が必要です。 更新シナリオの例としては、気象プロバイダーのデータセット内の新しい気象パラメーターの追加、機能の追加 (たとえば、 30 日間の予報の追加) などがあります

> [!NOTE]
> メタデータ (たとえば、気象データ モデル) の削除は サポートされていません。
>
> メタデータを更新するには、気象データ モデルで /Get/{ID} を呼び出し、変更されたプロパティを更新してから /Put/{ID} を実行して、ユーザーが設定したプロパティが失われないようにする必要があります。

## <a name="weather-data-telemetry-specifications"></a>気象データ (テレメトリ) の仕様

気象データは、処理のために Azure Event Hub にプッシュされる正規のメッセージにマップされます。 Azure Event Hub は、接続されているデバイスやアプリケーションからリアルタイムでデータ (テレメトリ) を取り込むことができるようにするサービスです。 気象データを FarmBeats に送信するには、FarmBeats のイベント ハブにメッセージを送信するクライアントを作成する必要があります。 テレメトリの送信の詳細については、[イベント ハブへのテレメトリの送信](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)に関するページを参照してください

指定されたイベント ハブにクライアントとしてテレメトリを送信する Python コードの例を次に示します。

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

正規のメッセージ形式は次のとおりです。

```json
{
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
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

**エラーのログ**

パートナー ジョブは既存のジョブ フレームワークで実行されるため、エラーは、FarmBeats 内の他の既存のジョブ (GetFarmData、SensorPlacement など) のエラーと同様にログに記録されます。 ADF パイプライン内で実行される ADF アクティビティでは、STDERR と STDOUT の両方にログ記録されます。 どちらのファイルも、FarmBeats リソース グループ内の "datahublogs-xxx" アカウントで使用できます。

データ ハブ ライブラリには、データ ハブのログ全体の一部としてログ記録できるようにするヘルパー関数が用意されています。 詳細については、[こちら](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)をご覧ください。

**トラブルシューティングのオプションまたはサポート**

顧客が指定された FarmBeats インスタンスで気象データを受信できない場合、気象パートナーは、サポートやその問題をトラブルシューティングするためのメカニズムを提供する必要があります。

## <a name="add-extendedtype"></a>ExtendedType を追加する

FarmBeats は、センサーのメジャーの種類や単位の新規追加をサポートしています。 気象パートナーは、[こちら](https://github.com/azurefarmbeats/noaa_docker)のリファレンス実装内の bootstrap_manifest.json ファイルを更新して新しい単位または種類を追加できることに注意してください

新しい WeatherMeasure の種類 (例: "PrecipitationDepth") を追加するには、次の手順に従います。

1. クエリ フィルター (key = WeatherMeasureType) を使用して /ExtendedType に対する GET 要求を実行します。
2. 返されたオブジェクトの ID をメモします。
3. 返されたオブジェクトの一覧に新しい種類を追加し、次の新しい一覧を使用して、/ExtendedType{ID} に対する PUT 要求を実行します。 入力ペイロードは、上記の受信した応答と同じである必要があり、新しい単位は、値の一覧の最後に追加されます。

/ExtendedType API の詳細については、[Swagger](https://aka.ms/FarmBeatsSwagger) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

以上で、FarmBeats と統合するコネクタ Docker が完成しました。 次は、この Docker を使用して FarmBeats に気象データを取得する方法を確認します。 [気象データの取得](get-weather-data-from-weather-partner.md)に関するページを参照してください。
