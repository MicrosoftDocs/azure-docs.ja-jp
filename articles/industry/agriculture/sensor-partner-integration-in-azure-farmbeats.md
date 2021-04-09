---
title: センサー パートナーの統合
description: この記事では、センサー パートナーの統合について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 8d1b8203fa50609daf59431c2cfecba68eba52b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179750"
---
# <a name="sensor-partner-integration"></a>センサー パートナーの統合

この記事では、Azure FarmBeats **Translator** コンポーネントについて説明します。これを使うと、センサー パートナーとの統合が可能になります。

このコンポーネントを使用すると、パートナーは FarmBeats Datahub API を使用して FarmBeats を統合し、お客様のデバイス データとテレメトリを FarmBeats Datahub に送信することができます。 データは、FarmBeats で使用できるようになると FarmBeats Accelerator を使用して視覚化され、データ融合や機械学習/人工知能モデルの構築に使用できるようになります。

## <a name="before-you-start"></a>開始する前に

Translator コンポーネントを開発するには、FarmBeats API へのアクセスを有効にする以下の資格情報が必要です。

- API エンドポイント
- テナント ID
- クライアント ID
- クライアント シークレット
- EventHub 接続文字列

上記の資格情報を取得する際には、次のセクションを参照してください:[デバイス統合を有効にする](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Translator の開発

**REST API ベースの統合**

FarmBeats のセンサー データ統合機能は、REST API 経由で公開されます。 機能には、メタデータの定義、デバイスとセンサーのプロビジョニング、デバイスとセンサーの管理などがあります。

**テレメトリ インジェスト**

テレメトリ データは、処理のために Azure Event Hubs に発行される正規のメッセージにマップされます。 Azure Event Hubs は、接続されているデバイスやアプリケーションからリアルタイムでデータ (テレメトリ) の取り込みを可能にするサービスです。

**API の開発**

API には、Swagger の技術ドキュメントが用意されています。 API とそれに対応する要求または応答の詳細については、[Swagger](https://aka.ms/FarmBeatsSwagger) に関するページを参照してください。

**認証**

FarmBeats では、Microsoft Azure Active Directory 認証が使用されます。 Azure App Service によって、組み込みの認証と承認がサポートされます。

詳細については、「 [Azure Active Directory](../../app-service/overview-authentication-authorization.md)」を参照してください。

FarmBeats Datahub ではベアラー認証が使用され、次の資格情報が必要です。
   - クライアント ID
   - クライアント シークレット
   - テナント ID

これらの資格情報を使用して、呼び出し元はアクセス トークンを要求できます。 そのトークンを、次に示すように、後続の API 要求のヘッダー セクションで送信する必要があります。

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

FarmBeats に対する後続の API 呼び出しに使用できるアクセス トークンを提供する Python コードの例を次に示します。

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```


**HTTP 要求ヘッダー**

FarmBeats Datahub への API 呼び出しを行うときに指定する必要がある、最も一般的な要求ヘッダーを次に示します。


**ヘッダー** | **説明と例**
--- | ---
Content-Type | 要求の形式 (Content-Type: application/<format>)。 FarmBeats Datahub API の場合、形式は JSON です。 Content-Type, application/json
承認 | API 呼び出しを行うために必要なアクセス トークンを指定します。 Authorization:Bearer <Access-Token>
Accept | 応答形式。 FarmBeats Datahub API の場合、形式は JSON です。 Accept: application/json

**API 要求**

REST API 要求を行うには、HTTP (GET、POST、または PUT) メソッド、API サービスへの URL、リソース (クエリ、データ送信、更新、削除の対象となるリソース) の Uniform Resource Identifier (URI)、1 つ以上の HTTP 要求ヘッダーを組み合わせます。 API サービスの URL は、お客様が提供する API エンドポイントです。 例として、 https://\<yourdatahub-website-name>.azurewebsites.net があります。

必要に応じて、GET 呼び出しにクエリ パラメーターを追加して、応答のデータのフィルター処理、サイズ制限、並べ替えを行うことができます。

次に示すのは、デバイスの一覧を取得する要求の例です。

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
ほとんどの GET、POST、PUT 呼び出しには、JSON 要求本文が必要です。

次に示すのは、デバイスを作成する要求の例です。 (このサンプルには、要求本文での入力 JSON が含まれています)。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>データ形式

JSON は、任意のデータ構造をシンプルなテキストで表現する、言語に依存しない一般的なデータ形式です。 詳細については、[json.org](http://json.org) を参照してください。

## <a name="metadata-specifications"></a>メタデータの仕様

FarmBeats Datahub の次の API を使用することで、デバイス パートナーは、デバイスまたはセンサーのメタデータを作成して管理できます。

- /**DeviceModel**:DeviceModel は、デバイスのメタデータ (製造元など) およびデバイスの種類 (ゲートウェイまたはノード) に対応します。
- /**Device**:Device は、ファームに存在する物理デバイスに対応します。
- /**SensorModel**:SensorModel は、センサーのメタデータ (製造元など)、センサーの種類 (アナログまたはデジタル)、センサーのメジャー (周辺温度や圧力) に対応します。
- /**Sensor**:センサーは、値を記録する物理的なセンサーに対応するものです。 センサーは通常、デバイス ID を持つデバイスに接続されます。

  DeviceModel | 説明 |
  --- | ---
  Type (node、gateway)  | デバイスの種類 - Node または Gateway |
  Manufacturer  | 製造元の名前 |
  ProductCode  | デバイスの製品コード、モデル名、またはモデル番号。 例: EnviroMonitor#6800。 |
  Port  | ポートの名前と種類 (デジタルまたはアナログ)。  |
  名前  | リソースを識別する名前 たとえば、モデル名または製品名。 |
  説明  | そのモデルについてのわかりやすい説明を入力します。 |
  Properties  | 製造元から提供されるその他のプロパティ。 |
  **[デバイス]** | **説明** |
  DeviceModelId  |関連付けられているデバイス モデルの ID |
  HardwareId   |デバイスの一意の ID (MAC アドレスなど)。  |
  ReportingInterval |レポートの間隔 (秒)。 |
  場所    |デバイスの緯度 (-90 から +90)、経度 (-180 から 180)、海抜 (メートル単位)。 |
  ParentDeviceId | このデバイスが接続されている親デバイスの ID。 たとえば、ノードがゲートウェイに接続されている場合、ノードのゲートウェイは parentDeviceID です。 |
  名前  | リソースを識別するための名前。 デバイス パートナーは、デバイス パートナー側のデバイス名と一致する名前を送信する必要があります。 デバイス パートナー側のデバイス名がユーザー定義である場合、同じユーザー定義名を FarmBeats に反映させる必要があります。  |
  説明  | わかりやすい説明を入力します。  |
  Properties  |製造元から提供されるその他のプロパティ。  |
  **SensorModel** | **説明** |
  Type (analog、digital)  |アナログ センサーまたはデジタル センサーを表します。|
  Manufacturer  | 製造元の名前。 |
  ProductCode  | 製品コード、モデル名、またはモデル番号。 例: RS-CO2-N01。  |
  SensorMeasures > Name  | センサーのメジャーの名前。 小文字のみがサポートされます。 メジャーの深さが異なる場合は、深さを指定します。 例: soil_moisture_15cm。 この名前は、テレメトリ データと一致している必要があります。 |
  SensorMeasures > DataType  | テレメトリのデータ型。 現在、倍精度浮動小数点型がサポートされています。 |
  SensorMeasures > Type  | センサーのテレメトリ データの測定の種類。 システム定義の種類は次のとおりです。AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、Pressure、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 さらに追加するには、/ExtendedType API を参照してください。
  SensorMeasures > Unit | センサーのテレメトリ データの単位。 システム定義の単位は次のとおりです。NoUnit、Celsius、Fahrenheit、Kelvin、Rankine、Pascal、Mercury、PSI、MilliMeter、CentiMeter、Meter、Inch、Feet、Mile、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、Degree、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、Percentage、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、Liter、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond、InchesPerHour。 さらに追加するには、/ExtendedType API を参照してください。
  SensorMeasures > AggregationType  | none、average、maximum、minimum、または StandardDeviation のいずれか。
  SensorMeasures > Depth  | センサーの深さ (センチメートル単位)。 たとえば、地面より 10 cm 下の湿度。
  SensorMeasures > Description  | そのメジャーについてのわかりやすい説明を入力します。
  名前  | リソースを識別する名前 たとえば、モデル名または製品名。
  説明  | そのモデルについてのわかりやすい説明を入力します。
  Properties  | 製造元から提供されるその他のプロパティ。
  **センサー**  | **説明** |
  HardwareId  | 製造元によって設定された、センサーの一意の ID。
  SensorModelId  | 関連付けられているセンサー モデルの ID。
  場所  | センサーの緯度 (-90 から +90)、経度 (-180 から 180)、海抜 (メートル単位)。
  Port > Name  |センサーが接続されている、デバイス上のポートの名前と種類。 これは、デバイス モデルで定義されているのと同じ名前にする必要があります。
  deviceId  | センサーが接続されているデバイスの ID。
  名前  | リソースを識別するための名前。 たとえば、センサー名または製品名と、モデル番号または製品コード。
  説明  | わかりやすい説明を入力します。
  Properties  | 製造元から提供されるその他のプロパティ。

 各オブジェクトとそのプロパティの詳細については、[Swagger](https://aka.ms/FarmBeatsDatahubSwagger) に関するページを参照してください。

 > [!NOTE]
 > API からは、作成されたインスタンスごとに一意の ID が返されます。 デバイスの管理とメタデータの同期のため、Translator はこの ID を保持する必要があります。


**メタデータの同期**

Translator は、メタデータに更新を送信する必要があります。 更新シナリオの例としては、デバイスまたはセンサーの名前の変更や、デバイスまたはセンサーの場所の変更などがあります。

Translator には、FarmBeats のユーザー投稿リンクからインストールされた新しいデバイスやセンサーを追加する機能が必要です。 同様に、デバイスまたはセンサーがユーザーによって更新されている場合は、FarmBeats でも対応するデバイスまたはセンサーを更新する必要があります。 デバイスまたはセンサーの更新が必要になる一般的なシナリオは、デバイスの場所を変更したり、ノードにセンサーを追加したりする場合です。


> [!NOTE]
> デバイスまたはセンサーのメタデータの削除はサポートされていません。
>
> メタデータを更新するには、デバイスまたはセンサーで /Get/{id} を呼び出し、変更されたプロパティを更新してから /Put/{id} を実行して、ユーザーが設定したプロパティが失われないようにする必要があります。

### <a name="add-new-types-and-units"></a>種類と単位を追加する

FarmBeats は、センサーのメジャーの種類や単位の新規追加をサポートしています。 /ExtendedType API の詳細については、[Swagger](https://aka.ms/FarmBeatsSwagger) に関するページを参照してください。

## <a name="telemetry-specifications"></a>テレメトリの仕様

テレメトリ データは、処理のために Azure Event Hubs に発行される正規のメッセージにマップされます。 Azure Event Hubs は、接続されているデバイスやアプリケーションからリアルタイムでデータ (テレメトリ) の取り込みを可能にするサービスです。

## <a name="send-telemetry-data-to-farmbeats"></a>テレメトリ データを FarmBeats に送信する

テレメトリ データを FarmBeats に送信するには、FarmBeats のイベント ハブにメッセージを送信するクライアントを作成します。 テレメトリ データの詳細については、[イベント ハブへのテレメトリの送信](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)に関する記事を参照してください。

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
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
テレメトリ JSON では、すべてのキー名を小文字にする必要があります。 たとえば、deviceid や sensordata などです。

テレメトリ メッセージの例を次に示します。


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

> [!NOTE]
> 以降のセクションは、その他の変更に関連しています (例: UI、エラー管理など)。センサー パートナーは、Translator コンポーネントの開発中にそれらを参照できます。


## <a name="link-a-farmbeats-account"></a>FarmBeats アカウントにリンクする

お客様は、デバイスやセンサーを購入して展開した後は、デバイス パートナーのサービスとしてのソフトウェア (SaaS) ポータルでデバイスのデータとテレメトリにアクセスできます。 デバイス パートナーは、以下の資格情報を入力する方法を提供することで、お客様が自分のアカウントを Azure 上の FarmBeats インスタンスにリンクできるようにします。

   - 表示名 (ユーザーがこの統合の名前を定義するためのオプション フィールド)
   - API エンドポイント
   - テナント ID
   - クライアント ID
   - クライアント シークレット
   - EventHub 接続文字列
   - 開始日

   > [!NOTE]
   > 開始日から、履歴データのフィード (つまり、ユーザーが指定した日付以降のデータ) が有効になります。

## <a name="unlink-farmbeats"></a>FarmBeats のリンク解除

デバイス パートナーは、お客様が既存の FarmBeats 統合のリンクを解除可能にすることができます。 FarmBeats のリンクを解除しても、FarmBeats Datahub で作成されたデバイスやセンサーのメタデータは削除されません。 リンクを解除すると次のことが行われます。

   - テレメトリ フローが停止されます。
   - デバイス パートナーにおいて統合資格情報が削除または消去されます。

## <a name="edit-farmbeats-integration"></a>FarmBeats 統合の編集

デバイス パートナーは、クライアント シークレットまたは接続文字列が変更された場合にお客様が FarmBeats 統合設定を編集可能にすることができます。 この場合に編集できるのは以下のフィールドだけです。

   - 表示名 (該当する場合)
   - クライアント シークレット (クリア テキストではなく、"2x8***********" 形式または表示および非表示機能を使って表示されます)
   - 接続文字列 (クリア テキストではなく、"2x8***********" 形式または表示および非表示機能を使って表示されます)

## <a name="view-the-last-telemetry-sent"></a>最後に送信されたテレメトリを表示する

デバイス パートナーは、送信された最新のテレメトリのタイムスタンプをお客様が **[Telemetry Sent] (送信されたテレメトリ)** で表示可能にすることができます。 これは、最新のテレメトリが FarmBeats に正常に送信された時間です。

## <a name="troubleshooting-and-error-management"></a>トラブルシューティングとエラーの管理

**トラブルシューティングのオプションまたはサポート**

お客様が、指定した FarmBeats インスタンスでデバイス データやテレメトリを受信できない場合は、デバイス パートナーが、サポートとトラブルシューティングのためのメカニズムを提供する必要があります。

**テレメトリ データの保有期間**

エラーまたはデータ損失が発生した場合にテレメトリをデバッグまたは再送信することができるように、事前に定義された期間はテレメトリ データを保持する必要があります。

**エラー管理またはエラー通知**

エラーにより、デバイスやセンサーのメタデータ、またはデバイス パートナー システムでのデータ統合またはテレメトリ データ フローに影響がある場合、お客様が通知を受け取る必要があります。 エラーを解決するためのメカニズムも、設計および実装する必要があります。

**接続チェックリスト**

デバイスの製造元やパートナーは、次のようなチェックリストを使用して、お客様から提供された資格情報が正確であることを確実にすることができます。

   - 指定された資格情報でアクセス トークンを受け取ったかどうかを確認します。
   - 受け取ったアクセス トークンで API 呼び出しが成功するかどうかを確認します。
   - EventHub クライアント接続が確立されているかどうかを確認します。

## <a name="next-steps"></a>次のステップ

REST API の詳細については、「[REST API](rest-api-in-azure-farmbeats.md)」を参照してください。