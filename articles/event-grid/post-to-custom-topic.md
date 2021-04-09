---
title: Azure Event Grid のカスタム トピックにイベントを投稿する
description: この記事では、カスタム トピックにイベントを投稿する方法について説明します。 投稿とイベント データの形式を示します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ed126487938e524264c94544903460854ffc4d41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98681619"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Azure Event Grid のカスタム トピックに投稿する

この記事では、カスタム トピックにイベントを投稿する方法について説明します。 投稿とイベント データの形式を示します。 [サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) は、予期される形式と一致する投稿に対してのみ適用されます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>エンドポイント

カスタム トピックに HTTP POST を送信するときは、`https://<topic-endpoint>?api-version=2018-01-01` という URI 形式を使います。

たとえば、`https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` は有効な URI です。

Azure CLI でカスタム トピックのエンドポイントを取得するには、次のコマンドを使います。

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Azure PowerShell でカスタム トピックのエンドポイントを取得するには、次のコマンドを使います。

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>ヘッダー

要求では、認証用のキーを含む `aeg-sas-key` という名前のヘッダー値を設定します。

たとえば、`aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==` は有効なヘッダー値です。

Azure CLI でカスタム トピックのキーを取得するには、次のコマンドを使います。

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

PowerShell でカスタム トピックのキーを取得するには、次のコマンドを使います。

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>イベント データ

カスタム トピックでは、最上位レベルのデータに、リソースによって定義される標準的なイベントと同じフィールドが含まれます。 これらのプロパティの 1 つは、カスタム トピックに固有のプロパティを含むデータ プロパティです。 イベントの発行元が、そのデータ オブジェクトのプロパティを決定します。 次のスキーマを使います。

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

これらのプロパティについては、「[Azure Event Grid イベント スキーマ](event-schema.md)」をご覧ください。 Event Grid トピックへイベントを送信する際の、配列の合計サイズの上限は 1 MB です。 イベントの最大許容サイズも 1 MB です。 64 KB を超えるイベントは、64 KB の増分単位で課金されます。 

たとえば、次に示すのは有効なイベント データ スキーマです。

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Response

トピック エンドポイントへの投稿後に、応答を受信します。 応答は、標準 HTTP 応答コードです。 いくつかの一般的な応答を次に示します。

|結果  |Response  |
|---------|---------|
|Success  | 200 OK  |
|イベント データの形式が正しくない | 400 Bad Request |
|無効なアクセス キー | 401 権限がありません |
|エンドポイントが正しくない | 404 見つかりません |
|配列またはイベントが、サイズ制限を超えています | 413 ペイロードが大きすぎます |

エラーの場合、メッセージ本文は次の形式になります。

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>次のステップ

* イベント配信の監視について詳しくは、「[Event Grid メッセージ配信の監視](monitor-event-delivery.md)」をご覧ください。
* 認証キーについて詳しくは、「[Event Grid のセキュリティと認証](security-authentication.md)」をご覧ください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
