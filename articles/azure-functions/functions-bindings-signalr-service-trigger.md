---
title: Azure Functions SignalR Service トリガー バインド
description: Azure Functions から Azure SignalR Service メッセージを送信する方法について説明します。
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: 2482a26987ec142880acc51bf470d844655b6e3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97763517"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Azure Functions における SignalR Service のトリガー バインド

*SignalR* トリガー バインドを使用して、Azure SignalR Service から送信されたメッセージに応答します。 関数がトリガーされると、関数に渡されるメッセージは JSON オブジェクトとして解析されます。

SignalR Service サーバーレス モードでは、SignalR Service は[アップストリーム](../azure-signalr/concept-upstream.md)機能を使用して、クライアントから関数アプリにメッセージを送信します。 また関数アプリでは、SignalR Service トリガー バインドを使用してこれらのメッセージを処理します。 一般的なアーキテクチャを次に示します。:::image type="content" source="media/functions-bindings-signalr-service/signalr-trigger.png" alt-text="SignalR トリガーのアーキテクチャ":::

セットアップと構成の詳細については、[概要](functions-bindings-signalr-service.md)に関する記事を参照してください。

## <a name="example"></a>例

次の例は、トリガー バインドを使用してメッセージを受信し、メッセージをログに記録する関数を示しています。

# <a name="c"></a>[C#](#tab/csharp)

C# 用 SignalR Service トリガー バインドには、2 つのプログラミング モデルがあります。 クラス ベースのモデルと従来のモデルです。 クラス ベースのモデルは、一貫性のある SignalR サーバー側プログラミング エクスペリエンスを提供します。 従来のモデルは柔軟性がより高く、他の関数バインドと似ています。

### <a name="with-class-based-model"></a>クラス ベースのモデルの使用

詳細については、[クラス ベースのモデル](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model)を参照してください。

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>従来のモデルの使用

従来のモデルは、C# で開発された Azure 関数の規則に従います。 これに慣れていない場合は、[ドキュメント](./functions-dotnet-class-library.md)を参照してください。

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>属性 `[SignalRParameter]` を使用した `ParameterNames` の簡略化

`ParameterNames` の使用は少し面倒であるため、同じ目的を達成するために `SignalRParameter` が提供されています。

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

*function.json* ファイルのバインド データを次に示します。

function.json の例:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Python コードを次に示します。

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>構成

### <a name="signalrtrigger"></a>SignalRTrigger

次の表は、*function.json* ファイルと `SignalRTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**| 該当なし | `SignalRTrigger` に設定する必要があります。|
|**direction**| 該当なし | `in` に設定する必要があります。|
|**name**| 該当なし | トリガー呼び出しコンテキスト オブジェクトの関数コードで使用される変数名。 |
|**hubName**|**HubName**| この値は、トリガーされる関数の SignalR ハブの名前に設定する必要があります。|
|**category**|**カテゴリ**| この値は、トリガーされる関数のメッセージのCategoryとして設定する必要があります。 カテゴリには次のいずれかの値を指定することができます。 <ul><li>**connections**:*connected* および *disconnected* イベントを含む</li><li>**messages**:*connections* カテゴリ以外の他のすべてのイベントを含む</li></ul> |
|**event**|**Event**| この値は、トリガーされる関数のメッセージのイベントとして設定する必要があります。 *messages* カテゴリの場合、イベントは「[呼び出しメッセージ](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)」でクライアントが送信する *target* です。 *connections* カテゴリの場合、*connected* および *disconnected* のみが使用されます。 |
|**parameterNames**|**ParameterNames**| (省略可能) パラメーターにバインドする名前のリスト。 |
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 接続文字列を含むアプリ設定の名前 (既定値は "AzureSignalRConnectionString") |

## <a name="payload"></a>ペイロード

トリガーの入力型は、`InvocationContext` またはカスタム型のいずれかとして宣言されています。 `InvocationContext` を選択した場合は、要求のコンテンツへのフル アクセスが取得されます。 カスタム型 の場合、ランタイムは JSON 要求本文を解析して、オブジェクトのプロパティを設定しようとします。

### <a name="invocationcontext"></a>InvocationContext

InvocationContext には、SignalR サービスから送信されるメッセージのすべてのコンテンツが含まれています。

|InvocationContext のプロパティ | 説明|
|------------------------------|------------|
|引数| *messages* カテゴリで使用可能。 「[呼び出しメッセージ](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)」の *arguments* が格納されています|
|Error| *disconnected* イベントで使用可能。 接続がエラーなしで閉じられた場合、またはエラー メッセージが含まれている場合は、空になる場合があります。|
|ハブ| メッセージが属しているハブの名前。|
|カテゴリ| メッセージのカテゴリ。|
|Event| メッセージのイベント。|
|ConnectionId| メッセージを送信するクライアントの接続 ID。|
|UserId| メッセージを送信するクライアントのユーザー ID。|
|ヘッダー| 要求のヘッダー。|
|クエリ| クライアントがサービスに接続するときの要求のクエリ。|
|Claims| クライアントのクレーム。|

## <a name="using-parameternames"></a>`ParameterNames` の使用

`SignalRTrigger` のプロパティ `ParameterNames` を使用すると、呼び出しメッセージの引数を関数のパラメーターにバインドできます。 定義した名前は、他のバインドの[バインド式](../azure-functions/functions-bindings-expressions-patterns.md)の一部として、またはコードのパラメーターとして使用できます。 これにより、`InvocationContext` の引数へのアクセスがより便利になります。

たとえば、JavaScript SignalR クライアントが 2 つの引数 (`message1`、`message2`) を使用して Azure 関数でメソッド `broadcast` を呼び出そうとしているとします。

```javascript
await connection.invoke("broadcast", message1, message2);
```

`parameterNames` を設定すると、定義した名前が、クライアント側で送信される引数それぞれに対応します。 

```cs
[SignalRTrigger(parameterNames: new string[] {"arg1, arg2"})]
```

この場合、`arg1` に `message1` のコンテンツが含まれ、`arg2` に `message2` のコンテンツが含まれます。


### <a name="remarks"></a>解説

パラメーター バインドの場合、順序は重要です。 `ParameterNames` を使用している場合、`ParameterNames` の順序は、クライアントで呼び出した引数の順序と一致します。 C# で属性 `[SignalRParameter]` を使用している場合、Azure 関数メソッドの引数の順序は、クライアントの引数の順序と一致します。

`ParameterNames` および属性 `[SignalRParameter]` を同時に使用することは **できません**。使用すると、例外が発生します。

## <a name="signalr-service-integration"></a>SignalR Service の統合

SignalR Service トリガー バインドを使用している場合、SignalR サービスには関数アプリにアクセスするための URL が必要です。 URL は、SignalR Service 側の **アップストリームの設定** で構成する必要があります。 

:::image type="content" source="../azure-signalr/media/concept-upstream/upstream-portal.png" alt-text="アップストリーム ポータル":::

SignalR Service トリガーを使用する場合、URL は単純で、次に示す形式にすることができます。

```http
<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>
```

`Function_App_URL` は関数アプリの [概要] ページで確認でき、`API_KEY` は Azure 関数によって生成されます。 関数アプリの **[アプリ キー]** ブレードで `signalr_extension` から `API_KEY` を取得できます。
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API キー":::

1 つの SignalR サービスで複数の関数アプリを一緒に使用する場合は、アップストリームで複雑なルーティング規則をサポートすることもできます。 詳細については「[アップストリームの設定](../azure-signalr/concept-upstream.md)」をご覧ください。

## <a name="step-by-step-sample"></a>ステップ バイ ステップ サンプル

GitHub のサンプルに従って、SignalR Service トリガー バインドとアップストリーム機能を使用して関数アプリに関するチャット ルームをデプロイできます。[双方向チャット ルームのサンプル](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)

## <a name="next-steps"></a>次のステップ

* [Azure SignalR Service を使用した Azure Functions の開発と構成](../azure-signalr/signalr-concept-serverless-development-config.md)
* [SignalR Service トリガー バインドのサンプル](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
