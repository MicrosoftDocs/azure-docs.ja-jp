---
title: Azure Event Grid - サブスクリプション検証のトラブルシューティング
description: この記事では、サブスクリプション検証をトラブルシューティングする方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48844859013507ab684ef8879b7b85dd6b6fe8cd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118989"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Azure Event Grid サブスクリプション検証のトラブルシューティング
この記事では、イベント サブスクリプション検証をトラブルシューティングする方法について説明します。 

> [!IMPORTANT]
> Webhook のエンドポイント検証の詳細については、「[Webhook のイベント配信](webhook-event-delivery.md)」をご覧ください。

## <a name="validate-event-grid-event-subscription-using-postman"></a>Postman を使用して Event Grid イベント サブスクリプションを検証する
Event Grid イベントの Webhook サブスクリプションを検証するために Postman を使用する例を次に示します。 

![Postman を使用した Event Grid イベント サブスクリプション検証](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

サンプルの **SubscriptionValidationEvent** の JSON を次に示します。

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

正常な応答のサンプルを次に示します。

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Webhook の Event Grid イベント検証の詳細については、「[Event Grid イベントを使用したエンドポイントの検証](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)」をご覧ください。


## <a name="validate-event-grid-event-subscription-using-curl"></a>Curl を使用して Event Grid イベント サブスクリプションを検証する 
Event Grid イベントの Webhook サブスクリプションを検証するためのサンプル Curl コマンドを次に示します。 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Postman を使用してクラウド イベント サブスクリプションを検証する
クラウド イベントの Webhook サブスクリプションを検証するために Postman を使用する例を次に示します。 

![Postman を使用したクラウド イベント サブスクリプション検証](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

クラウド イベントを使用した検証には **HTTP OPTIONS** メソッドを使用します。 Webhook のクラウド イベント検証の詳細については、[クラウド イベントを使用したエンドポイントの検証](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)に関する記事をご覧ください。

## <a name="error-code-403"></a>エラー コード:403
Webhook から応答で 403 (禁止) が返される場合、Webhook が Azure Application Gateway または Web アプリケーション ファイアウォールの背後にあるかどうかを確認します。 そうである場合は、これらのファイアウォール規則を無効にし、もう一度 HTTP POST を実行する必要があります。

  - 920300 (要求に Accept ヘッダーがありません、これは修正できます)
  - 942430 (制限された SQL 文字の異常検出 (引数): 特殊文字数を超過しました (12))
  - 920230 (複数の URL エンコードが検出されました)
  - 942130 (SQL インジェクション攻撃:SQL トートロジーが検出されました。)
  - 931130 (可能性のあるリモート ファイル インクルード (RFI) 攻撃 = ドメイン外参照/リンク)

## <a name="next-steps"></a>次のステップ
さらにサポートが必要な場合は、[Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-eventgrid)に問題を投稿するか、[サポート チケット](https://azure.microsoft.com/support/options/)を開いてください。 
