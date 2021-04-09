---
title: Azure IoT Central からデータをエクスポートする | Microsoft Docs
description: 新しいデータ エクスポートを使用して Azure とカスタム クラウドの宛先に IoT データをエクスポートする方法について説明します。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: 7152012c7c4a342c7491e5f8b835eaede4269c4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100522616"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>データ エクスポートを使用してクラウドの宛先に IoT データをエクスポートする

> [!Note]
> この記事では、IoT Central のデータ エクスポート機能について説明します。 従来のデータ エクスポート機能の詳細については、[データ エクスポートを使用してクラウドの宛先に IoT データをエクスポートする (レガシ)](./howto-export-data-legacy.md) に関するページを参照してください。

この記事では、Azure IoT Central の新しいデータ エクスポート機能を使用する方法について説明します。 この機能を使用して、フィルター処理およびエンリッチ化された IoT データを IoT Central アプリケーションから連続エクスポートします。 データ エクスポートを使用すると、ウォーム パスの分析情報、分析、およびストレージ用に、クラウド ソリューションの他の部分にほぼリアルタイムで変更がプッシュされます。

たとえば、次のように操作できます。

- テレメトリ データとプロパティの変更を JSON 形式でほぼリアルタイムに連続エクスポートする
- データ ストリームをフィルター処理して、カスタム条件に一致するデータをエクスポートする
- デバイスからのカスタム値とプロパティ値を使用してデータ ストリームをエンリッチ化する
- データを Azure Event Hubs、Azure Service Bus、Azure Blob Storage、Webhook エンドポイントなどの宛先に送信する

> [!Tip]
> データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、データ エクスポートを早い段階で有効にしてください。

## <a name="prerequisites"></a>前提条件

データ エクスポート機能を使用するには、[V3 アプリケーション](howto-get-app-info.md)が必要です。また、[データ エクスポート](howto-manage-users-roles.md)のアクセス許可が必要です。

V2 アプリケーションをご使用の場合は、[V3 への V2 IoT Central アプリケーションの移行](howto-migrate.md)に関する記事を参照してください。

## <a name="set-up-export-destination"></a>エクスポート先の設定

データ エクスポートを構成する前に、エクスポート先が存在している必要があります。 現在、次のエクスポート先の種類を使用できます。

- Azure Event Hubs
- Azure Service Bus キュー
- Azure Service Bus トピック
- Azure Blob Storage
- Webhook

### <a name="create-an-event-hubs-destination"></a>Event Hubs の宛先を作成する

エクスポート先となる既存の Event Hubs 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Event Hubs 名前空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)を作成します。 詳細については、[Azure Event Hubs のドキュメント](../../event-hubs/event-hubs-create.md)を参照してください。

1. Event Hubs 名前空間にイベント ハブを作成します。 名前空間に移動し、上部の **[+ イベント ハブ]** を選択して、イベント ハブ インスタンスを作成します。

1. IoT Central でデータ エクスポートを設定するときに使用するキーを生成します。

    - 作成したイベント ハブ インスタンスを選択します。
    - **[設定]、[共有アクセス ポリシー]** の順に選択します。
    - 新しいキーを作成するか、**送信** アクセス許可を持っている既存のキーを選択します。
    - プライマリ キーまたはセカンダリの接続文字列をコピーします。 この接続文字列は、IoT Central に新しい宛先を設定するために使用します。
    - または、Event Hubs 名前空間全体の接続文字列を生成することもできます。
        1. Azure portal で Event Hubs 名前空間にアクセスします。
        2. **[設定]** で、 **[共有アクセス ポリシー]** を選択します。
        3. 新しいキーを作成するか、**送信** アクセス許可を持っている既存のキーを選択します。
        4. プライマリまたはセカンダリの接続文字列をコピーします。
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Service Bus キューまたはトピックの宛先を作成する

エクスポート先となる既存の Service Bus 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Service Bus 名前空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)を作成します。 詳細については、[Azure Service Bus のドキュメント](../../service-bus-messaging/service-bus-create-namespace-portal.md)を参照してください。

1. エクスポート先のキューまたはトピックを作成するには、Service Bus 名前空間に移動し、 **[+ キュー]** または **[+ トピック]** を選択します。

1. IoT Central でデータ エクスポートを設定するときに使用するキーを生成します。

    - 作成したキューまたはトピックをクリックします。
    - **[設定]/[共有アクセス ポリシー]** の順に選択します。
    - 新しいキーを作成するか、**送信** アクセス許可を持っている既存のキーを選択します。
    - プライマリ キーまたはセカンダリの接続文字列をコピーします。 この接続文字列は、IoT Central に新しい宛先を設定するために使用します。
    - または、Service Bus 名前空間全体の接続文字列を生成することもできます。
        1. Azure portal で、ご利用の Service Bus 名前空間に移動します。
        2. **[設定]** で、 **[共有アクセス ポリシー]** を選択します。
        3. 新しいキーを作成するか、**送信** アクセス許可を持っている既存のキーを選択します。
        4. プライマリまたはセカンダリの接続文字列をコピーします。

### <a name="create-an-azure-blob-storage-destination"></a>Azure Blob Storage の宛先を作成する

エクスポート先となる既存の Azure Storage アカウントがない場合は、次の手順に従います。

1. [Azure portal で新しいストレージ アカウント](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)を作成します。 新しい [Azure Blob ストレージ アカウント](../../storage/blobs/storage-quickstart-blobs-portal.md)または [Azure Data Lake Storage v2 ストレージ アカウント](../../storage/common/storage-account-create.md)の作成の詳細を確認できます。 データのエクスポートでは、ブロック BLOB をサポートするストレージ アカウントにのみデータを書き込めます。 次の一覧は、互換性のある既知のストレージ アカウントの種類を示しています。

    |パフォーマンス レベル|アカウントの種類|
    |-|-|
    |Standard|General Purpose V2|
    |Standard|General Purpose V1|
    |Standard|BLOB ストレージ|
    |Premium|ブロック BLOB ストレージ|

1. ストレージ アカウントにコンテナーを作成するには、ストレージ アカウントにアクセスします。 **[Blob service]** で **[BLOB の参照]** を選択します。 上部の **[+ コンテナー]** を選択して、新しいコンテナーを作成します。

1. **[設定] > [アクセス キー]** の順に移動してストレージ アカウントの接続文字列を生成します。 2 つの接続文字列のうち 1 つをコピーします。

### <a name="create-a-webhook-endpoint"></a>Webhook エンドポイントを作成する

パブリックに使用できる HTTP Webhook エンドポイントにデータをエクスポートできます。 [RequestBin](https://requestbin.net/) を使用して、テスト用の Webhook エンドポイントを作成できます。 RequestBin は、要求の上限に達したときに、要求をスロットルします。

1. [RequestBin](https://requestbin.net/) を開きます。
2. 新しい RequestBin を作成し、**Bin URL** をコピーします。 この URL は、データ エクスポートをテストするときに使用します。

## <a name="set-up-data-export"></a>データ エクスポートの設定

データのエクスポート先を準備したので、IoT Central アプリケーションでデータのエクスポートを設定します。

1. ご使用の IoT Central アプリケーションにサインインします。

1. 左側のペインで、 **[データのエクスポート]** を選択します。

    > [!Tip]
    > 左側のペインに **[データのエクスポート]** が表示されない場合は、アプリでデータ エクスポートを構成するアクセス許可がありません。 データ エクスポートの設定について、管理者に問い合わせてください。

1. **[+ 新しいエクスポート]** を選択します。

1. 新しいエクスポートの表示名を入力し、データ エクスポートが **[有効]** になっていることを確認します。

1. エクスポートするデータの種類を選択します。 次の表は、サポートされるデータ エクスポートの型の一覧を示します。

    | データ型 | 説明 | データ形式 |
    | :------------- | :---------- | :----------- |
    |  テレメトリ | デバイスからのテレメトリ メッセージがほぼリアルタイムでエクスポートされます。 エクスポートされた各メッセージには、元のデバイス メッセージの完全な内容が正規化されて含まれます。   |  [テレメトリ メッセージの形式](#telemetry-format)   |
    | プロパティ変更 | デバイスとクラウドのプロパティに対する変更がほぼリアルタイムでエクスポートされます。 読み取り専用のデバイス プロパティでは、報告された値に対する変更がエクスポートされます。 読み取り/書き込みプロパティの場合、報告された値と必要な値の両方がエクスポートされます。 | [プロパティ変更メッセージの形式](#property-changes-format) |

<a name="DataExportFilters"></a>
1. 必要に応じて、フィルターを追加して、エクスポートするデータの量を減らします。 データ エクスポートの型ごとに、使用できるフィルターの種類は異なります。

    テレメトリをフィルター処理する場合、以下があります。

    - デバイス名、デバイス ID、およびデバイス テンプレートのフィルター条件に一致するデバイスのテレメトリだけを格納するように、エクスポートされたストリームを **フィルター処理** します。
    - 機能を **フィルター処理** する: **[名前]** ドロップダウンでテレメトリ項目を選択すると、エクスポートされたストリームには、フィルター条件を満たすテレメトリだけが含まれます。 **[名前]** ドロップダウンでデバイスまたはクラウドのプロパティ項目を選択すると、エクスポートされたストリームには、フィルター条件に一致するプロパティを持つデバイスからのテレメトリだけが含まれます。
    - **メッセージ プロパティのフィルター**: デバイス SDK を使用するデバイスでは、各テレメトリ メッセージで *メッセージのプロパティ* または *アプリケーションのプロパティ* を送信できます。 これらのプロパティは、メッセージにカスタム識別子をタグを付けるキーと値のペアのバッグです。 メッセージ プロパティ フィルターを作成するには、検索するメッセージ プロパティ キーを入力し、条件を指定します。 指定したフィルター条件に一致するプロパティを持つテレメトリ メッセージのみがエクスポートされます。 次の文字列比較演算子がサポートされています: 等しい、等しくない、次を含む、次を含まない、存在する、存在しない。 [アプリケーション プロパティの詳細については、IoT Hub のドキュメントを参照してください](../../iot-hub/iot-hub-devguide-messages-construct.md)。

    プロパティの変更をフィルター処理するには、**機能フィルター** を使用します。 ドロップダウンでプロパティ項目を選択します。 エクスポートされたストリームには、フィルター条件を満たす選択したプロパティへの変更のみが含まれます。

<a name="DataExportEnrichmnents"></a>
1. 必要に応じて、追加のキーと値のペアのメタデータを使用してエクスポートしたメッセージをエンリッチ化します。 次のエンリッチメントは、テレメトリとプロパティ変更のデータ エクスポートの型で使用できます。

    - **カスタム文字列**: 各メッセージに静的なカスタム文字列を追加します。 任意のキーを入力し、任意の文字列値を入力します。
    - **プロパティ**: デバイスから報告された現在のプロパティまたはクラウド プロパティの値を各メッセージに追加します。 任意のキーを入力し、デバイスまたはクラウドのプロパティを選択します。 エクスポートされたメッセージが、指定したプロパティを持たないデバイスからのものである場合、エクスポートされたメッセージにはそのエンリッチメントがありません。

1. 新しい宛先を追加するか、既に作成済みの宛先を追加します。 **[Create a new one]\(新しく作成する\)** リンクを選択し、次の情報を追加します。

    - **宛先名**: IoT Central 内の宛先の表示名
    - **宛先の種類**: 宛先の種類を選択します。 宛先をまだ設定していない場合は、「[エクスポート先の設定](#set-up-export-destination)」を参照してください。
    - Azure Event Hubs、Azure Service Bus キューまたはトピックの場合は、リソースの接続文字列を貼り付け、必要に応じてイベント ハブ、キュー、またはトピック名を大文字と小文字を区別して入力します。
    - Azure Blob Storage の場合は、リソースの接続文字列を貼り付け、必要に応じてコンテナー名を大文字と小文字を区別して入力します。
    - Webhook の場合は、Webhook エンドポイントのコールバック URL を貼り付けます。 必要に応じて、Webhook 認可 (OAuth 2.0 および認可トークン) を構成し、カスタム ヘッダーを追加することができます。 
        - OAuth 2.0 の場合は、クライアント資格情報フローのみがサポートされます。 宛先を保存すると、IoT Central では OAuth プロバイダーと通信して認証トークンが取得されます。 このトークンは、この宛先に送信されるすべてのメッセージの "Authorization" ヘッダーに添付されます。
        - 認可トークンの場合、この宛先に送信されるすべてのメッセージの "Authorization" ヘッダーに直接添付されるトークン値を指定できます。
    - **［作成］** を選択します

1. **[+ 宛先]** を選択し、ドロップダウンから宛先を選択します。 1 つのエクスポートに最大 5 つの宛先を追加できます。

1. エクスポートの設定が完了したら、 **[保存]** を選択します。 数分後に、エクスポート先にデータが表示されます。

## <a name="monitor-your-export"></a>エクスポートの監視

IoT Central でエクスポートの状態を確認するだけでなく、[Azure Monitor](../../azure-monitor/overview.md) を使用して、エクスポートしているデータの量やエクスポート エラーを確認することもできます。 エクスポートとデバイスの正常性のメトリックには、Azure portal 内のグラフ、REST API、または PowerShell や Azure CLI のクエリを使用してアクセスできます。 現時点では、Azure Monitor で次のデータ エクスポート メトリックを監視できます。

- フィルター適用前のエクスポート対象受信メッセージの数。
- フィルターを通過したメッセージの数。
- 宛先に正常にエクスポートされたメッセージの数。
- 発生したエラーの数。

詳細については、「[IoT Central アプリケーションの全体的な正常性状態を監視する](howto-monitor-application-health.md)」を参照してください。

## <a name="destinations"></a>変換先

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage の宛先

データは 1 分に 1 回エクスポートされ、各ファイルには、前のエクスポート以降の変更のバッチが含まれます。 エクスポートされたデータは JSON 形式で保存されます。 ストレージ アカウント内のエクスポートされたデータの既定のパスは次のとおりです。

- テレメトリ: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- プロパティ変更: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Azure portal でエクスポートされたファイルを参照するには、そのファイルに移動し、 **[BLOB の編集]** を選択します。

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs および Azure Service Bus の宛先

データはほぼリアルタイムでエクスポートされます。 データはメッセージ本文に含まれ、UTF-8 としてエンコードされた JSON 形式です。

メッセージの注釈またはシステム プロパティ バッグ内には、メッセージ本文の対応するフィールドと同じ値を持つ `iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source`、および `iotcentral-message-type` フィールドがあります。

### <a name="webhook-destination"></a>Webhook の宛先

Webhook が宛先の場合も、データはほぼリアルタイムでエクスポートされます。 メッセージ本文のデータは、Event Hubs および Service Bus と同じ形式です。

## <a name="telemetry-format"></a>テレメトリ形式

エクスポートされた各メッセージには、メッセージ本文でデバイスから送信された、完全なメッセージが正規化された形式で含まれます。 メッセージは JSON 形式で、UTF-8 としてエンコードされます。 各メッセージに含まれる情報は次のとおりです。

- `applicationId`:IoT Central アプリケーションの ID。
- `messageSource`:メッセージのソース - `telemetry`。
- `deviceId`:テレメトリ メッセージを送信したデバイスの ID。
- `schema`:ペイロード スキーマの名前とバージョン。
- `templateId`:デバイスに関連付けられているデバイス テンプレートの ID。
- `enrichments`:エクスポートに設定されたエンリッチメント。
- `messageProperties`:デバイスからメッセージと一緒に送信された追加のプロパティ。 これらのプロパティは *アプリケーション プロパティ* と呼ばれることもあります。 [詳細については IoT Hub のドキュメントを参照してください](../../iot-hub/iot-hub-devguide-messages-construct.md)。

Event Hubs と Service Bus の場合、IoT Central は、デバイスからメッセージを受信した後、すぐに新しいメッセージをエクスポートします。 各メッセージのユーザー プロパティ (アプリケーション プロパティとも呼ばれます) では、`iotcentral-device-id`、`iotcentral-application-id`、および `iotcentral-message-source` が自動的に含まれます。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。

次の例は、エクスポートされたテレメトリ メッセージを示しています。

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>メッセージのプロパティ

テレメトリ メッセージには、テレメトリ ペイロードに加え、メタデータのプロパティが含まれています。 前のスニペットは、`deviceId` や `enqueuedTime` など、システム メッセージの例を示しています。 システム メッセージ プロパティの詳細については、「[D2C IoT Hub メッセージのシステム プロパティ](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages)」を参照してください。

テレメトリ メッセージにカスタム メタデータを追加する必要がある場合、テレメトリ メッセージにプロパティを追加できます。 たとえば、デバイスでメッセージが作成されるとき、タイムスタンプを追加する必要があります。

次のコード スニペットは、デバイス上でメッセージを作成するとき、それに `iothub-creation-time-utc` プロパティを追加する方法を示しています。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

次のスニペットは、BLOB ストレージにエクスポートされたメッセージにおけるこのプロパティを示しています。

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>プロパティ変更の形式

各メッセージまたはレコードは、デバイスまたはクラウドのプロパティに対する 1 つの変更を表します。 デバイスのプロパティの場合、報告された値の変更のみが別個のメッセージとしてエクスポートされます。 エクスポートされたメッセージに含まれる情報は次のとおりです。

- `applicationId`:IoT Central アプリケーションの ID。
- `messageSource`:メッセージのソース - `properties`。
- `messageType`:`cloudPropertyChange`、`devicePropertyDesiredChange` または `devicePropertyReportedChange` のいずれかです。
- `deviceId`:テレメトリ メッセージを送信したデバイスの ID。
- `schema`:ペイロード スキーマの名前とバージョン。
- `templateId`:デバイスに関連付けられているデバイス テンプレートの ID。
- `enrichments`:エクスポートに設定されたエンリッチメント。

Event Hubs と Service Bus の場合、IoT Central から新しいメッセージ データが、ほぼリアルタイムでイベント ハブあるいは Service Bus キューまたはトピックに送信されます。 各メッセージのユーザー プロパティ (アプリケーション プロパティとも呼ばれます) では、`iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source` および `iotcentral-message-type` が自動的に含まれます。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。

次の例は、Azure Blob Storage で受信したエクスポートされたプロパティ変更メッセージを示しています。

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>従来のデータ エクスポートとデータ エクスポートの比較

次の表は、[従来のデータ エクスポート](howto-export-data-legacy.md)と新しいデータ エクスポートの機能の違いを示しています。

| 機能  | 従来のデータ エクスポート | 新しいデータ エクスポート |
| :------------- | :---------- | :----------- |
| 使用できるデータの種類 | テレメトリ、デバイス、デバイス テンプレート | テレメトリ、プロパティ変更 |
| フィルター処理 | なし | エクスポートするデータの種類によって異なります。 テレメトリの場合は、テレメトリ、メッセージ プロパティ、プロパティ値によるフィルター処理 |
| エンリッチメント | なし | デバイスのカスタム文字列またはプロパティ値を使用してエンリッチ化する |
| 変換先 | Azure Event Hubs、Azure Service Bus キューおよびトピック、Azure Blob Storage | 従来のデータ エクスポートと同じ宛先に加えて Webhook|
| サポートされているアプリケーションのバージョン | V2、V3 | V3 のみ |
| 重要な制限 | アプリごとに 5 つのエクスポート、エクスポートごとに 1 つの宛先 | エクスポートの宛先の接続数はアプリごとに 10 個 |

## <a name="next-steps"></a>次の手順

新しいデータ エクスポートの使用方法を理解できたので、次の手順として、[IoT Central で分析を使用する方法](./howto-create-analytics.md)の習得に進むことをお勧めします
