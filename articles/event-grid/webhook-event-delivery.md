---
title: webhook のイベント配信
description: この記事では、WebHook を使用する場合の WebHook イベント配信とエンドポイント検証について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e9a52d0cb3e4e880d91e1b748d97ef3041298930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87461240"
---
# <a name="webhook-event-delivery"></a>Webhook のイベント配信
Webhook は、Azure Event Grid からイベントを受信する多数ある方法の 1 つです。 新しいイベントの準備ができるたびに、Event Grid サービスは、本文にイベントが含まれる HTTP 要求を構成済み HTTP エンドポイントに POST します。

Webhook をサポートする他の多くのサービスと同様に、Event Grid を使用するには、Webhook エンドポイントへのイベントの配信を開始する前に、そのエンドポイントの所有権を証明する必要があります。 この要件により、悪意のあるユーザーはエンドポイントをイベントで氾濫させることができなくなります。 以下に示す 3 つの Azure サービスのいずれかを使用すると、Azure インフラストラクチャはこの検証を自動的に処理します。

- [Event Grid コネクタ](/connectors/azureeventgrid/)を使用した Azure Logic Apps
- [Webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) を使用した Azure Automation
- [Event Grid トリガー](../azure-functions/functions-bindings-event-grid.md)を使用した Azure Functions

## <a name="endpoint-validation-with-event-grid-events"></a>Event Grid イベントを使用したエンドポイントの検証
他の種類のエンドポイント (HTTP トリガー ベースの Azure 関数など) を使用する場合は、エンドポイントのコードが Event Grid を使用した検証ハンドシェイクに参加する必要があります。 Event Grid では、サブスクリプションを検証する 2 つの方法がサポートされています。

1. **同期ハンドシェイク**:イベント サブスクリプションの作成時に、Event Grid はサブスクリプション検証イベントをエンドポイントに送信します。 このイベントのスキーマは、他の Event Grid イベントに似ています。 このイベントのデータ部分には `validationCode` プロパティが含まれます。 アプリケーションでは、検証の要求が想定されるイベント サブスクリプションに対するものであることが確認され、検証コードを応答で同期的に返します。 このハンドシェイク メカニズムは、すべての Event Grid バージョンでサポートされます。

2. **非同期ハンドシェイク**:検証コードを応答で同期的に返さない場合があります。 たとえば、サード パーティのサービス ([`Zapier`](https://zapier.com) や [IFTTT](https://ifttt.com/) など) を使用する場合は、プログラムによって検証コードに応答できません。

   2018-05-01-preview バージョン以降では、手動の検証ハンドシェイクが Event Grid によってサポートされるようになりました。 API バージョン 2018-05-01-preview 以降を使用する SDK またはツールでイベント サブスクリプションを作成すると、Event Grid によって、サブスクリプション検証イベントのデータ部分で `validationUrl` プロパティが送信されます。 ハンドシェイクを完了するには、イベント データ内でその URL を探し、そこへ GET 要求を実行します。 REST クライアントと Web ブラウザーのどちらでも使用できます。

   指定された URL は **5 分間** 有効です。 この期間、イベント サブスクリプションのプロビジョニング状態は `AwaitingManualAction` になります。 手動の検証を 5 分以内に完了しなかった場合、プロビジョニング状態は `Failed` に設定されます。 手動による検証を始める前に、もう一度イベント サブスクリプションを作成する必要があります。

   この認証メカニズムではまた、webhook エンドポイントが 200 の HTTP 状態コードを返すことも必要です。それにより、手動検証モードに設定される前に、検証イベントの POST が受け付けられたことを認識できるようになります。 つまり、エンドポイントが 200 を返しても、同期的に検証の応答を戻さないと、モードは手動検証モードに移行されます。 5 分以内に検証 URL に GET が存在した場合、検証ハンドシェイクは成功したと見なされます。

> [!NOTE]
> 検証に自己署名証明書を使用することは、サポートされていません。 代わりに、商用証明機関 (CA) からの署名入り証明書を使用します。

### <a name="validation-details"></a>検証の詳細

- イベント サブスクリプションの作成時または更新時に、Event Grid はサブスクリプション検証イベントをターゲット エンドポイントに投稿します。
- このイベントには、ヘッダー値 "aeg-event-type: SubscriptionValidation" が含まれています。
- イベント本文のスキーマは、他の Event Grid イベントと同じです。
- イベントの eventType プロパティは、`Microsoft.EventGrid.SubscriptionValidationEvent` です。
- イベントの data プロパティには、ランダムに生成された文字列を持つ `validationCode` プロパティが含まれています。 たとえば、"validationCode: acb13…" のようなプロパティです。
- イベント データには、`validationUrl` プロパティと、サブスクリプションを手動で検証するための URL も含まれます。
- 配列には、検証イベントのみが含まれています。 その他のイベントは、検証コードをエコーで返した後、別の要求で送信されます。
- EventGrid DataPlane SDK には、サブスクリプション検証イベント データとサブスクリプション検証の応答に対応するクラスがあります。

SubscriptionValidationEvent の例を以下に示します。

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

エンドポイントの所有権を証明するには、次の例に示すように、validationResponse プロパティで検証コードをエコーで返します。

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

HTTP 200 OK 応答状態コードを返す必要があります。 HTTP 202 Accepted は有効な Event Grid サブスクリプション検証の応答として認識されません。 HTTP 要求は 30 秒以内に完了する必要があります。 操作が 30 秒以内に完了しない場合、操作は取り消され、5 秒後に再試行される場合があります。 すべての試行が失敗した場合、検証ハンドシェイク エラーとして扱われます。

または、検証 URL に GET 要求を手動で送信して、サブスクリプションを検証することができます。 イベント サブスクリプションは、検証されるまで保留状態にとどまります。 検証 URL ではポート 553 が使用されます。 ファイアウォール規則によってポート 553 がブロックされている場合、手動ハンドシェイクを正常に行うには、規則を更新する必要があります。

サブスクリプション検証ハンドシェイクの処理の例については、[C# のサンプル](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)に関するページをご覧ください。

## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v1.0 を使用したエンドポイントの検証
Event Grid を既に使い慣れている場合、不正使用を防ぐための Event Grid のエンドポイント検証ハンドシェイクをご存じかもしれません。 CloudEvents v1.0 では、HTTP OPTIONS メソッドを使用して、独自の[不正使用防止のセマンティクス](webhook-event-delivery.md)が実装されています。 詳細については、 [こちら](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)を参照してください。 出力に CloudEvents スキーマを使用すると、Event Grid では、Event Grid の検証イベント メカニズムではなく CloudEvents v1.0 の不正使用防止が使用されます。

## <a name="next-steps"></a>次のステップ
イベント サブスクリプション検証をトラブルシューティングする方法については、次の記事を参照してください。 

[イベント サブスクリプション検証のトラブルシューティング](troubleshoot-subscription-validation.md)
