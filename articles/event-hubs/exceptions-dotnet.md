---
title: Azure Event Hubs - .NET の例外
description: この記事では、Azure Event Hubs .NET メッセージングの例外と推奨アクションの一覧を示します。
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: spelluru
ms.openlocfilehash: af2d820fd255d041ade21a00d7d7a9ac6b12fcba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745904"
---
# <a name="eventhubsexception---net"></a>EventHubsException - .NET
**EventHubsException** は、Event Hubs に固有の操作によって問題 (サービス内のエラーとクライアント固有の両方のエラーを含む) が発生した場合にトリガーされます。 

## <a name="exception-information"></a>例外情報
例外には、エラーのコンテキストとその相対的な重要度を理解するのに役立つ、次のコンテキスト情報が含まれています。 

- **IsTransient**:例外が回復可能であると見なされるかどうかを示します。 一時的であると見なされた場合は、適切な再試行ポリシーが既に適用されており、再試行が失敗しています。
- **[Reason]\(理由\)** :根本原因を分類して明確にするのに役立つ、一連の既知のエラーの原因を示します。 これらの理由は、例外メッセージのテキストを調べることが理想的ではない場合に、例外のフィルター処理やその他のロジックを適用できるようにすることを目的としています。 障害の主な理由をいくつか次に示します。
    - **クライアントが閉じられた**:Event Hub クライアントが既に閉じられているか破棄されているときに発生します。 アプリケーション コードを確認して、Event Hubs クライアント ライブラリのオブジェクトが意図したスコープで作成され、閉じられていることを確認することをお勧めします。
    - **サービスのタイムアウト**:予想された時間内に、Event Hubs サービスから操作に対する応答がなかったことを示します。 この問題は、一時的なネットワークの問題またはサービスの問題が原因で発生した可能性があります。 Event Hubs サービスが要求を正常に完了したどうか分からず、状態が不明です。 現在の状態を確認し、必要に応じて再試行することをお勧めします。
    - **クォータを超過した**:1 つのコンシューマー グループに対するアクティブな読み取り操作が多すぎることを示します。 この制限は Event Hubs 名前空間のレベルによって異なり、上位レベルへの移行が必要になる場合があります。 別の方法として、追加のコンシューマー グループを作成し、どのグループに対してもコンシューマー クライアントの読み取り数が制限内になるようにすることもできます。 詳細については、「[Azure Event Hubs のクォータと制限](event-hubs-quotas.md)」をご覧ください。
    - **メッセージ サイズの超過**:個々のイベントとイベントのバッチの両方で許容される最大サイズとしてのイベント データ。 これにはイベントのデータと、関連するメタデータとシステムのすべてのオーバーヘッドが含まれます。 このエラーを解決するには、バッチで送信されるイベントの数を減らすか、メッセージに含まれるデータのサイズを小さくします。 サイズの制限は変更される可能性があるため、詳細については、「[Azure Event Hubs のクォータと制限](event-hubs-quotas.md)」をご覧ください。
    - **コンシューマーの切断**:コンシューマー クライアントが Event Hub サービスによってイベント ハブのインスタンスから切断されました。 これは通常、より所有者レベルのコンシューマーがパーティションとコンシューマー グループのペアリングに対する所有権を主張した場合に発生します。
    - **リソースが見つかりません**:Event Hubs サービスは、イベント ハブ、コンシューマー グループ、パーティションなどのリソースを見つけることができませんでした。 削除されたか、Event Hubs サービス自体に問題がある可能性があります。

## <a name="handling-exceptions"></a>例外の処理
**EventHubException** の特定の失敗の理由に対処するには、いくつかの方法があります。 1 つの方法は、catch ブロックの一部として例外フィルター句を適用することです。

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>次の手順
例外は他にもあり、[レガシの記事](event-hubs-messaging-exceptions.md)に記載されています。 これらの一部は、レガシの Event Hubs .NET クライアント ライブラリにのみ適用されます。