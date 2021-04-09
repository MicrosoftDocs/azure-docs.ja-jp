---
title: 画像パートナーの統合
description: この記事では、画像パートナーとの統合について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.custom: has-adal-ref
ms.openlocfilehash: 1bc723892e29ccc24a7612cfbe50d4b274cdacdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183796"
---
# <a name="imagery-partner-integration"></a>画像パートナーの統合

この記事では、Azure FarmBeats Translator コンポーネントを使用して画像データを FarmBeats に送信する方法について説明します。 農業に関する画像データは、マルチスペクトル カメラ、衛星、ドローンなどのさまざまなソースから生成できます。 農業に関する画像パートナーは、FarmBeats と統合することで、独自に作成されたファームのマップを顧客に提供できます。

データは、使用可能になると、FarmBeats Accelerator を通じて視覚化できます。また、農業関連企業や顧客のシステム インテグレーターによるデータ融合や機械学習および人工知能 (ML および AI) モデルの構築に使用される可能性もあります。

FarmBeats では、次のことが可能です。

- /ExtendedType API を使用して、カスタム画像の種類、ソース、およびファイル形式を定義する。
- /Scene および /SceneFile API を利用してさまざまなソースから画像データを取り込む。

以下の情報は、任意の形式の画像を FarmBeats システムに取り込むことに焦点を当てています。

**[Drone Imagery]\(ドローンの画像\)** セクションを選択すると、ポップアップが開き、ドローンを使った高解像度のオルソモザイク画像が表示されます。 ドローンの飛行計画や生データの取得に役立つ、パートナーのソフトウェアにアクセスすることができます。 経路計画やオルソモザイク画像の合成にも引き続きパートナーのソフトウェアを使用します。

ドローン パートナーは、顧客が自身の顧客アカウントを Azure 上の FarmBeats インスタンスとリンクできるようにしておく必要があります。

FarmBeats をリンクするには、ドローン パートナーのソフトウェアで次の資格情報を使用する必要があります。

- API エンドポイント
- テナント ID
- クライアント ID
- クライアント シークレット

## <a name="api-development"></a>API の開発

API には、Swagger の技術ドキュメントが用意されています。 API とそれに対応する要求または応答の詳細については、「[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)」を参照してください。

## <a name="authentication"></a>認証

FarmBeats には Microsoft Azure [Active Directory](../../app-service/overview-authentication-authorization.md) (Azure AD) が使用されます。 Azure App Service によって、組み込みの認証と承認がサポートされます。 

Azure AD の詳細については、「[Azure Active Directory](../../app-service/overview-authentication-authorization.md)」を参照してください。   

FarmBeats Datahub ではベアラー認証が使用され、次の資格情報が必要です。

- クライアント ID
- クライアント シークレット
- テナント ID

呼び出し元は、上記の資格情報を使用してアクセス トークンを要求できます。トークンは、次のように、後続の API 要求のヘッダー セクションで送信する必要があります。

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

次の Python コード サンプルを使うと、アクセス トークンを取得できます。 その後、後続の FarmBeats に対する API 呼び出しには、このトークンを使用できます。

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

## <a name="http-request-headers"></a>HTTP 要求ヘッダー

FarmBeats Datahub への API 呼び出しを行うときに指定する必要がある、最も一般的な要求ヘッダーを次に示します。

**ヘッダー** | **説明と例**
--- | ---
Content-Type  | 要求の形式 (Content-Type: application/<format>)。 FarmBeats Datahub API の場合、形式は JSON です。 Content-Type, application/json
承認 | API 呼び出しを行うために必要なアクセス トークンを指定します。 Authorization:Bearer <Access-Token>
Accept  | 応答形式。 FarmBeats Datahub API の場合、形式は JSON です。 Accept: application/json


## <a name="api-requests"></a>API 要求

REST API 要求を行うには、次のように組み合わせます。

- HTTP メソッド (GET、POST、および PUT)。
- API サービスの URL。
- リソース URI (クエリ、データの送信、更新、または削除)。
- 1 つ以上の HTTP 要求ヘッダー。

必要に応じて、GET 呼び出しにクエリ パラメーターを追加して、応答のデータのフィルター処理、サイズ制限、並べ替えを行うことができます。

次に示すのは、デバイスの一覧を取得する要求の例です。

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

ほとんどの GET、POST、PUT 呼び出しには、JSON 要求本文が必要です。

次に示すのは、デバイスを作成する要求の例です。 このサンプルには、要求本文での入力 JSON が含まれています。


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"accept: application/json" -H
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>データ形式

JSON は、任意のデータ構造をシンプルなテキストで表現する、言語に依存しない一般的なデータ形式です。 詳細については、[JSON.org](https://JSON.org) を参照してください。

## <a name="ingest-imagery-into-farmbeats"></a>FarmBeats に画像を取り込む

パートナーは FarmBeats Datahub に接続できる資格情報を取得した後、Translator コンポーネントで次の手順を実行します。

1.  アップロードされる画像の種類に応じて、次のフィールド用に新しい拡張型を作成します。

    - **Scene Source (シーン ソース)** :たとえば、drone_partner_name
    - **Scene Type (シーンの種類)** :たとえば、drone
    - **Scene File Type (シーン ファイルの種類)** :たとえば、chlorophyll index です
    - **Scene File Content Type (シーン ファイルのコンテンツ タイプ)** :たとえば、image/tiff です

2.  /Farms API を呼び出して、Azure FarmBeats システム内からファームの一覧を取得します。
3.  顧客がファームの一覧から 1 つのファームを選択できるようにします。

    パートナー システムでは、パートナー ソフトウェア内にファームを表示し、経路の計画、ドローンの飛行、画像の収集を行う必要があります。

4.  /Scene API を呼び出し、一意のシーン ID を持つ新しいシーンを作成するために必要な詳細情報を提供します。
5.  BLOB SAS URL を受け取り、必要な画像を FarmBeats Datahub にアップロードし、選択したファームのコンテキストで FarmBeats システムにアップロードします。

API 呼び出しの詳細なフローを次に示します。

### <a name="step-1-extendedtype"></a>手順 1:ExtendedType

FarmBeats で型とファイル ソースが使用可能かどうかを確認するには、/ExtendedType API にチェックインします。 これを行うには、/ExtendedType API に対して GET を呼び出します。

システム定義の値を次に示します。

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

この手順は、1 回限りのセットアップです。 この新しいシーンの種類のスコープは、FarmBeats がインストールされたサブスクリプションに限定されます。

たとえば、SceneSource: "SlantRange" を追加するには、キー "SceneSource" 入力ペイロードを使用し、/ExtendedType API の ID に対して PUT を実行します。

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

未開発の土地は、システム定義の Scene Source 値に新たに追加されます。

### <a name="step-2-get-farm-details"></a>手順 2:ファームの詳細を取得する

シーン (.tiff または .csv ファイル) は、ファームのコンテキストに含まれます。 /Farm API に対して GET を実行して、ファームの詳細を取得する必要があります。 API からは、FarmBeats で使用可能なファームの一覧が返されます。 データを取り込むファームを選択できます。

GET /Farm の応答:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>手順 3:シーン ID (POST 呼び出し) を作成する

提供された情報を使用して新しいシーン (.tiff または .csv ファイル) を作成し、このシーンが関連付けられる日付、シーケンス、ファーム ID を指定します。 このシーンに関連付けられるメタデータは、期間やメジャーの種類などのプロパティで定義できます。

新しいシーンを作成すると、ファームに関連付けられた新しいシーン ID が作成されます。 シーン ID が作成された後は、ユーザーはそれを使用して新しいファイル (.tiff または .csv) を作成し、ファイルの内容を保存できるようになります。

/Scene API に対する POST 呼び出しの入力ペイロードの例:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API の応答:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**シーン ファイルを作成する**

手順 3 で返されたシーン ID は、シーン ファイルの入力です。 シーン ファイルからは、24 時間有効な SAS URL トークンが返されます。

ユーザーが一連の画像をプログラムによってアップロードする必要がある場合は、Blob Storage SDK を使用すると、SceneFile ID、場所、URL を使ってメソッドを定義できます。

/SceneFile API に対する POST 呼び出しの入力ペイロードの例:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API の応答:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

/SceneFile API への POST 呼び出しでは、SAS アップロード URL が返されます。これは、Azure Blob Storage クライアントまたはライブラリを使用して .csv または .tiff ファイルをアップロードする際に利用できます。


## <a name="next-steps"></a>次のステップ

REST API ベースの統合の詳細については、「[REST API](rest-api-in-azure-farmbeats.md)」を参照してください。