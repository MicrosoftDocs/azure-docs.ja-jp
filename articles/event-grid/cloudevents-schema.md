---
title: CloudEvents スキーマ内のイベントで Azure Event Grid を使用する
description: CloudEvents スキーマを Azure Event Grid 内のイベント用に使用する方法について説明します。 サービスでは、CloudEvents の JSON 実装でのイベントがサポートされています。
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 484f118791d57c082a9f4383b1af4a22c04849c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737904"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Event Grid に CloudEvents v1.0 スキーマを使用する
Azure Event Grid は、[既定のイベント スキーマ](event-schema.md)に加え、[CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) および [HTTP プロトコル バインディング](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)の JSON 実装のイベントをネイティブでサポートします。 [CloudEvents](https://cloudevents.io/) は、イベント データを記述するための[オープンな仕様](https://github.com/cloudevents/spec/blob/v1.0/spec.md)です。

CloudEvents を使用すると、クラウド ベースのイベントを発行したり使用したりするための共通のイベント スキーマを提供し、相互運用性を簡略化することができます。 このスキーマを使用すれば、ツールを統一化したり、イベントのルーティングや処理方法を標準化したり、外部のイベント スキーマを共通の方法で逆シリアル化することができます。 共通のスキーマを使用することで、プラットフォーム間での作業をより簡単に統合できます。

CloudEvents は、[Cloud Native Computing Foundation](https://www.cncf.io/) を通じ、複数の[コラボレーター](https://github.com/cloudevents/spec/blob/master/community/contributors.md) (マイクロソフトを含む) によって構築されています。 現在、バージョン 1.0 が提供されています。

この記事では、Event Grid で CloudEvents スキーマを使用する方法について説明します。

## <a name="cloudevent-schema"></a>CloudEvent スキーマ

次に示すのは、CloudEvents 形式の Azure Blob Storage イベントの例です。

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

使用できるフィールド、その種類、定義の詳細については、[CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes) に関するページを参照してください。

CloudEvents スキーマと Event Grid スキーマで配信されるイベントのヘッダー値は同じです (`content-type` を除く)。 CloudEvents スキーマの場合、そのヘッダー値は `"content-type":"application/cloudevents+json; charset=utf-8"` です。 Event Grid スキーマの場合、そのヘッダー値は `"content-type":"application/json; charset=utf-8"` です。

## <a name="configure-event-grid-for-cloudevents"></a>Event Grid を CloudEvents 用に構成する

Event Grid は、CloudEvents スキーマ内のイベントの入力と出力の両方に使用できます。 次の表は、使用できる変換を示しています。

 Event Grid リソース | 入力スキーマ       | 配信スキーマ
|---------------------|-------------------|---------------------
| システム トピック       | Event Grid スキーマ | Event Grid スキーマまたは CloudEvents スキーマ
| ユーザー トピック/ドメイン | Event Grid スキーマ | Event Grid スキーマまたは CloudEvents スキーマ
| ユーザー トピック/ドメイン | CloudEvents スキーマ | CloudEvents スキーマ
| ユーザー トピック/ドメイン | カスタム スキーマ     | カスタム スキーマ、Event Grid スキーマ、または CloudEvents スキーマ
| PartnerTopics       | CloudEvents スキーマ | CloudEvents スキーマ

いずれのイベント スキーマについても、Event Grid では、Event Grid トピックへの発行時やイベント サブスクリプションの作成時に検証が必要です。

詳細については、「[Event Grid security and authentication](security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。

### <a name="input-schema"></a>入力スキーマ

カスタム トピックを作成するときは、カスタム トピックに対する入力スキーマを設定します。

Azure CLI では、次を使用します。

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

PowerShell では、次を使用します。

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>出力スキーマ

イベント サブスクリプションを作成するときは、出力スキーマを設定します。

Azure CLI では、次を使用します。

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

PowerShell では、次を使用します。
```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 現時点では、CloudEvents スキーマでイベントを配信する際に、Azure Functions アプリの Event Grid トリガーを使用することはできません。 HTTP トリガーを使用します。 CloudEvents スキーマでイベントを受け取る HTTP トリガーを実装する例については、[Azure Functions での CloudEvents の使用](#azure-functions)に関する記事を参照してください。

## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v1.0 を使用したエンドポイントの検証

Event Grid を既に使い慣れている場合、不正使用を防ぐためのエンドポイント検証ハンドシェイクをご存じかもしれません。 CloudEvents v1.0 を使用すると、HTTP OPTIONS メソッドによって、独自の[不正使用防止のセマンティクス](webhook-event-delivery.md)が実装されます。 詳細については、「[イベント配信用の HTTP 1.1 Web Hooks - バージョン 1.0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)」を参照してください。 出力に CloudEvents スキーマを使用すると、Event Grid では、Event Grid の検証イベント メカニズムではなく CloudEvents v1.0 の不正使用防止が使用されます。

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Azure Functions と共に使用する

[Azure Functions Event Grid バインド](../azure-functions/functions-bindings-event-grid.md)では CloudEvents がネイティブにサポートされていないため、CloudEvents メッセージの読み取りには HTTP によってトリガーされる関数が使用されます。 CloudEvents の読み取りに HTTP トリガーを使用する場合は、Event Grid トリガーによって自動的に行われる処理のコードを記述する必要があります。

* [サブスクリプション検証要求](../event-grid/webhook-event-delivery.md)に検証応答を送信します
* 要求本文に含まれるイベント配列の要素ごとに、関数を 1 回呼び出します

関数をローカルに呼び出す場合、または Azure 内で実行するときに使用する URL については、[HTTP トリガーのバインドに関するリファレンス ドキュメント](../azure-functions/functions-bindings-http-webhook.md)をご覧ください。

次に示す HTTP トリガーの C# コード サンプルでは、Event Grid トリガーの動作をシミュレートします。 CloudEvents スキーマで配信されたイベントでは、この例を使用します。

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

次に示す HTTP トリガーの JavaScript コード サンプルでは、Event Grid トリガーの動作をシミュレートします。 CloudEvents スキーマで配信されたイベントでは、この例を使用します。

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>次のステップ

* イベント配信の監視について詳しくは、「[Event Grid メッセージ配信の監視](monitor-event-delivery.md)」をご覧ください。
* CloudEvents をテストし、問題についてコメントし、改良に[協力](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md)することをお勧めします。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
