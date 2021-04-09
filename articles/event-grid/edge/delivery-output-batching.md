---
title: Azure Event Grid IoT Edge での出力のバッチ処理 | Microsoft Docs
description: Event Grid on IoT Edge での出力のバッチ処理。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0ae2261f8278c4d5e1944b01a9731afd293df20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171637"
---
# <a name="output-batching"></a>出力のバッチ処理

Event Grid では、1 つの配信要求による複数のイベントの配信がサポートされています。 この機能により、HTTP の要求ごとのオーバーヘッドを費やすことなく、配信全体のスループットを向上させることができます。 バッチ処理は、既定では無効になっており、サブスクリプションごとに有効にできます。

> [!WARNING]
> サブスクライバー コードがバッチ処理要求ごとに、潜在的により多くの作業を行う必要がある場合でも、各配信要求を処理するために許可される最大期間は変わりません。 配信タイムアウトの既定値は 60 秒です。

## <a name="batching-policy"></a>バッチ処理のポリシー

Event Grid のバッチ処理の動作は、次の 2 つの設定を調整して、サブスクライバーごとにカスタマイズできます。

* バッチごとの最大イベント数

  この設定では、バッチ処理される配信要求に追加できるイベントの数の上限が決まります。

* 優先バッチ サイズ (KB 単位)

  このノブは、配信要求ごとに送信できる最大キロバイト数をさらに制御するために使用されます

## <a name="batching-behavior"></a>バッチ処理の動作

* すべてまたはなし

  Event Grid 操作は、"すべてまたはなし" のセマンティクスです。 バッチ配信の一部成功はサポートされていません。 サブスクライバーは、バッチごとに 60 秒で合理的に処理できるだけの数のイベントを要求するように注意する必要があります。

* オプティミスティック バッチ処理

  バッチ処理ポリシー設定は、バッチ処理動作に対する厳密な限界ではなく、ベストエフォート ベースで尊重されます。 イベント レートが低い場合、バッチのサイズが、バッチごとのイベントの要求した最大数より小さいことがよくあります。

* 既定値は OFF に設定されています

  既定では、Event Grid によって、各配信要求に 1 つのイベントのみが追加されます。 バッチ処理を有効にする方法は、イベント サブスクリプション JSON で、この記事で前述した設定のいずれかを指定することです。

* 既定値

  イベント サブスクリプションを作成するときに、両方の設定 (バッチあたりの最大イベント数とキロバイト単位の概算バッチ サイズ) を指定する必要はありません。 1 つの設定さえ指定されれば、Event Grid によって (構成可能な) 既定値が使用されます。 既定値と、それらをオーバーライドする方法については、以降のセクションを参照してください。

## <a name="turn-on-output-batching"></a>出力のバッチ処理の有効化

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>最大許容値の構成

次のデプロイ時設定によって、イベント サブスクリプションの作成時に許容される最大値が制御されます。

| プロパティ名 | 説明 |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | `PreferredBatchSizeInKilobytes` ノブに許可される最大値。 既定値は `1033` です。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch` ノブに許可される最大値。 既定値は `50` です。

## <a name="configuring-runtime-default-values"></a>実行時の既定値の構成

イベント サブスクリプションでの指定がない場合、次のデプロイ時設定によって、各ノブの実行時の既定値が制御されます。 繰り返しになりますが、バッチ処理動作を有効にするには、イベント サブスクリプションで少なくとも 1 つのノブを設定する必要があります。

| プロパティ名 | 説明 |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | `MaxEventsPerBatch` のみが指定されている場合の配信要求の最大サイズ。 既定値は `1_058_576` です。
| `broker__defaultMaxEventsPerBatch` | `MaxBatchSizeInBytes` のみが指定されている場合にバッチに追加するイベントの最大数。 既定値は `10` です。
