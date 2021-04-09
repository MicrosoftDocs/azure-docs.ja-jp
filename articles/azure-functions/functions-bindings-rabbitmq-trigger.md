---
title: Azure Functions の RabbitMQ トリガー
description: RabbitMQ メッセージの作成時に Azure 関数を実行する方法について説明します。
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: be3c5bc2d178171aaebd322e13b23b3a6f79c442
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388993"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>Azure Functions の RabbitMQ トリガーの概要

> [!NOTE]
> RabbitMQ バインドは、**Premium および Dedicated** プランでのみ完全にサポートされています。 従量課金はサポートされていません。

RabbitMQ キューからのメッセージに応答するには、RabbitMQ トリガーを使用します。

セットアップと構成の詳細については、[概要](functions-bindings-rabbitmq.md)に関する記事を参照してください。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、RabbitMQ メッセージを [RabbitMQ イベント](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)として読み取り、ログに記録する [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

次の例は、メッセージを POCO として読み取る方法を示しています。

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }

    public class RabbitMQTriggerCSharp{
        [FunctionName("RabbitMQTriggerCSharp")]
        public static void RabbitMQTrigger_BasicDeliverEventArgs(
            [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass pocObj,
            ILogger logger
            )
        {
            logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {pocObj}");
        }
    }
}
```

JSON オブジェクトと同様に、メッセージが C# オブジェクトとして適切に書式設定されていない場合、エラーが発生します。 その場合は、変数 pocObj にバインドされ、そこから必要なものに対して使用できます。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの RabbitMQ トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、RabbitMQ メッセージを読み取り、ログに記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

C# スクリプト コードを次に示します。

```C#
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの RabbitMQ トリガー バインドと、そのバインドが使用される [JavaScript スクリプト関数](functions-reference-node.md)を示しています。 この関数は、RabbitMQ メッセージを読み取り、ログに記録します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

JavaScript スクリプト コードを次に示します。

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

次の例では、トリガーを使用して RabbitMQ キュー メッセージを読み取る方法を示します。

RabbitMQ のバインドは *function.json* で定義され、そこで *type* は `RabbitMQTrigger` に設定されます。

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

次の Java 関数は、[Java RabbitMQ type](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) からの `@RabbitMQTrigger` 注釈を使用して、RabbitMQ キュー トリガーの構成を記述します。 この関数は、キューにあるメッセージを取得し、ログに追加します。

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) 属性を使用します。

メソッド シグネチャでの `RabbitMQTrigger` 属性を次に示します。

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

完全な例については、C# の[例](#example)を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`RabbitMQTrigger` 注釈を使用すると、RabbitMQ メッセージの作成時に実行される関数を作成できます。 使用可能な構成オプションには、キュー名と接続文字列名があります。 パラメーターの詳細については、[RabbitMQTrigger Java 注釈](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java)に関するページを参照してください。

詳細については、トリガーの[例](#example)を参照してください。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `RabbitMQTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "RabbitMQTrigger" に設定する必要があります。|
|**direction** | 該当なし | "in" に設定する必要があります。|
|**name** | 該当なし | 関数コード内のキューを表す変数の名前。 |
|**queueName**|**QueueName**| メッセージを受信するキューの名前です。 |
|**hostName**|**HostName**|(ConnectStringSetting を使用する場合は無視されます) <br>キューのホスト名 (例:10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ConnectionStringSetting を使用する場合は無視されます) <br>キューにアクセスするためのユーザー名を含むアプリ設定の名前。 例: UserNameSetting: "%< UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|(ConnectionStringSetting を使用する場合は無視されます) <br>キューにアクセスするためのパスワードを含むアプリ設定の名前。 例: PasswordSetting: "%< PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|RabbitMQ メッセージ キュー接続文字列を含むアプリ設定の名前。 接続文字列は、local.settings.json のアプリ設定ではなく、直接指定した場合、トリガーは機能しないことに注意してください。 (例:*function.json* の場合: connectionStringSetting: "rabbitMQConnection" <br> *local.settings.json* の場合: "rabbitMQConnection" : "< ActualConnectionstring >")|
|**port**|**[ポート]**|(ConnectionStringSetting を使用する場合は無視されます) 使用されるポートを取得または設定します。 既定値は 0 です。これは rabbitmq クライアントの既定のポート設定を指します (5672)。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

既定のメッセージの種類は [RabbitMQ Event](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html) であり、RabbitMQ Event の `Body` プロパティは次の一覧に示されている型として読み取ることができます。

* `An object serializable as JSON` - メッセージが有効な JSON 文字列として配信されている場合。
* `string`
* `byte[]`
* `POCO` - メッセージが C# オブジェクトとして書式設定されている場合。 完全な例については、C# の[例](#example)を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

既定のメッセージの種類は [RabbitMQ Event](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html) であり、RabbitMQ Event の `Body` プロパティは次の一覧に示されている型として読み取ることができます。

* `An object serializable as JSON` - メッセージが有効な JSON 文字列として配信されている場合。
* `string`
* `byte[]`
* `POCO` - メッセージが C# オブジェクトとして書式設定されている場合。 完全な例については、C# スクリプトの[例](#example)を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

キュー メッセージは、context.bindings.<NAME> を介して使用できます。 ここでの <NAME> は、function.json で定義されている名前と一致します。 ペイロードが JSON の場合、値はオブジェクトに逆シリアル化されます。

# <a name="python"></a>[Python](#tab/python)

Python の[例](#example)を参照してください。

# <a name="java"></a>[Java](#tab/java)

Java の[属性と注釈](#attributes-and-annotations)に関するページを参照してください。

---

## <a name="dead-letter-queues"></a>配信不能キュー
配信不能キューと交換は、RabbitMQ トリガーから制御または構成することはできません。  配信不能キューを使用するには、RabbitMQ のトリガーによって使用されるキューを事前に構成します。 [RabbitMQ のドキュメント](https://www.rabbitmq.com/dlx.html)を参照してください。

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x 以降でこのバインドに使用可能なグローバル構成設定について説明します。 次の *host.json* ファイルの例には、このバインドの設定のみが含まれています。 グローバル構成設定の詳細については、[Azure Functions の host.json のリファレンス](functions-host-json.md)を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|prefetchCount|30|メッセージの受信者が同時に要求およびキャッシュできるメッセージ数を取得または設定します。|
|queueName|該当なし| メッセージを受信するキューの名前です。|
|connectionString|該当なし|RabbitMQ メッセージ キューの接続文字列。 接続文字列は、アプリ設定ではなく、ここで直接指定されることに注意してください。|
|port|0|(connectionString を使用する場合は無視されます) 使用されるポートを取得または設定します。 既定値は 0 です。これは rabbitmq クライアントの既定のポート設定を指します (5672)。|

## <a name="local-testing"></a>ローカル テスト

> [!NOTE]
> connectionString は、"hostName"、"userName"、および "password" よりも優先されます。 これらがすべて設定されている場合、connectionString によって他の 2 つがオーバーライドされます。

接続文字列を使用せずにローカルでテストする場合は、該当する場合、*host.json* の "rabbitMQ" セクションで、"hostName" 設定と "userName" および "password" を設定する必要があります。

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|hostName|該当なし|(connectionString を使用する場合は無視されます) <br>キューのホスト名 (例:10.26.45.210)|
|userName|該当なし|(connectionString を使用する場合は無視されます) <br>キューにアクセスするための名前 |
|password|該当なし|(connectionString を使用する場合は無視されます) <br>キューにアクセスするためのパスワード|


## <a name="enable-runtime-scaling"></a>ランタイム スケールを有効にする

RabbitMQ トリガーを複数のインスタンスにスケールアウトするには、**ランタイム スケールの監視** 設定を有効にする必要があります。 

ポータルでは、この設定はご利用の関数アプリの **[構成]**  >  **[関数のランタイム設定]** の下で見つけることができます。

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

CLI では、次のコマンドを使用して、**ランタイム スケールの監視** を有効にすることができます。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

## <a name="monitoring-rabbitmq-endpoint"></a>RabbitMQ エンドポイントの監視
特定の RabbitMQ エンドポイントのキューと交換を監視するには、次のようにします。

* [RabbitMQ 管理プラグイン](https://www.rabbitmq.com/management.html)を有効にする
* http://{node-hostname}:15672 にアクセスし、ユーザー名とパスワードを使用してログインします。

## <a name="next-steps"></a>次のステップ

- [Azure Functions から RabbitMQ メッセージを送信する (出力バインド)](./functions-bindings-rabbitmq-output.md)
