---
title: Live Video Analytics on IoT Edge でダイレクト メソッドを使用する - Azure
description: Live Video Analytics on IoT Edge では、いくつかのダイレクト メソッドが公開されています。 ダイレクト メソッドは、このトピックで説明する規則に基づいています。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8b5c16dc72beed4ec757e48461a2fc194c113f8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656242"
---
# <a name="direct-methods"></a>ダイレクト メソッド

Live Video Analytics on IoT Edge では、IoT Hub から呼び出すことができるダイレクト メソッドがいくつか公開されています。 ダイレクト メソッドは、デバイスとの要求/応答型通信を表し、すぐに要求の成功または失敗が確定する (ユーザーが指定したタイムアウト後) という点で HTTP 呼び出しに似ています。 この方法は、デバイスが応答できるかどうかに応じて即座に実行するアクションが異なるシナリオで便利です。 詳細については、「[IoT Hub からのダイレクト メソッドの呼び出しについて](../../iot-hub/iot-hub-devguide-direct-methods.md)」を参照してください。

このトピックでは、これらのメソッドと規則について説明します。

## <a name="conventions"></a>規則

ダイレクト メソッドは、次の規則に基づいています。

1. ダイレクト メソッドには、MAJOR.MINOR 形式で指定されたバージョンがあります (次の例を参照)。

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. 特定のバージョンの Live Video Analytics on IoT Edge では、そのバージョンまでのすべてのダイレクト メソッドがサポートされます。 たとえば、モジュールのバージョンが 1.3 の場合は、バージョン 1.3、1.2、1.1、1.0 の各ダイレクト メソッドがサポートされます。
3. すべてのダイレクト メソッドは同期的です。
4. エラーの結果は OData のエラー スキーマに従います。
5. 名前は、次の制約に従う必要があります。
    
    * 英数字とダッシュのみ。ただし、先頭と末尾にはダッシュを使用できません
    * スペースは使用できません
    * 最大 32 文字

### <a name="example"></a>例

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>最上位レベルのエラー コード     

|Status |コード   |Message|
|---|---|---|
|400|   BadRequest| Request is not valid (要求は無効です)|
|400|   InvalidResource|    リソースが無効です|
|400|   InvalidVersion| API のバージョンが無効です|
|402|   ConnectivityRequired    |エッジ モジュールが適切に機能するには、クラウド接続が必要です。|
|404|   NotFound    |リソースが見つかりませんでした|
|409|   Conflict|   操作が競合しています|
|500|   InternalServerError|    内部サーバー エラー|
|503|   ServerBusy| サーバー ビジー|

### <a name="detailed-error-codes"></a>詳細なエラー コード

グラフ モジュールの検証などの詳細な検証エラーは、エラーの詳細として追加されます。

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|Status|    詳細コード   |説明|
|---|---|---|
|400|   GraphValidationError|   サイクルやパーティション分割などの一般的なグラフ エラー。|
|400|   ModuleValidationError|  モジュール固有の検証エラー。|
|409|   GraphTopologyInUse| グラフ トポロジは、1 つまたは複数のグラフ インスタンスによってまだ参照されています。|
|409|   OperationNotAllowedInState| 要求された操作は、現在の状態では実行できません。|
|409|   ResourceValidationError|    参照されたリソース (例: asset) は、有効な状態ではありません。|

## <a name="details"></a>詳細  

### <a name="graphtopologyget"></a>GraphTopologyGet

単一のグラフ トポロジを取得します。

#### <a name="request"></a>Request

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Response

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| エンティティが見つかりました | 200 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| エンティティが見つかりません | 404 |  |
| 一般的なサーバー エラー | 500 番台 |  |

### <a name="graphtopologyset"></a>GraphTopologySet

指定した名前を持つ既存のトポロジが存在しない場合は 1 つのトポロジを作成し、同じ名前の既存のトポロジがある場合はそれを更新します。

重要な点:

* トポロジは、それを参照しているグラフがない場合は、自由に更新できます。
* 次の場合に限り、参照しているすべてのグラフを非アクティブ化すると、トポロジを自由に更新できます。

    * 新しく追加されるパラメーターが既定値である
    * 削除されるパラメーターがどのグラフによっても参照されていない
* アクティブなグラフがある場合は、トポロジを更新できません

#### <a name="request"></a>Request

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>Response

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>状態コード

|条件  |status code    |詳細エラー コード|
|---|---|---|
既存のエンティティが更新されました |200|   該当なし|
新しいエンティティが作成されました  |201|   該当なし|
一般的なユーザー エラー |400 番台  ||
グラフ検証エラー |400    |GraphValidationError|
モジュール検証エラー|   400 |ModuleValidationError|
一般的なサーバー エラー   |500 番台  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

1 つのグラフ トポロジを削除します。

#### <a name="request"></a>Request

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Response

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| エンティティが削除されました | 200 | 該当なし |
| エンティティが見つかりません | 204 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| グラフ トポロジは、1 つまたは複数のグラフ インスタンスによって参照されています | 409 | GraphTopologyInUse |
| 一般的なサーバー エラー | 500 番台 |  |

### <a name="graphtopologylist"></a>GraphTopologyList

フィルター条件に一致するすべてのグラフ トポロジのリストを取得します。

#### <a name="request"></a>Request

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Response

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>フィルターのサポート

|操作 |フィールド    |オペレーター|
|---|---|---|
|$orderby|name  |asc|


#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| Success | 200 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| 一般的なサーバー エラー | 500 番台 |  |

### <a name="graphinstanceget"></a>GraphInstanceGet

1 つのグラフ インスタンスを取得します。

#### <a name="request"></a>Request

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Response

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| エンティティが見つかりました | 200 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| エンティティが見つかりません | 404 |  |
| 一般的なサーバー エラー | 500 番台 |  |

### <a name="graphinstanceset"></a>GraphInstanceSet

指定した名前を持つ既存のグラフ インスタンスが存在しない場合は 1 つのグラフ インスタンスを作成し、同じ名前の既存のグラフ インスタンスがある場合はそれを更新します。

重要な点:

* グラフ インスタンスは、"非アクティブ化" 状態のときは自由に更新できます。

    * グラフは、更新されるたびに再検証されます。
* グラフ インスタンスの更新は、グラフが "非アクティブ" 状態でないときは部分的に制限されます。
* グラフがアクティブ状態のときは、グラフ インスタンスを更新できません。

#### <a name="request"></a>Request

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>Response

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| 既存のエンティティが更新されました | 200 | 該当なし |
| 新しいエンティティが作成されました | 201 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| グラフ検証エラー | 400 | GraphValidationError |
| モジュール検証エラー | 400 | ModuleValidationError |
| リソース検証エラー | 409 | ResourceValidationError |
| 一般的なサーバー エラー | 500 番台 |  |  |

### <a name="graphinstancedelete"></a>GraphInstanceDelete

1 つのグラフ インスタンスを削除します。

重要な点:

* 削除できるのは、非アクティブなグラフだけです。

#### <a name="request"></a>Request

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Response

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| グラフが正常に削除されました | 200 | 該当なし |
| グラフが見つかりません | 204 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| グラフが "停止" 状態ではありません | 409 | OperationNotAllowedInState |
| 一般的なサーバー エラー | 500 番台 |  |

### <a name="graphinstancelist"></a>GraphInstanceList

これは GraphTopologyList に似ています。 グラフ インスタンスを列挙するために使用できます。
フィルター条件に一致するすべてのグラフ インスタンスのリストを取得します。

#### <a name="request"></a>Request

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Response

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>フィルターのサポート

|操作  |   フィールド|   オペレーター|
|---|---|---|
|$orderby|  name|   asc|

#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| Success | 200 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| 一般的なサーバー エラー | 500 番台 |  |

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

1 つのグラフ インスタンスをアクティブにします。 

重要な点

* メソッドからは、グラフがアクティブになったときにだけ戻ります 
* アクティブ化が行われている間、グラフは "アクティブ化中" 状態とみなされます。

    * グラフのリストと取得の操作では、適切な状態のグラフが返されます。
* べき等性:

    * "アクティブ化中" 状態のグラフを開始すると、グラフが非アクティブ化された場合と同じように動作します (つまり、グラフがアクティブになるまで呼び出しはブロックされます)
    * "アクティブ" 状態のグラフをアクティブ化すると、すぐに正常に戻ります。

#### <a name="request"></a>Request

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Response

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| グラフが正常にアクティブ化されました | 200 | 該当なし |
| 新しいエンティティが作成されました | 201 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| モジュール検証エラー | 400 | ModuleValidationError |
| リソース検証エラー | 409 | ResourceValidationError |
| グラフが非アクティブ化中状態です | 409 | OperationNotAllowedInState |
| 一般的なサーバー エラー | 500 番台 |  |

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

1 つのグラフ インスタンスを非アクティブにします。 グラフを非アクティブ化すると、メディア処理が正常に非アクティブ化され、エッジに一時的に格納されたイベントとメディアがある場合は、すべてがクラウドに確実にコミットされます。

重要な点:

* メソッドからは、グラフが非アクティブになったときにだけ戻ります
* 非アクティブ化が行われている間、グラフは "非アクティブ化中" 状態とみなされます。

    * グラフのリストと取得の操作では、適切な状態のグラフが返されます。
    * 停止は、すべてのメディアがクラウドにアップロードされたときにだけ完了します。
* べき等性:

    * "非アクティブ化中" 状態のグラフを非アクティブ化すると、グラフが非アクティブ化された場合と同じように動作します (つまり、グラフがアクティブになるまで呼び出しはブロックされます)
    * "非アクティブ" 状態のグラフを非アクティブ化すると、すぐに正常に戻ります。

#### <a name="request"></a>Request

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Response

```
{
    "status": 200,
    "payload": null
}
```

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| グラフが正常にアクティブ化されました | 200 | 該当なし |
| 新しいエンティティが作成されました | 201 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| グラフがアクティブ化中状態です | 409 | OperationNotAllowedInState |
| 一般的なサーバー エラー | 500 番台 |  |

## <a name="next-steps"></a>次のステップ

[モジュール ツインの構成スキーマ](module-twin-configuration-schema.md)
