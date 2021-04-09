---
title: Azure Event Grid スキーマへのカスタム フィールドのマップ
description: この記事では、イベント データが Event Grid スキーマと一致しない場合にカスタム スキーマを Azure Event Grid スキーマに変換する方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34381782c9337631b0aa04e47eb5897a8071139a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97109200"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Event Grid スキーマへのカスタム フィールドのマップ

イベント データと必要な [Event Grid スキーマ](event-schema.md)が一致しない場合でも、Event Grid を使用して、イベントをサブスクライバーにルーティングできます。 この記事では、カスタム スキーマを Event Grid スキーマにマップする方法について説明します。

## <a name="original-event-schema"></a>元のイベント スキーマ

次の形式でイベントを送信するアプリケーションがあるとします。

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

この形式は必要なスキーマと一致しませんが、Event Grid を使用して、フィールドをスキーマにマップできます。 つまり、元のスキーマの値を受け取ることができます。

## <a name="create-custom-topic-with-mapped-fields"></a>フィールドをマップしたカスタム トピックの作成

カスタム トピックを作成する際に、元のイベントのフィールドを Event Grid スキーマにマップする方法を指定します。 マッピングをカスタマイズする際に使用する値として、次の 3 つがあります。

* **--input-schema** の値では、スキーマのタイプを指定します。 使用可能なオプションは、CloudEvents スキーマ、カスタム イベント スキーマ、または Event Grid スキーマです。 既定値は、Event Grid スキーマです。 カスタム スキーマと Event Grid スキーマの間のカスタム マッピングを作成する場合は、カスタム イベント スキーマを使用します。 イベントが CloudEvents フォーマット内にある場合は、CloudEvents スキーマを使用します。

* **-mapping-default-values** プロパティでは、Event Grid スキーマ内のフィールドの既定値を指定します。 `subject`、`eventtype`、および `dataversion` の既定値を指定できます。 通常、これらの 3 つのフィールドのいずれかに対応するフィールドがカスタム スキーマに含まれていない場合に、このパラメーターを使用します。 たとえば、dataversion が常に **1.0** に設定されるように指定できます。

* **-mapping-fields** の値により、スキーマのフィールドが Event Grid スキーマにマッピングされます。 値はスペースで区切ったキー/値のペアで指定します。 キー名には、Event Grid フィールドの名前を使用します。 値には、カスタム フィールドの名前を使用します。 `id`、`topic`、`eventtime`、`subject`、`eventtype`、および `dataversion` ではキー名を使用できます。

Azure CLI でカスタム トピックを作成するには、次を使用します。

```azurecli-interactive
az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

PowerShell では、次を使用します。

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Event Grid トピックのサブスクライブ

カスタム トピックをサブスクライブする場合は、イベントの受信に使用するスキーマを指定します。 CloudEvents スキーマ、カスタム イベント スキーマ、または Event Grid スキーマのいずれかを指定します。 既定値は、Event Grid スキーマです。

次の例では、Event Grid トピックをサブスクライブし、Event Grid スキーマを使用します。 Azure CLI では、次を使用します。

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

次の例では、イベントの入力スキーマを使用します。

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

次の例では、Event Grid トピックをサブスクライブし、Event Grid スキーマを使用します。 PowerShell では、次を使用します。

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

次の例では、イベントの入力スキーマを使用します。

```azurepowershell-interactive
New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>トピックへのイベントの公開

これで、カスタム トピックにイベントを送信し、マッピングの結果を確認する準備が整いました。 次のスクリプトで[サンプル スキーマ](#original-event-schema)を使ってイベントを投稿します。

Azure CLI では、次を使用します。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

PowerShell では、次を使用します。

```azurepowershell-interactive
$endpoint = (Get-AzEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

ここで、Webhook エンドポイントを確認します。 2 つのサブスクリプションで異なるスキーマを使ってイベントが配信されています。

最初のサブスクリプションでは、Event Grid スキーマを使用しました。 配信されたイベントの形式は次のとおりです。

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

これらのフィールドには、カスタム トピックのマッピングが含まれています。 **myEventTypeField** は **EventType** にマップされています。 **DataVersion** と **Subject** の既定値が使用されています。 **Data** オブジェクトには元のイベント スキーマのフィールドが含まれています。

2 つ目のサブスクリプションでは、入力イベント スキーマを使用しました。 配信されたイベントの形式は次のとおりです。

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

元のフィールドが配信されていることに注目してください。

## <a name="next-steps"></a>次のステップ

* イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
