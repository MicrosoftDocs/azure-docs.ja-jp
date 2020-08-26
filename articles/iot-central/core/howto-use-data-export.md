---
title: IoT Central からデータをエクスポートする | Microsoft Docs
description: 新しいデータ エクスポートを使用して Azure とカスタム クラウドの宛先に IoT データをエクスポートする方法について説明します。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: f51630154b77233aeb2587ac3a2d603c1da6fa4f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036557"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>データ エクスポートを使用してクラウドの宛先に IoT データをエクスポートする (プレビュー)

> [!Note]
> 従来のデータ エクスポートをお探しですか。 データ エクスポートに関するドキュメントは、[こちら](./howto-export-data.md)から参照できます。 新しいデータ エクスポートと従来のデータ エクスポートの違いについては、[比較表](#comparison-of-legacy-data-export-and-new-data-export)を参照してください。

この記事では、Azure IoT Central の新しいデータ エクスポートのプレビュー機能を使用する方法について説明します。 この機能を使用すると、フィルター処理およびエンリッチ化された IoT データをクラウド サービスに連続エクスポートできます。 データ エクスポートを使用して、ウォーム パスの分析情報、分析、およびストレージ用に、クラウド ソリューションの他の部分にほぼリアルタイムで変更をプッシュできます。 

 たとえば、次のように操作できます。
-   テレメトリ データとプロパティの変更を JSON 形式でほぼリアルタイムに連続エクスポートする
-   これらのデータ ストリームをフィルター処理して、カスタム条件に一致する特定の機能をエクスポートする
-   デバイスからのカスタム値とプロパティ値を使用してデータ ストリームをエンリッチ化する
-   このデータを Azure Event Hubs、Azure Service Bus、Azure Blob Storage、Webhook エンドポイントなどの宛先に送信する

> [!Note]
> データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、データ エクスポートを早い段階で有効にしてください。

## <a name="prerequisites"></a>前提条件

データ エクスポート (プレビュー) を使用するには、V3 アプリケーションが必要です。また、データ エクスポートのアクセス許可が必要です。

## <a name="set-up-export-destination"></a>エクスポート先の設定

データ エクスポートを構成する前に、エクスポート先が存在している必要があります。 使用可能な宛先の種類は次のとおりです。
  - Azure Event Hubs
  - Azure Service Bus キュー
  - Azure Service Bus トピック
  - Azure Blob Storage
  - Webhook

### <a name="create-an-event-hubs-destination"></a>Event Hubs の宛先を作成する

エクスポート先となる既存の Event Hubs 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Event Hubs 名前空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)を作成します。 詳細については、[Azure Event Hubs のドキュメント](../../event-hubs/event-hubs-create.md)を参照してください。

2. Event Hubs 名前空間にイベント ハブを作成します。 名前空間に移動し、上部の **[+ イベント ハブ]** を選択して、イベント ハブ インスタンスを作成します。

3. データ エクスポートを設定するために IoT Central で使用するキーを生成します。 
    - 作成したイベント ハブ インスタンスをクリックします。 
    - **[設定]、[共有アクセス ポリシー]** の順にクリックします。 
    - 新しいキーを作成するか、**送信**アクセス許可を持っている既存のキーを選択します。 
    - プライマリ キーまたはセカンダリの接続文字列をコピーします。 これは、IoT Central に新しい宛先を設定するために使用します。

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Service Bus キューまたはトピックの宛先を作成する

エクスポート先となる既存の Service Bus 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Service Bus 名前空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)を作成します。 詳細については、[Azure Service Bus のドキュメント](../../service-bus-messaging/service-bus-create-namespace-portal.md)を参照してください。

2. エクスポート先のキューまたはトピックを作成するには、Service Bus 名前空間に移動し、 **[+ キュー]** または **[+ トピック]** を選択します。

3. データ エクスポートを設定するために IoT Central で使用するキーを生成します。 
    - 作成したキューまたはトピックをクリックします。 
    - **[設定]、[共有アクセス ポリシー]** の順にクリックします。 
    - 新しいキーを作成するか、**送信**アクセス許可を持っている既存のキーを選択します。 
    - プライマリ キーまたはセカンダリの接続文字列をコピーします。 これは、IoT Central に新しい宛先を設定するために使用します。

### <a name="create-an-azure-blob-storage-destination"></a>Azure Blob Storage の宛先を作成する

エクスポート先となる既存の Azure Storage アカウントがない場合は、次の手順に従います。

1. [Azure portal で新しいストレージ アカウント](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)を作成します。 新しい [Azure Blob ストレージ アカウント](https://aka.ms/blobdocscreatestorageaccount)または [Azure Data Lake Storage v2 ストレージ アカウント](../../storage/blobs/data-lake-storage-quickstart-create-account.md)の作成の詳細を確認できます。 データのエクスポートでは、ブロック BLOB をサポートするストレージ アカウントにのみデータを書き込めます。 次の一覧は、互換性のある既知のストレージ アカウントの種類を示しています。

    |パフォーマンス レベル|アカウントの種類|
    |-|-|
    |Standard|General Purpose V2|
    |Standard|General Purpose V1|
    |Standard|BLOB ストレージ|
    |Premium|ブロック BLOB ストレージ|

2. ご自分のストレージ アカウントでコンテナーを作成します。 ストレージ アカウントに移動します。 **[Blob service]** で **[BLOB の参照]** を選択します。 上部の **[+ コンテナー]** を選択して、新しいコンテナーを作成します。

3. **[設定]、[アクセス キー]** の順に移動してストレージ アカウントの接続文字列を生成します。 2 つの接続文字列のうち 1 つをコピーします。

### <a name="create-a-webhook-endpoint"></a>Webhook エンドポイントを作成する
パブリックに使用できる HTTP エンドポイントをデータのエクスポート先として指定できます。 RequestBin を使用して、テスト用の Webhook エンドポイントを作成できます。 RequestBin には要求の制限が設定されていて、それを超えると要求が調整されることにご注意ください。

1. [RequestBin](https://requestbin.net/) を開きます。
2. 新しい RequestBin を作成し、Bin URL をコピーします。

## <a name="set-up-data-export"></a>データ エクスポートの設定

これでデータのエクスポート先ができたので、次の手順に従ってデータ エクスポートを設定します。

1. ご使用の IoT Central アプリケーションにサインインします。

2. 左側のペインで、 **[データのエクスポート]** を選択します。

    > [!Tip]
    > 左側のペインに **[データのエクスポート]** が表示されない場合は、アプリでデータ エクスポートを構成するアクセス許可がありません。 データ エクスポートの設定について、管理者に問い合わせてください。

3. **[+ 新しいエクスポート]** ボタンを選択します。 

4. 新しいエクスポートの表示名を入力し、データが流れるように **[有効]** トグルを確実にオンにします。

## <a name="1-choose-the-type-of-data-to-export"></a>1.エクスポートするデータの種類を選択する
異なるデータの種類の中から連続エクスポートするものを選択できます。 サポートされているデータの種類は次のとおりです。

| データ型 | 説明 | データ形式 |
| :------------- | :---------- | :----------- |
|  テレメトリ | デバイスからのテレメトリ メッセージがほぼリアルタイムでエクスポートされます。 エクスポートされた各メッセージには、元のデバイス メッセージの完全な内容が正規化されて含まれます。   |  [テレメトリ メッセージの形式](#telemetry-format)   |
| プロパティ変更 | デバイスとクラウドのプロパティに対する変更がほぼリアルタイムでエクスポートされます。 読み取り専用のデバイス プロパティでは、報告された値に対する変更がエクスポートされます。 読み取り/書き込みプロパティの場合、報告された値と必要な値の両方がエクスポートされます。 | [プロパティ変更メッセージの形式](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2.(省略可能) フィルターを追加する
フィルターを追加して、フィルター条件に基づいてエクスポートされるデータの量を減らします。 エクスポートするデータの種類ごとに、使用できるフィルターの種類は異なります。

### <a name="telemetry-filters"></a>テレメトリ フィルター
  - **機能フィルター**: ドロップダウンでテレメトリ項目を選択すると、エクスポートされたストリームには、フィルター条件を満たすテレメトリだけが含まれます。 ドロップダウンでデバイスまたはクラウドのプロパティ項目を選択すると、エクスポートされたストリームには、フィルター条件に一致するプロパティを持つデバイスからのテレメトリだけが含まれます。
  - **メッセージ プロパティのフィルター**: デバイス SDK を使用しているデバイスには、各テレメトリ メッセージで "*メッセージ プロパティ*" または "*アプリケーション プロパティ*" を送信することが許可されています。これは、一般的にカスタム識別子を使用してメッセージにタグを付けるために使用される、キーと値のペアのバッグです。 メッセージ プロパティ フィルターは、検索するメッセージ プロパティ キーを入力し、条件を指定すると作成できます。 指定したフィルター条件に一致するメッセージ プロパティを持つテレメトリ メッセージのみがエクスポートされます。 文字列比較演算子のみがサポートされています (等しい、等しくない、次を含む、次を含まない、存在する、存在しない)。 [アプリケーション プロパティの詳細については、IoT Hub のドキュメントを参照してください](../../iot-hub/iot-hub-devguide-messages-construct.md)。

### <a name="property-changes-filters"></a>プロパティ変更フィルター
**プロパティ フィルター**: ドロップダウンでプロパティ項目を選択すると、エクスポートされたストリームには、フィルター条件を満たす選択したプロパティへの変更のみが含まれます。

## <a name="3-optional-add-enrichments"></a>3.(省略可能) エンリッチメントを追加する
キーと値のペアに追加のメタデータを使用して、エクスポートされたメッセージをエンリッチ化するエンリッチメントを追加します。 テレメトリとプロパティ変更というデータの種類に使用できるエンリッチメントは次のとおりです。
  - **カスタム文字列**: 各メッセージに静的なカスタム文字列を追加します。 任意のキーを入力し、任意の文字列値を入力します。
  - **プロパティ**: デバイスから報告された現在のプロパティまたはクラウド プロパティの値を各メッセージに追加します。 任意のキーを入力し、デバイスまたはクラウドのプロパティを選択します。 エクスポートされたメッセージが、指定したデバイスまたはクラウドのプロパティを持たないデバイスからのものである場合、エクスポートされたメッセージにそのエンリッチメントは含まれません。

## <a name="4-add-destinations"></a>4.宛先を追加する
新しい宛先を作成するか、既に作成済みの宛先を追加します。 
  
1. **新しい宛先を作成する**リンクをクリックします。 次の情報を入力します。
    - **宛先名**: IoT Central 内の宛先の表示名
    - **宛先の種類**: 宛先の種類を選択します。 まだ設定していない場合は、[エクスポート先を設定](#set-up-export-destination)します。
    - Azure Event Hubs、Azure Service Bus キューまたはトピックの場合は、リソースの接続文字列を貼り付けます。 
    - Azure Blob Storage の場合は、リソースの接続文字列を貼り付け、コンテナー名 (大文字と小文字を区別) を入力します。
    - Webhook の場合は、Webhook エンドポイントのコールバック URL を貼り付けます。 
    - **[作成]** をクリックします。

2. **[+ 宛先]** をクリックし、ドロップダウンから宛先を選択します。 1 つのエクスポートに最大 5 つの宛先を追加できます。

3. エクスポートの設定が完了したら、 **[保存]** をクリックします。 数分後、宛先にデータが表示されます。

## <a name="export-contents-and-format"></a>エクスポートの内容と形式

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage の宛先

データは 1 分に 1 回エクスポートされ、各ファイルには、最後にエクスポートされたファイル以降の変更のバッチが含まれます。 エクスポートされたデータは、JSON 形式で 3 つのフォルダーに配置されます。 ストレージ アカウントでの既定のパスは次のとおりです。

- テレメトリ: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- プロパティ変更: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Azure portal でエクスポートされたファイルを参照するには、そのファイルに移動し、 **[BLOB の編集]** タブを選択します。

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs および Azure Service Bus の宛先

データはほぼリアルタイムでエクスポートされます。 データはメッセージ本文に含まれ、UTF-8 としてエンコードされた JSON 形式です。 

メッセージの注釈またはシステム プロパティ バッグ内に、メッセージ本文の対応するフィールドと同じ値を持つ `iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source`、`iotcentral-message-type` があります。

### <a name="webhook-destination"></a>Webhook の宛先
Webhook が宛先の場合も、データはほぼリアルタイムでエクスポートされます。 メッセージ本文のデータは、Event Hubs および Service Bus と同じ形式です。


## <a name="telemetry-format"></a>テレメトリ形式
エクスポートされた各メッセージには、UTF-8 としてエンコードされた JSON 形式のメッセージ本文でデバイスから送信された、完全なメッセージが正規化された形式で含まれます。 各メッセージに含まれる追加情報は次のとおりです。

- IoT Central アプリの `applicationId`
- テレメトリ データをエクスポートするための "*テレメトリ*" である `messageSource`
- テレメトリ メッセージを送信したデバイスの `deviceId`
- `schema` はペイロード スキーマの名前とバージョンです
- `templateId` はデバイスに関連付けられているデバイス テンプレート ID です
- `enrichments` はエクスポートに設定されたエンリッチメントです
- `messageProperties` は、デバイスからメッセージと共に送信されたデータの追加の部分です。 これは "*アプリケーション プロパティ*" とも呼ばれます。[詳細については、IoT Hub のドキュメントを参照してください](../../iot-hub/iot-hub-devguide-messages-construct.md)。

Event Hubs と Service Bus の場合、IoT Central は、デバイスからメッセージを受信した後、すぐに新しいメッセージをエクスポートします。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。

次の例は、エクスポートされたテレメトリ メッセージを示しています。

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
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

## <a name="property-changes-format"></a>プロパティ変更の形式

各メッセージまたはレコードは、デバイスまたはクラウドのプロパティに対する 1 つの変更を表します。 デバイスのプロパティの場合、報告された値の変更のみが別個のメッセージとしてエクスポートされます。 エクスポートされたメッセージに含まれる追加情報は次のとおりです。

- IoT Central アプリの `applicationId`
- プロパティ変更データをエクスポートするための "*プロパティ*" である `messageSource`
- `messageType` (*cloudPropertyChange* または *devicePropertyDesiredChange*、*devicePropertyReportedChange* のいずれか)
- プロパティが変更されたデバイスの `deviceId`
- `schema` はペイロード スキーマの名前とバージョンです
- `templateId` はデバイスに関連付けられているデバイス テンプレート ID です
- `enrichments` はエクスポートに設定されたエンリッチメントです

Event Hubs と Service Bus の場合、IoT Central から新しいメッセージ データが、ほぼリアルタイムでイベント ハブあるいは Service Bus キューまたはトピックに送信されます。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。

次の例は、Azure Blob Storage で受信したエクスポートされたプロパティ変更メッセージを示しています。

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>従来のデータ エクスポートと新しいデータ エクスポートの比較
これは従来のデータ エクスポートと新しいデータ エクスポートの違いを強調した表です。 従来のデータ エクスポートについては、[こちら](howto-export-data.md)を参照してください。

| 機能  | 従来のデータ エクスポート | 新しいデータ エクスポート |
| :------------- | :---------- | :----------- |
| 使用できるデータの種類 | テレメトリ、デバイス、デバイス テンプレート | テレメトリ、プロパティ変更 |
| フィルター処理 | なし | エクスポートするデータの種類によって異なります。 テレメトリの場合は、テレメトリ、メッセージ プロパティ、プロパティ値によるフィルター処理 |
| エンリッチメント | なし | デバイスのカスタム文字列またはプロパティ値を使用してエンリッチ化する |
| 変換先 | Azure Event Hubs、Azure Service Bus キューおよびトピック、Azure Blob Storage | 従来のデータ エクスポートおよび Webhook の場合と同じ| 
| サポート対象アプリ | V2、V3 | V3 のみ |
| 重要な制限 | アプリごとに 5 つのエクスポート、エクスポートごとに 1 つの宛先 | エクスポートの宛先の接続数はアプリごとに 10 個 | 

## <a name="next-steps"></a>次の手順

新しいデータ エクスポートの使用方法がわかったので、次の手順に進みます。

> [!div class="nextstepaction"]
> [IoT Central で分析を使用する方法](./howto-create-analytics.md)
