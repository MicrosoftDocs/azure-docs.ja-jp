---
title: 過去のテレメトリ データの取り込み
description: この記事では、テレメトリの履歴データを取り込む方法について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.custom: has-adal-ref
ms.openlocfilehash: e4182f3718422be3a0aa99c980d33026df4ce9af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173751"
---
# <a name="ingest-historical-telemetry-data"></a>過去のテレメトリ データの取り込み

この記事では、センサーの履歴データを Azure FarmBeats に取り込む方法について説明します。

デバイスやセンサーなどのモノのインターネット (IoT) リソースから履歴データを取り込むことは、FarmBeats では一般的なシナリオです。 デバイスとセンサーのメタデータを作成してから、その履歴データを正規形式で FarmBeats に取り込みます。

## <a name="before-you-begin"></a>開始する前に

この記事を読み進める前に、確実に FarmBeats がインストールされていて、IoT デバイスから履歴データが収集されているようにします。 次の手順で説明するように、パートナー アクセスを有効にする必要もあります。

## <a name="enable-partner-access"></a>パートナー アクセスを有効にする

Azure FarmBeats インスタンスへのパートナー統合を有効にする必要があります。 この手順では、デバイス パートナーとして Azure FarmBeats インスタンスにアクセスできるクライアントを作成し、以降の手順で必要となる以下の値を指定します。

- API エンドポイント:データ ハブの URL (例: https://\<datahub>.azurewebsites.net)
- テナント ID
- クライアント ID
- クライアント シークレット
- EventHub 接続文字列

次の手順に従います。

> [!NOTE]
> 次の手順を実行するには、管理者である必要があります。

1. https://portal.azure.com/ にサインインします。

2. **FarmBeats バージョン 1.2.7 以降を使用している場合は、手順 a、b、c をスキップし、手順 3 に進みます。** 。 FarmBeats のバージョンを確認するには、FarmBeats UI の右上隅にある **設定** アイコンを選択します。

      a.  **[Azure Active Directory]**  >  **[アプリの登録]** の順に進みます。

      b. FarmBeats デプロイの一部として作成された **[アプリの登録]** を選択します。 それは、FarmBeats データ ハブと同じ名前になります。

      c. **[API の公開]** を選択し、 **[クライアント アプリケーションの追加]** を選択し、「**04b07795-8ddb-461a-bbee-02f9e1bf7b46**」と入力し、 **[Authorize Scope]\(スコープの承認\)** をオンにします。 これにより、Azure CLI (Cloud Shell) にアクセスして、次の手順を実行することができます。

3. Cloud Shell を開きます。 このオプションは、Azure portal の右上隅にあるツール バーで使用できます。

    ![Azure portal のツール バー](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. 環境が **[PowerShell]** に設定されていることを確認します。 既定では、[Bash] に設定されています。

    ![PowerShell ツール バーの設定](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. ホーム ディレクトリに移動します。

    ```azurepowershell-interactive
    cd
    ```

6. 次のコマンドを実行します。 これにより、Azure AD 要求に使用する認証済みアカウントが接続されます

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. 次のコマンドを実行します。 これにより、スクリプトがホーム ディレクトリにダウンロードされます。

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. 次のスクリプトを実行します。 このスクリプトは、 **[Azure Active Directory]**  >  **[概要]** ページから取得できるテナント ID を要求します。

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

9. 画面の指示に従って、**API エンドポイント**、**テナント ID**、**クライアント ID**、**クライアント シークレット**、および **EventHub 接続文字列** の値をキャプチャします。


## <a name="create-device-or-sensor-metadata"></a>デバイスまたはセンサーのメタデータを作成する

 必要な資格情報が得られたので、デバイスとセンサーを定義できます。 これを行うには、FarmBeats API を呼び出してメタデータを作成します。 前のセクションで作成したクライアント アプリとして API を呼び出す必要があります。

 FarmBeats Datahub には、デバイスまたはセンサーのメタデータの作成と管理を可能にする次の API が用意されています。

 > [!NOTE]
 > パートナーは、メタデータの読み取り、作成、更新のみを行うことができます。**削除オプションは、パートナーに制限されます。**

- /**DeviceModel**:DeviceModel は、デバイスのメタデータ (製造元など) およびデバイスの種類 (ゲートウェイまたはノードのいずれか) に対応します。
- /**Device**:Device は、ファームに存在する物理デバイスに対応します。
- /**SensorModel**:SensorModel は、センサーのメタデータ (製造元など)、センサーの種類 (アナログまたはデジタル)、センサーのメジャー (周辺温度や圧力) に対応します。
- /**Sensor**:センサーは、値を記録する物理的なセンサーに対応するものです。 センサーは通常、デバイス ID を持つデバイスに接続されます。

| DeviceModel | 検索候補 |
|--|--|
| Type (node、gateway) | デバイスの種類 - Node または Gateway |
| Manufacturer | 製造元の名前 |
| ProductCode | デバイスの製品コード、モデル名、またはモデル番号。 例: EnviroMonitor#6800。 |
| Port | ポートの名前と種類 (デジタルまたはアナログ)。 |
| 名前 | リソースを識別するための名前。 たとえば、モデル名または製品名。 |
| 説明 | そのモデルについてのわかりやすい説明を入力します。 |
| Properties | 製造元から提供されるその他のプロパティ。 |
| **[デバイス]** |  |
| DeviceModelId | 関連付けられているデバイス モデルの ID |
| HardwareId | MAC アドレスなど、デバイスの一意の ID。 |
| ReportingInterval | レポートの間隔 (秒)。 |
| 場所 | デバイスの緯度 (-90 から +90)、経度 (-180 から 180)、海抜 (メートル単位)。 |
| ParentDeviceId | このデバイスが接続されている親デバイスの ID。 たとえば、ゲートウェイに接続されているノードがあります。 ノードは parentDeviceId というゲートウェイを備えています。 |
| 名前 | リソースを識別するための名前。 デバイス パートナーは、パートナー側のデバイス名と一致する名前を送信する必要があります。 パートナー デバイス名がユーザー定義である場合、同じユーザー定義名を FarmBeats に反映させる必要があります。 |
| 説明 | わかりやすい説明を入力します。 |
| Properties | 製造元から提供されるその他のプロパティ。 |
| **SensorModel** |  |
| Type (analog、digital) | センサーの種類 (アナログまたはデジタル)。 |
| Manufacturer | センサーの製造元。 |
| ProductCode | 製品コード、モデル名、またはモデル番号。 例: RS-CO2-N01。 |
| SensorMeasures > Name | センサーのメジャーの名前。 小文字のみがサポートされます。 メジャーの深さが異なる場合は、深さを指定します。 例: soil_moisture_15cm。 この名前は、テレメトリ データと一致している必要があります。 |
| SensorMeasures > DataType | テレメトリのデータ型。 現在、倍精度浮動小数点型がサポートされています。 |
| SensorMeasures > Type | センサーのテレメトリ データの測定の種類。 システムで定義された種類は、AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、Pressure、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR です。 さらに追加するには、/ExtendedType API を参照してください。 |
| SensorMeasures > Unit | センサーのテレメトリ データの単位。 システムで定義された単位は、NoUnit、Celsius、Fahrenheit、Kelvin、Rankine、Pascal、Mercury、PSI、MilliMeter、CentiMeter、Meter、Inch、Feet、Mile、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、Degree、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、Percentage、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、Liter、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond、InchesPerHour です。さらに追加する場合は、/ExtendedType API を参照してください。 |
| SensorMeasures > AggregationType | 値は、none、average、maximum、minimum、または StandardDeviation を指定できます。 |
| 名前 | リソースを識別する名前。 たとえば、モデル名または製品名。 |
| 説明 | そのモデルについてのわかりやすい説明を入力します。 |
| Properties | 製造元から提供されるその他のプロパティ。 |
| **センサー** |  |
| HardwareId | 製造元によって設定された、センサーの一意の ID。 |
| SensorModelId | 関連付けられているセンサー モデルの ID。 |
| 場所 | センサーの緯度 (-90 から +90)、経度 (-180 から 180)、海抜 (メートル単位)。 |
| Port > Name | センサーが接続されている、デバイス上のポートの名前と種類。 これは、デバイス モデルで定義されているのと同じ名前にする必要があります。 |
| DeviceID | センサーが接続されているデバイスの ID。 |
| 名前 | リソースを識別する名前 たとえば、センサー名または製品名と、モデル番号または製品コード。 |
| 説明 | わかりやすい説明を入力します。 |
| Properties | 製造元から提供されるその他のプロパティ。 |

オブジェクトの詳細については、[Swagger](https://aka.ms/FarmBeatsDatahubSwagger) を参照してください。

### <a name="api-request-to-create-metadata"></a>メタデータを作成するための API 要求

API 要求を行うには、HTTP (POST) メソッド、API サービスの URL、クエリ、データの送信、要求の作成または削除の対象となるリソースの URI を結合します。 その後、1 つまたは複数の HTTP 要求ヘッダーを追加します。 API サービスの URL は API エンドポイント、つまり、データ ハブの URL (https://\<yourdatahub>.azurewebsites.net) です。

### <a name="authentication"></a>認証

FarmBeats データ ハブではベアラー認証を使用します。その際、前のセクションで生成した次の資格情報が必要になります。

- クライアント ID
- クライアント シークレット
- テナント ID

これらの資格情報を使用して、呼び出し元はアクセス トークンを要求できます。 そのトークンを、次に示すように、後続の API 要求のヘッダー セクションで送信する必要があります。

```
headers = *{"Authorization": "Bearer " + access_token, …}*
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

- **Content-Type**: application/json
- **承認**: Bearer <Access-Token>
- **Accept**: application/json

### <a name="input-payload-to-create-metadata"></a>メタデータを作成するための入力ペイロード

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Device

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
センサー

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

次の要求の例を使うと、デバイスを作成できます。 この要求では、要求本体にペイロードとして JSON を入力しています。

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",
\"reportingInterval\": 900,  \"name\": \"Device123\",
\"description\": \"Test Device 123\"}" *
```

Python のサンプル コードを次に示します。 このサンプルで使用されるアクセス トークンは、認証時に受信するものと同じです。

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> API からは、作成されたインスタンスごとに一意の ID が返されます。 対応するテレメトリ メッセージを送信するには、ID を保持する必要があります。

### <a name="send-telemetry"></a>テレメトリを送信する

FarmBeats でデバイスとセンサーを作成したので、関連付けられたテレメトリ メッセージを送信できるようになりました。

### <a name="create-a-telemetry-client"></a>テレメトリ クライアントを作成する

テレメトリを Azure Event Hubs に送信して、処理する必要があります。 Azure Event Hubs は、接続されているデバイスやアプリケーションからリアルタイムでデータ (テレメトリ) の取り込みを可能にするサービスです。 テレメトリ データを FarmBeats に送信するには、FarmBeats のイベント ハブにメッセージを送信するクライアントを作成します。 テレメトリの送信の詳細については、[Azure Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) に関するページを参照してください。

### <a name="send-a-telemetry-message-as-the-client"></a>クライアントとしてテレメトリ メッセージを送信する

Event Hubs クライアントとして接続を確立したら、メッセージを JSON としてイベント ハブに送信できます。

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

センサーの履歴データの形式を、Azure FarmBeats が理解できる正規形式に変換します。 正規のメッセージ形式は次のとおりです。

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

対応するデバイスとセンサーを追加した後、前のセクションで説明したように、テレメトリ メッセージでデバイス ID とセンサー ID を取得します。

テレメトリ メッセージの例を次に示します。


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
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

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>センサーから履歴またはストリーミング データを取り込んだ後で、テレメトリ データを表示できない

**現象**:デバイスまたはセンサーがデプロイされており、FarmBeats 上でデバイスまたはセンサーを作成し、EventHub にテレメトリを取り込みましたが、FarmBeats 上でテレメトリ データを取得または表示することができません。

**是正措置**:

1. 適切なパートナー登録を確実に行ってください。これを確かめるには、ご利用の Datahub Swagger にアクセスし、/Partner API に移動し、Get を実行して、パートナーが登録されているかどうかを確認します。 そうなっていない場合は、[こちらのステップ](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)に従って、パートナーを追加してください。

2. 確実に、パートナー クライアントの資格情報を使用して、メタデータ (DeviceModel、Device、SensorModel、Sensor) を作成します。

3. 次に示すような正しいテレメトリ メッセージ形式を確実に使用します。

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

## <a name="next-steps"></a>次のステップ

REST API ベースの統合の詳細については、「[REST API](rest-api-in-azure-farmbeats.md)」を参照してください。