---
title: Azure Maps の実行時間の長い操作 API
description: Azure Maps での実行時間の長い非同期バックグラウンド処理について説明します
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f5fb7c8059c8b98e8ec514a4159e96f48db7b1ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906201"
---
# <a name="creator-preview-long-running-operation-api"></a>Creator (プレビュー) の実行時間の長い操作 API

> [!IMPORTANT]
> Azure Maps Creator サービスは、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Maps の一部の API では、[非同期の要求-応答パターン](/azure/architecture/patterns/async-request-reply)が使用されます。 このパターンにより、Azure Maps で高可用性と応答性の高いサービスを提供できます。 この記事では、実行時間の長い非同期バックグラウンド処理の Azure Map 固有の実装について説明します。

## <a name="submitting-a-request"></a>要求の送信

クライアント アプリケーションでは、HTTP API への同期呼び出しを使用して、実行時間の長い操作を開始します。 通常、この呼び出しは HTTP POST 要求の形式です。 非同期ワークロードが正常に作成されると、API から HTTP `202` 状態コードが返されて、要求が受け入れられたことが示されます。 この応答には、実行時間の長い操作の状態を確認するためにクライアントがポーリングできるエンドポイントを指す `Location` ヘッダーが含まれています。

### <a name="example-of-a-success-response"></a>成功応答の例

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

呼び出しが検証に合格しなかった場合、API からは代わりに無効な要求に対する HTTP `400` 応答が返されます。 応答の本文では、要求が無効であった理由に関する詳細情報がクライアントに提供されます。

### <a name="monitoring-the-operation-status"></a>操作の状態の監視

受け付けられた応答ヘッダーで提供される場所エンドポイントをポーリングして、実行時間の長い操作の状態を確認できます。 操作状態要求からの応答本文には、常に `status` プロパティと `createdDateTime` プロパティが含まれます。 `status` プロパティでは、実行時間の長い操作の現在の状態が示されます。 返される可能性のある状態は、`"NotStarted"`、`"Running"`、`"Succeeded"`、`"Failed"` です。 `createdDateTime` プロパティでは、実行時間の長い操作を開始する最初の要求が行われた時刻が示されます。 状態が `"NotStarted"` または `"Running"` の場合は、応答で `Retry-After` ヘッダーも提供されます。 `Retry-After` ヘッダー (秒単位) を使用して、操作状態 API に対する次のポーリング呼び出しを行うタイミングを決定できます。

### <a name="example-of-running-a-status-response"></a>状態応答の実行の例

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>操作の完了の処理

実行時間の長い操作が完了すると、応答の状態は `"Succeeded"` または `"Failed"` になります。 実行時間の長い操作で新しいリソースが作成された場合は、成功応答で HTTP `201 Created` 状態コードが返されます。 応答には、リソースに関するメタデータを指す `Location` ヘッダーも含まれます。 新しいリソースが作成されなかった場合は、成功応答で HTTP `200 OK` 状態コードが返されます。 エラーが発生した場合も、応答状態コードは `200 OK` コードになります。 ただし、応答の本文には `error` プロパティが含まれています。 エラー データは、OData エラー仕様に準拠しています。

### <a name="example-of-success-response"></a>成功応答の例

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>失敗応答の例

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```