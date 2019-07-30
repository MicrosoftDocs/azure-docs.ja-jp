---
title: Azure Functions の Event Grid トリガー
description: Azure Functions で Event Grid イベントを処理する方法を説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: f48eced2ebcc4ad92c5124194ed2e2df92f64f11
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480656"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Azure Functions の Event Grid トリガー

この記事では、Azure Functions で [Event Grid](../event-grid/overview.md) イベントを処理する方法を説明します。

Event Grid は、"*パブリッシャー*" 内で発生したイベントについてユーザーに通知する HTTP 要求を送信する Azure サービスです。 パブリッシャーは、イベントを生成するサービスまたはリソースです。 たとえば、Azure Blob Storage アカウントはパブリッシャーであり、[BLOB のアップロードまたは削除がイベント](../storage/blobs/storage-blob-event-overview.md)です。 一部の [Azure サービスには、Event Grid にイベントを発行するサポートが組み込まれています](../event-grid/overview.md#event-sources)。

イベント "*ハンドラー*" は、イベントを受信して処理します。 Azure Functions は、[Event Grid イベントを処理する組み込みサポートを備えている Azure サービス](../event-grid/overview.md#event-handlers)の 1 つです。 この記事では、Event Grid からイベントを受信したときに、Event Grid トリガーを使って関数を呼び出す方法を説明します。

好みに応じて、Event Grid イベントの処理に HTTP トリガーを使うこともできます。後の「[Event Grid トリガーとして HTTP トリガーを使用する](#use-an-http-trigger-as-an-event-grid-trigger)」をご覧ください。 現時点では、[CloudEvents スキーマ](../event-grid/cloudevents-schema.md)でイベントを配信する際に、Azure Functions アプリの Event Grid トリガーを使用することはできません。 代わりに、HTTP トリガーを使用してください。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

Event Grid トリガーは、[Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet パッケージ、バージョン 2.x で提供されます。 パッケージのソース コードは、[azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Event Grid トリガーは、[Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet パッケージ、バージョン 1.x で提供されます。 パッケージのソース コードは、[azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) GitHub リポジトリにあります。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>例

Event Grid トリガーの言語固有の例をご覧ください。

* C#
* [C# スクリプト (.csx)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

HTTP トリガーの例については、後の「[HTTP トリガーを使用する方法](#use-an-http-trigger-as-an-event-grid-trigger)」をご覧ください。

### <a name="c-2x"></a>C# (2.x)

次の例は、`EventGridEvent` にバインドする Functions 2.x の [C# 関数](functions-dotnet-class-library.md)を示したものです。

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

詳しくは、「パッケージ」、「[属性](#attributes)」、「[構成](#configuration)」、および「[使用法](#usage)」をご覧ください。

### <a name="c-version-1x"></a>C# (バージョン 1.x)

次の例は、`JObject` にバインドする Functions 1.x の [C# 関数](functions-dotnet-class-library.md)を示したものです。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

### <a name="c-script-example"></a>C# スクリプトの例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-script-version-2x"></a>C# スクリプト (バージョン 2.x)

`EventGridEvent` にバインドする Functions 2.x の C# スクリプト コードを次に示します。

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

詳しくは、「パッケージ」、「[属性](#attributes)」、「[構成](#configuration)」、および「[使用法](#usage)」をご覧ください。

#### <a name="c-script-version-1x"></a>C# スクリプト (バージョン 1.x)

`JObject` にバインドする Functions 1.x の C# スクリプト コードを次に示します。

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

### <a name="javascript-example"></a>JavaScript の例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドを使用する [JavaScript 関数](functions-reference-node.md)を示しています。

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

### <a name="python-example"></a>Python の例

次の例は、*function.json* ファイルのトリガー バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。

*function.json* ファイルのバインディング データを次に示します。

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>トリガー - Java の例

このセクションには、次の例が含まれています。

* [Event Grid グリッド トリガー、文字列パラメーター](#event-grid-trigger-string-parameter-java)
* [Event Grid グリッド トリガー、POJO パラメーター](#event-grid-trigger-pojo-parameter-java)

次の例では、*function.json* ファイルのトリガー バインドとそのバインドを使用し、イベントを出力する [Java 関数](functions-reference-java.md)を示しています。最初にイベントとして ```String``` を受け取り、次に POJO を受け取ります。

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

#### <a name="event-grid-trigger-string-parameter-java"></a>Event Grid グリッド トリガー、文字列パラメーター (Java)

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: " + content);      
  }
```

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Event Grid グリッド トリガー、POJO パラメーター (Java)

この例では、次の POJO が使用されています。Event Grid イベントの最上位プロパティを表します。

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

受け取り時、イベントの JSON ペイロードが ```EventSchema``` POJO に逆シリアル化され、関数で使用されます。 それにより、関数はオブジェクト指向でイベントのプロパティにアクセスできます。

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が EventGrid に由来するパラメーター上で `EventGridTrigger` 注釈を使用します。 これらの注釈を使用したパラメーターによって、イベントを受信したときに関数が実行されます。  この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

## <a name="attributes"></a>属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) 属性を使用します。

メソッド シグネチャでの `EventGridTrigger` 属性を次に示します。

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

完全な例については、「C# の例」を参照してください。

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルで設定したバインド構成のプロパティを説明しています。 `EventGridTrigger` 属性で設定するコンストラクター パラメーターまたはプロパティはありません。

|function.json のプロパティ |説明|
|---------|---------|
| **type** | 必須 - `eventGridTrigger` に設定する必要があります。 |
| **direction** | 必須 - `in` に設定する必要があります。 |
| **name** | 必須 - イベント データを受信するパラメーターの、関数コードで使われている変数名。 |

## <a name="usage"></a>使用法

Azure Functions 1.x の C# および F# 関数については、Event Grid トリガーに次のパラメーター型を使用できます。

* `JObject`
* `string`

Azure Functions 2.x の C# および F# 関数については、Event Grid トリガーに次のパラメーター型を使用することもできます。

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- すべてのイベントの種類に共通するフィールドのプロパティを定義します。

> [!NOTE]
> Functions 1.x では、`Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` にバインドしようとした場合、コンパイラに「非推奨」メッセージが表示され、代わりに `Microsoft.Azure.EventGrid.Models.EventGridEvent` 使用するよう推奨されます。 新しい種類を使用するには、[Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet パッケージを参照し、`EventGridEvent` の種類名の先頭に `Microsoft.Azure.EventGrid.Models` を付けることによって完全修飾します。 C# スクリプト関数で NuGet パッケージを参照する方法については、「[NuGet パッケージを使用する](functions-reference-csharp.md#using-nuget-packages)」をご覧ください

JavaScript 関数では、*function.json* `name` プロパティによって指定されているパラメーターが、イベント オブジェクトへの参照を保持しています。

## <a name="event-schema"></a>イベント スキーマ

Event Grid イベントのデータは、HTTP 要求の本文内の JSON オブジェクトとして受信されます。 JSON は次の例のようになります。

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

示されている例は、1 要素の配列です。 Event Grid は常に配列を送信し、配列で複数のイベントを送信できます。 ランタイムは、配列の各要素に対して 1 回、関数を呼び出します。

イベント JSON データの最上位レベルのプロパティはすべてのイベントの種類で同じですが、`data` プロパティの内容は各イベントの種類に固有です。 この例では、BLOB ストレージ イベントのものです。

共通プロパティとイベント固有プロパティについて詳しくは、Event Grid のドキュメントの「[イベントのプロパティ](../event-grid/event-schema.md#event-properties)」をご覧ください。

`EventGridEvent` 型では、最上位レベルのプロパティのみが定義されています。`Data` プロパティは `JObject` です。

## <a name="create-a-subscription"></a>サブスクリプションの作成

Event Grid の HTTP 要求の受信を始めるには、関数を呼び出すエンドポイント URL を指定する Event Grid サブスクリプションを作成します。

### <a name="azure-portal"></a>Azure ポータル

Event Grid トリガーを使って Azure Portal で開発した関数の場合は、 **[vent Grid サブスクリプションの追加]** を選びます。

![ポータルでサブスクリプションを作成する](media/functions-bindings-event-grid/portal-sub-create.png)

このリンクを選ぶと、ポータルに **[イベント サブスクリプションの作成]** ページが表示され、エンドポイント URL があらかじめ設定されています。

![事前設定されたエンドポイント URL](media/functions-bindings-event-grid/endpoint-url.png)

Azure Portal を使ってサブスクリプションを作成する方法について詳しくは、Event Grid のドキュメントの「[カスタム イベントの作成 - Azure Portal](../event-grid/custom-event-quickstart-portal.md)」をご覧ください。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) を使ってサブスクリプションを作成するには、[az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) コマンドを使います。

このコマンドには、関数を呼び出すエンドポイント URL が必要です。 バージョン固有の URL パターンの例を次に示します。

#### <a name="version-2x-runtime"></a>バージョン 2.x ランタイム

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>バージョン 1.x ランタイム

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

システム キーは、Event Grid トリガー用のエンドポイント URL に含める必要のある承認キーです。 次のセクションでは、システム キーを取得する方法について説明します。

BLOB ストレージ アカウントをサブスクライブする例を次に示します (システム キーのプレースホルダーを含みます)。

#### <a name="version-2x-runtime"></a>バージョン 2.x ランタイム

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>バージョン 1.x ランタイム

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

サブスクリプションを作成する方法について詳しくは、[BLOB ストレージのクイック スタート](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account)または他の Event Grid クイック スタートのページをご覧ください。

### <a name="get-the-system-key"></a>システム キーを取得する

次の API (HTTP GET) を使って、システム キーを取得できます。

#### <a name="version-2x-runtime"></a>バージョン 2.x ランタイム

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>バージョン 1.x ランタイム

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

これは管理 API なので、お使いの関数アプリの[マスター キー](functions-bindings-http-webhook.md#authorization-keys)が必要です。 システム キー (Event Grid トリガー関数を呼び出す場合) とマスター キー (関数アプリで管理タスクを実行する場合) を混同しないでください。 Event Grid トピックをサブスクライブするときは、システム キーを使います。

システム キーを提供する応答の例を次に示します。

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

ポータルの **[Function App の設定]** タブから関数アプリのマスター キーを取得できます。

> [!IMPORTANT]
> マスター キーでは、関数アプリへの管理者アクセス権が提供されます。 このキーを第三者と共有したり、ネイティブ クライアント アプリケーションで配布したりしないでください。

詳しくは、HTTP トリガーのリファレンス記事の「[承認キー](functions-bindings-http-webhook.md#authorization-keys)」をご覧ください。

代わりに、HTTP PUT を送信して自分でキーの値を指定することもできます。

## <a name="local-testing-with-viewer-web-app"></a>ビューアー Web アプリでのローカル テスト

Event Grid トリガーをローカルにテストするには、クラウド内の送信元からローカル コンピューターに配信された Event Grid の HTTP 要求を取得する必要があります。 これを行う方法の 1 つは、オンラインで要求をキャプチャし、ローカル コンピューター上でそれを手動で再送信することです。

1. イベント メッセージをキャプチャする[ビューアー Web アプリを作成](#create-a-viewer-web-app)します。
1. ビューアー アプリにイベントを送信する [Event Grid サブスクリプションを作成](#create-an-event-grid-subscription)します。
1. [要求を生成](#generate-a-request)し、ビューアー アプリから要求本文をコピーします。
1. Event Grid トリガー関数の localhost URL に[要求を手動で投稿](#manually-post-the-request)します。

テストが完了したら、エンドポイントを更新することで、同じサブスクリプションを運用環境に使うことができます。 [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI コマンドを使います。

### <a name="create-a-viewer-web-app"></a>ビューアー Web アプリを作成する

イベント メッセージのキャプチャを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロイすることができます。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

**[Deploy to Azure]\(Azure にデプロイ\)** を選択して、ソリューションをサブスクリプションにデプロイします。 Azure portal で、パラメーターの値を指定します。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

サイトは表示されますが、イベントはまだ送信されていません。

![新しいサイトを表示する](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid のサブスクリプションを作成する

テストする種類の Event Grid サブスクリプションを作成し、イベント通知のエンドポイントとして Web アプリからの URL を指定します。 Web アプリのエンドポイントには、サフィックス `/api/updates/` が含まれている必要があります。 したがって、完全な URL は `https://<your-site-name>.azurewebsites.net/api/updates` となります。

Azure Portal を使ってサブスクリプションを作成する方法については、Event Grid のドキュメントの「[カスタム イベントの作成 - Azure Portal](../event-grid/custom-event-quickstart-portal.md)」を参照してください。

### <a name="generate-a-request"></a>要求を生成する

Web アプリ エンドポイントへの HTTP トラフィックを生成するイベントをトリガーします。  たとえば、BLOB ストレージ サブスクリプションを作成した場合は、BLOB をアップロードまたは削除します。 要求が Web アプリに表示されたら、要求本文をコピーします。

最初に、サブスクリプション検証要求を受信します。検証要求はすべて無視し、イベント要求をコピーします。

![Web アプリから要求本文をコピーする](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>要求を手動で投稿する

Event Grid 関数をローカルで実行します。

[Postman](https://www.getpostman.com/) や [curl](https://curl.haxx.se/docs/httpscripting.html) などのツールを使って、HTTP POST 要求を作成します。

* `Content-Type: application/json` ヘッダーを設定します。
* `aeg-event-type: Notification` ヘッダーを設定します。
* 要求本文に RequestBin のデータを貼り付けます。
* Event Grid トリガー関数の URL に投稿します。
  * 2\.x の場合は、以下のパターンを使用します。

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * 1\.x の場合は、以下を使用します。

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

`functionName` パラメーターには、`FunctionName` 属性で指定されている名前を指定する必要があります。

次のスクリーンショットでは、Postman のヘッダーと要求本文を示します。

![Postman でのヘッダー](media/functions-bindings-event-grid/postman2.png)

![Postman での要求本文](media/functions-bindings-event-grid/postman.png)

Event Grid トリガー関数が実行されて、次の例のようなログが表示されます。

![Event Grid トリガー関数のログの例](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>ngrok でのローカル テスト

Event Grid トリガーをローカルにテストするもう 1 つの方法は、インターネットと開発用コンピューターの間の HTTP 接続を自動化することです。 [ngrok](https://ngrok.com/) などのツールを使ってこれを行うことができます。

1. [ngrok のエンドポイントを作成](#create-an-ngrok-endpoint)します。
1. [Event Grid トリガー関数を実行](#run-the-event-grid-trigger-function)します。
1. ngrok のエンドポイントにイベントを送信する [Event Grid サブスクリプションを作成](#create-a-subscription)します。
1. [イベントをトリガー](#trigger-an-event)します。

テストが完了したら、エンドポイントを更新することで、同じサブスクリプションを運用環境に使うことができます。 [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI コマンドを使います。

### <a name="create-an-ngrok-endpoint"></a>ngrok のエンドポイントを作成する

[ngrok](https://ngrok.com/) から *ngrok.exe* をダウンロードし、次のコマンドで実行します。

```
ngrok http -host-header=localhost 7071
```

-host-header パラメーターは、関数ランタイムは localhost で実行するときは localhost からの要求を期待するので必要です。 7071 は、ランタイムがローカルで実行するときの既定のポート番号です。

コマンドは、次のような出力を作成します。

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Event Grid サブスクリプションには、`https://{subdomain}.ngrok.io` という URL を使います。

### <a name="run-the-event-grid-trigger-function"></a>Event Grid トリガー関数を実行する

ngrok の URL は Event Grid によって特別に処理されないので、サブスクリプションが作成される時点で、関数がローカルで実行している必要があります。 実行していないと、検証応答が送信されず、サブスクリプションの作成が失敗します。

### <a name="create-a-subscription"></a>サブスクリプションの作成

テストする種類の Event Grid サブスクリプションを作成し、それに ngrok エンドポイントを提供します。

Functions 2.x に対して、次のようにこのエンドポイント パターンを使用します。

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Functions 1.x に対して、次のようにこのエンドポイント パターンを使用します。

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

`{FUNCTION_NAME}` パラメーターには、`FunctionName` 属性で指定されている名前を指定する必要があります。

Azure CLI を使う例を次に示します。

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

サブスクリプションを作成する方法については、前の「[サブスクリプションの作成](#create-a-subscription)」をご覧ください。

### <a name="trigger-an-event"></a>イベントをトリガーする

ngrok のエンドポイントへの HTTP トラフィックを生成するイベントをトリガーします。  たとえば、BLOB ストレージ サブスクリプションを作成した場合は、BLOB をアップロードまたは削除します。

Event Grid トリガー関数が実行されて、次の例のようなログが表示されます。

![Event Grid トリガー関数のログの例](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Event Grid トリガーとして HTTP トリガーを使用する

Event Grid イベントは HTTP 要求として受信されるので、Event Grid トリガーの代わりに HTTP トリガーを使ってイベントを処理できます。 これを行う理由の 1 つとして考えられるのは、関数を呼び出すエンドポイントの URL をより詳細に制御するためです。 もう 1 つの理由は、[CloudEvents スキーマ](../event-grid/cloudevents-schema.md)でイベントを受信する必要があるためです。 現時点では、Event Grid トリガーでは CloudEvents スキーマはサポートされていません。 このセクションの例では、Event Grid スキーマと CloudEvents スキーマの両方のソリューションを示します。

HTTP トリガーを使う場合は、Event Grid トリガーによって自動的に行われる処理のコードを記述する必要があります。

* [サブスクリプション検証要求](../event-grid/security-authentication.md#webhook-event-delivery)に検証応答を送信します。
* 要求本文に含まれるイベント配列の要素ごとに、関数を 1 回呼び出します。

関数をローカルに呼び出す場合、または Azure 内で実行するときに使う URL については、[HTTP トリガーのバインドに関するリファレンス ドキュメント](functions-bindings-http-webhook.md)をご覧ください

### <a name="event-grid-schema"></a>Event Grid スキーマ

次に示す HTTP トリガーの C# コード サンプルでは、Event Grid トリガーの動作をシミュレートします。 Event Grid スキーマで配信されたイベントでは、この例を使用します。

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.LogInformation("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.LogInformation($"Subject: {eventGridEvent.Subject}");
        log.LogInformation($"Time: {eventGridEvent.EventTime}");
        log.LogInformation($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

次に示す HTTP トリガーの JavaScript コード サンプルでは、Event Grid トリガーの動作をシミュレートします。 Event Grid スキーマで配信されたイベントでは、この例を使用します。

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = messages[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        // Event Grid schema delivers events in an array.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

イベント処理コードは、`messages` 配列のループ内を処理します。

### <a name="cloudevents-schema"></a>CloudEvents スキーマ

次に示す HTTP トリガーの C# コード サンプルでは、Event Grid トリガーの動作をシミュレートします。  CloudEvents スキーマで配信されたイベントでは、この例を使用します。

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

次に示す HTTP トリガーの JavaScript コード サンプルでは、Event Grid トリガーの動作をシミュレートします。 CloudEvents スキーマで配信されたイベントでは、この例を使用します。

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Event Grid の詳細を確認します](../event-grid/overview.md)
