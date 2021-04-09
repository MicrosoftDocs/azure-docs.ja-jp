---
title: 配信と再試行 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid on IoT Edge での配信と再試行。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: aa0b3a05fb26f6be951b697145d7b22e03b7792d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171620"
---
# <a name="delivery-and-retry"></a>配信と再試行

Event Grid は、持続性のある配信を提供します。 一致する各サブスクリプションに対して、最低 1 回は各メッセージを直ちに配信しようとします。 サブスクライバーのエンドポイントがイベントの受信を認識しない場合、またはエラーが発生した場合、Event Grid は固定された **再試行スケジュール** および **再試行ポリシー** に基づいて、配信を再試行します。  既定では、Event Grid モジュールはサブスクライバーに一度に 1 つのイベントを配信します。 ただし、ペイロードは 1 つのイベントを持つ配列です。 出力バッチ処理機能を有効にすると、モジュールに複数のイベントを同時に配信させることができます。 この機能の詳細については、「[出力のバッチ処理](delivery-output-batching.md)」を参照してください。  

> [!IMPORTANT]
>イベント データの永続化はサポートされていません。 つまり、Event Grid モジュールを再デプロイまたは再起動すると、まだ配信されていないイベントが失われることを意味します。

## <a name="retry-schedule"></a>再試行のスケジュール

Event Grid は、メッセージの配信後、応答を最大 60 秒間待機します。 サブスクライバーのエンドポイントが応答を確認しない場合は、後続の再試行のために、メッセージがバックオフ キューのいずれかにエンキューされます。

事前に構成されたバックオフ キューは 2 つあり、それらによって再試行の実行スケジュールが決定されます。 次のとおりです。

| スケジュール | 説明 |
| ---------| ------------ |
| 1 分 | ここに来るメッセージは、1 分ごとに試行されます。
| 10 分 | ここに来るメッセージは、10 分ごとに試行されます。

### <a name="how-it-works"></a>しくみ

1. メッセージが Event Grid モジュールに到着します。 すぐに配信しようとします。
1. 配信に失敗した場合、メッセージは 1 分のキューにエンキューされ、1 分後に再試行されます。
1. 配信の失敗が続く場合、メッセージは 10 分のキューにエンキューされ、10 分ごとに再試行されます。
1. 成功するか、再試行ポリシーの制限に達するまで、配信が試行されます。

## <a name="retry-policy-limits"></a>再試行ポリシーの制限

再試行ポリシーを決定する構成は、2 つあります。 次のとおりです。

* 最大試行回数
* イベントの Time-To-Live (TTL)

再試行ポリシーの制限のいずれかに達した場合、イベントは削除されます。 再試行スケジュール自体については、「再試行のスケジュール」セクションで説明しました。 これらの制限の構成は、すべてのサブスクライバーに対して、またはサブスクリプションごとに行うことができます。 次のセクションでは、それぞれについて詳しく説明します。

## <a name="configuring-defaults-for-all-subscribers"></a>すべてのサブスクライバーに対する既定値の構成

`brokers__defaultMaxDeliveryAttempts` と `broker__defaultEventTimeToLiveInSeconds` という 2 つのプロパティは、Event Grid デプロイの一部として構成できます。このデプロイによって、すべてのサブスクライバーに対する再試行ポリシーの既定値が制御されます。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | イベントを配信する試行の最大数。 既定値は 30 です。
| `broker__defaultEventTimeToLiveInSeconds` | イベントが配信されなかった場合に削除されるまでのイベント TTL (秒単位)。 既定値は **7200** 秒です。

## <a name="configuring-defaults-per-subscriber"></a>サブスクライバーごとの既定値の構成

また、サブスクリプションごとに再試行ポリシーの制限を指定することもできます。
サブスクライバーごとの既定値を構成する方法については、[API のドキュメント](api.md)を参照してください。 サブスクリプション レベルの既定値は、モジュール レベルの構成をオーバーライドします。

## <a name="examples"></a>例

次の例では、Event Grid モジュールで maxNumberOfAttempts = 3 と 30 分のイベント TTL を使用して、再試行ポリシーを設定します

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

次の例では、maxNumberOfAttempts = 3 と 30 分のイベント TTL を使用して、Web hook サブスクリプションを設定します

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
