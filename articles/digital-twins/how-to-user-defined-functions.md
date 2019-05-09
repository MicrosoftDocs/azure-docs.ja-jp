---
title: Azure Digital Twins 内でユーザー定義関数を作成する方法 | Microsoft Docs
description: Azure Digital Twins でユーザー定義関数、マッチャー、役割の割り当てを作成する方法。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: 7208f96d99127247b51510e0c43c1733bb327dfb
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076248"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Azure Digital Twins 内でユーザー定義関数を作成する方法

[ユーザー定義関数](./concepts-user-defined-functions.md)を使用すると、ユーザーは受信テレメトリ メッセージおよび空間グラフのメタデータから実行されるようにカスタム ロジックを構成できます。 ユーザーは定義済みの[エンドポイント](./how-to-egress-endpoints.md)にイベントを送信することもできます。

このガイドでは、受信した温度イベントから一定の温度を超えるすべての測定値を検出して警告する方法を示す例を見ていきます。

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>クライアント ライブラリ リファレンス

ユーザー定義関数の実行時のヘルパー メソッドとして使用できる関数の一覧が、[クライアント ライブラリ リファレンス](./reference-user-defined-functions-client-library.md) ドキュメントに示されています。

## <a name="create-a-matcher"></a>マッチャーを作成する

マッチャーは、特定のテレメトリ メッセージに対して実行されるユーザー定義関数を決定するグラフ オブジェクトです。

- 有効なマッチャー条件の比較は次のとおりです。

  - `Equals`
  - `NotEquals`
  - `Contains`

- 有効なマッチャー条件のターゲットは次のとおりです。

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

次の例のマッチャーは、データ型の値が `"Temperature"` であるすべてのセンサー テレメトリ イベントに対して true と評価されます。 ユーザー定義関数に複数のマッチャーを作成するには、以下に対して認証済みの HTTP POST 要求を実行します。

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

JSON 本文は次のようになります。

```JSON
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| 値 | 置換後の文字列 |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | インスタンスをホストするサーバーのリージョン |

## <a name="create-a-user-defined-function"></a>ユーザー定義関数を作成する

ユーザー定義関数を作成するには、Azure Digital Twins Management API にマルチパート HTTP 要求を行う必要があります。

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

マッチャーが作成された後、次の認証済みマルチパート HTTP POST 要求で関数スニペットをアップロードします。

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

以下の本文を使用します。

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| 値 | 置換後の文字列 |
| --- | --- |
| USER_DEFINED_BOUNDARY | マルチパート コンテンツ境界名 |
| YOUR_SPACE_IDENTIFIER | 空間識別子  |
| YOUR_MATCHER_IDENTIFIER | 使用するマッチャーの ID |

1. ヘッダーに `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"` が含まれていることを確認します。
1. 本文がマルチパートであることを確認します。

   - 最初の部分には、必須のユーザー定義関数メタデータが含まれています。
   - 2 番目の部分には、JavaScript の計算ロジックが含まれています。

1. **USER_DEFINED_BOUNDARY** セクションで、**spaceId** (`YOUR_SPACE_IDENTIFIER`) および **matchers** (`YOUR_MATCHER_IDENTIFIER`) の値を置き換えます。
1. JavaScript ユーザー定義関数が `Content-Type: text/javascript` として提供されていることを確認します。

### <a name="example-functions"></a>関数の例

データ型 **Temperature** のセンサー (`sensor.DataType`) に対して、センサー テレメトリの測定値を直接設定します。

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

**telemetry** パラメーターは、**SensorId** 属性と **Message** 属性 (センサーから送信されたメッセージに対応) を公開します。 **executionContext** パラメーターは、次の属性を公開します。

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

次の例では、センサー テレメトリの測定値が定義済みしきい値を超えた場合に、メッセージを記録します。 Azure Digital Twins インスタンスで診断設定が有効になっている場合は、ユーザー定義関数からのログも転送されます。

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

次のコードでは、温度レベルが定義済み定数を超えた場合に通知がトリガーされます。

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

より複雑なユーザー定義関数のコード サンプルについては、[占有率のクイック スタート](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)に関するページをご覧ください。

## <a name="create-a-role-assignment"></a>役割の割り当ての作成

ユーザー定義関数の実行に使われるロールの割り当てを作成します。 ユーザー定義関数にロールが割り当てられていない場合、ユーザー定義関数には Management API と対話したり、グラフ オブジェクトで操作を実行したりするための適切なアクセス許可がありません。 ユーザー定義関数で実行できるアクションは、Azure Digital Twins Management API 内のロールベースのアクセス制御によって指定および定義されます。 たとえば、ユーザー定義関数の範囲は、特定のロールまたは特定のアクセス制御パスを指定することで限定できます。 詳しくは、[ロールベースのアクセス制御](./security-role-based-access-control.md)のドキュメントをご覧ください。

1. すべてのロールについて[システム API に対してクエリを実行](./security-create-manage-role-assignments.md#all)して、ユーザー定義関数に割り当てるロール ID を取得します。 そのためには、以下に対して認証済みの HTTP GET 要求を実行します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   目的のロール ID を保持します。 これは、下で JSON 本文属性 **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) として渡されます。

1. **objectId** (`YOUR_USER_DEFINED_FUNCTION_ID`) は先ほど作成したユーザー定義関数 ID になります。
1. `fullpath` を使用して空間に対してクエリを実行して、**path** (`YOUR_ACCESS_CONTROL_PATH`) の値を見つけます。
1. 返された `spacePaths` 値をコピーします。 次のように使用します。 以下に対して認証済みの HTTP GET 要求を実行します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | 値 | 置換後の文字列 |
    | --- | --- |
    | YOUR_SPACE_NAME | 使用する空間の名前 |

1. 返された `spacePaths` 値を **path** に貼り付けて、認証済み HTTP POST 要求を以下に対して実行することでユーザー定義関数のロールの割り当てを作成します。

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    JSON 本文は次のようになります。

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | 値 | 置換後の文字列 |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | 目的の役割の識別子 |
    | YOUR_USER_DEFINED_FUNCTION_ID | 使用するユーザー定義関数の ID |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | ユーザー定義関数の種類を指定する ID |
    | YOUR_ACCESS_CONTROL_PATH | アクセス制御パス |

>[!TIP]
> ユーザー定義関数の Management API の操作とエンドポインについて詳しくは、[ロールの割り当てを作成および管理する方法](./security-create-manage-role-assignments.md)に関する記事をご覧ください。

## <a name="send-telemetry-to-be-processed"></a>処理するテレメトリを送信する

空間インテリジェンス グラフで定義されているセンサーによってテレメトリが送信されます。 さらに、テレメトリでは、アップロードされたユーザー定義関数の実行がトリガーされます。 データ プロセッサがテレメトリを取得します。 次に、ユーザー定義関数の呼び出し用の実行プランが作成されます。

1. 測定値の生成元であるセンサーのマッチャーを取得します。
1. どのマッチャーが正常に評価されたかに応じて、関連付けられているユーザー定義関数を取得します。
1. 各ユーザー定義関数を実行します。

## <a name="next-steps"></a>次の手順

- イベントの送信先となる [Azure Digital Twins エンドポイントを作成する](./how-to-egress-endpoints.md)方法を学習する。

- Azure Digital Twins でのルーティングについて詳しくは、「[ルーティング イベントおよびメッセージ](./concepts-events-routing.md)」をご覧ください。

- [クライアント ライブラリ リファレンス ドキュメント](./reference-user-defined-functions-client-library.md)をご覧ください。
