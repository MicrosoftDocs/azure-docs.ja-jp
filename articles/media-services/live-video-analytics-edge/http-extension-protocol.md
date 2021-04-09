---
title: HTTP 拡張プロトコル - Azure
description: この記事では、HTTP 拡張プロトコルを使用した、Live Video Analytics モジュールと AI または CV モジュールの間でのメッセージの送信について説明します。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: c3a9b5b70467479fdb9b1473a1f6738eaa8a7e1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455892"
---
# <a name="http-extension-protocol"></a>HTTP 拡張プロトコル

Live Video Analytics on IoT Edge では、[グラフ拡張ノード](/azure/media-services/live-video-analytics-edge/media-graph-extension-concept)を使用してメディア グラフ処理機能を拡張することができます。 HTTP 拡張プロセッサを拡張ノードとして使用した場合、Live Video Analytics モジュールとご利用の AI (または CV) モジュールとの間の通信は HTTP で行われます。

この記事では、HTTP 拡張プロトコルを使用した、Live Video Analytics モジュールと AI または CV モジュールの間でのメッセージの送信について説明します。 

HTTP コントラクトは、次の 2 つのコンポーネント間で定義されます。

* HTTP サーバー
* HTTP クライアントとして機能する、IoT Edge モジュールの Live Video Analytics

## <a name="request"></a>要求

Live Video Analytics モジュールから HTTP サーバーへの要求は次のようになります。

|Key|値|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|Accept|application/json, */*|
|承認|Basic、Digest、ベアラー (カスタム ヘッダーのサポートを通じて)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Length 本文の長さ (バイト単位)|
|User-Agent|Azure Media Services|
|Body|サポートされているコンテンツ タイプの 1 つでバイナリ エンコードされたイメージ バイト。|

### <a name="example"></a>例

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>Response

モジュールから Live Video Analytics モジュールへの応答は次のようになります。

|Key|値|
|---|---|
|状態コード|200 OK - 推論の結果が見つかりました<br/>204 No Content - AI によってコンテンツが見つかりませんでした<br/>400 Bad Request - 想定されていません<br/>500 Internal Server Error - 想定されていません<br/>503 Server Busy - AMS は、"Retry-After" ヘッダーに基づいて、またはヘッダーが事前に設定されていない場合の既定の時間に基づいてバックオフされます。|
|Content-Type|application/json|
|Content-Length|本文の長さ (バイト単位)|
|Body|単一の "inferences" プロパティを備えた JSON オブジェクト。|

### <a name="example"></a>例

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

[推論メタデータ スキーマ オブジェクト モデル](/azure/media-services/live-video-analytics-edge/inference-metadata-schema)に従って定義した事前に定義されたスキーマに従って、有効な JSON ドキュメントを使用して応答を返すことを強くお勧めします。 これにより、他のコンポーネント、および Live Video Analytics モジュールに今後追加される可能性がある機能との相互運用性が向上します。

モジュールによってコンテンツ タイプが "application/json" でない応答が返される場合、Live Video Analytics では、メッセージが base 64 コンテンツとしてエンコードされ、非透過の JSON ペイロードとしてシリアル化されます。

モジュールによってコンテンツ タイプが "application/json" の応答が返されたが、JSON スキーマが下記の推定メタデータ スキーマに従っていない場合、メッセージ ペイロードはパイプラインを介して転送されますが、相互運用性は低下します。 推論メタデータ スキーマに関する最新の詳しい日付情報については、[こちら](/azure/media-services/live-video-analytics-edge/inference-metadata-schema)を参照してください。

> [!NOTE]
> モジュールが結果を生成しない場合、応答本文が空の HTTP 204 状態コード (No Content) が返されます。 Live Video Analytics はこれを空の結果として認識し、パイプライン全体でイベントを転送しません。


## <a name="next-steps"></a>次のステップ

[gRPC 拡張プロトコル](./grpc-extension-protocol.md)
