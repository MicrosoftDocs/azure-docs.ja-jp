---
title: Azure App Configuration REST API - 調整
description: Azure App Configuration REST API 使用時の調整を理解するための参照ページ
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b4472fdcacfe5b62c0cded089224c6d41e32f47d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932440"
---
# <a name="throttling"></a>調整

構成ストアには、それがサービスを提供できる要求に制限があります。 構成ストアに割り当てられたクォータを超過した要求は、HTTP 429 (要求が多すぎます) という応答を受け取ります。

調整は、異なるクォータ ポリシーに分けられます。

- **合計要求数** - 要求の合計数
- **合計帯域幅** - 送信データ (バイト単位)
- **ストレージ** - ユーザー データのストレージの合計サイズ (バイト単位)

## <a name="handling-throttled-responses"></a>調整された応答の処理

特定のクォータのレート制限に達した場合、サーバーはその種類の以降の要求に対して _429_ 状態コードで応答します。 _429_ の応答には _retry-after-ms_ ヘッダーが含まれ、要求のクォータが補充されるまでの推奨待機時間 (ミリ秒単位) がクライアントに提供されます。

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

上の例では、クライアントは許可されているクォータを超えており、それ以上の要求を試みる前に、操作の速度を落として 10 ミリ秒待機するように勧められています。 クライアントは、プログレッシブ バックオフも考慮する必要があります。

## <a name="other-retry"></a>その他の再試行

サービスは、クライアントの再試行が必要な調整以外の状況を識別する場合があります (例:503 サービス利用不可)。 ​このような場合はすべて、`retry-after-ms` 応答ヘッダーが提供されます。 堅牢性を高めるために、クライアントは、推奨される間隔に従って再試行を行うように勧められます。

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
