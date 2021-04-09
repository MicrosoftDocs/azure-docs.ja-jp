---
title: Azure Event Grid - サブスクリプション検証のトラブルシューティング
description: この記事では、サブスクリプション検証をトラブルシューティングする方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94592942"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Azure Event Grid サブスクリプション検証のトラブルシューティング
イベント サブスクリプションの作成中に `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` のようなエラー メッセージが表示された場合は、検証ハンドシェイクでエラーが発生したことを示しています。 このエラーを解決するには、次の点を確認します。

- Postman か curl、あるいは同様のツールを使用し、[サンプル SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) の要求本文で Webhook URL に HTTP POST を実行します。
- Webhook で同期検証ハンドシェイク機構が実装されている場合、検証コードが応答の一部として返されることを確認します。
- Webhook で非同期検証ハンドシェイク機構が実装されている場合、HTTP POST から 200 OK が返されることを確認します。
- Webhook から応答で `403 (Forbidden)` が返される場合、Webhook が Azure Application Gateway または Web アプリケーション ファイアウォールの背後にあるかどうかを確認します。 そうである場合は、これらのファイアウォール規則を無効にし、もう一度 HTTP POST を実行する必要があります。
    - 920300 (要求に accept ヘッダーがありません)
    - 942430 (制限された SQL 文字の異常検出 (引数): 特殊文字数を超過しました (12))
    - 920230 (複数の URL エンコードが検出されました)
    - 942130 (SQL インジェクション攻撃:SQL トートロジーが検出されました。)
    - 931130 (可能性のあるリモート ファイル インクルード (RFI) 攻撃 = ドメイン外参照/リンク)

> [!IMPORTANT]
> Webhook のエンドポイント検証の詳細については、「[Webhook のイベント配信](webhook-event-delivery.md)」をご覧ください。

以降のセクションで、Postman と Curl を使用してイベント サブスクリプションを検証する方法について説明します。  

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

## <a name="troubleshoot-event-subscription-validation"></a>イベント サブスクリプション検証のトラブルシューティング

## <a name="next-steps"></a>次のステップ
さらにサポートが必要な場合は、[Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-eventgrid)に問題を投稿するか、[サポート チケット](https://azure.microsoft.com/support/options/)を開いてください。 
