---
title: 推論メタデータ スキーマ - Azure
description: この記事では、推論メタデータ スキーマについて説明します。
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 2de437577dc00692fb98c46fec32bfaa6612dc99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92019496"
---
# <a name="inference-metadata-schema"></a>推論メタデータ スキーマ 

HTTP ベースのコントラクトを使用するか、gRPC ベースのコントラクトを使用するかに関係なく、各推論オブジェクトは、このトピックで説明するオブジェクト モデルに従います。

## <a name="object-model"></a>オブジェクト モデル

![オブジェクト モデル](./media/inference-metadata-schema/object-model.png)
 
|型の定義|説明|
|---|---|
|タグ|結果に関連付けられたタグまたはラベル。タグ付けと共に、タグに関連付けられた信頼度値も取得します。|
|属性|結果に関連付けられた追加の属性。 信頼度値と共に推論エンジンから受け取る新しい属性を追加できます。|
|属性リスト|省略可能な属性のリスト。|
|Rectangle|画像の左上隅を基準とした長方形の領域。 必須プロパティは、"長さ"、"幅"、"高さ"、"原点から上端までの距離" です。|
|分類|サンプル全体に適用されることが多い分類子のラベル。 "タグ" を利用して結果を分類できます。|
|Entity|サンプルで検出または特定されたエンティティ。 エンティティは、推論エンジンによって検出されると、"タグ" を取得し、推論された追加の属性と、見つかったエンティティを囲む長方形のボックスの座標が返されます。|
|Event|サンプルで検出されたイベント。 サンプルでイベントが検出されると、イベントの名前とイベント固有のプロパティが返されます。|
|モーション|サンプルで検出されたモーション。 サンプルでモーションが検出されると、モーションが検出された長方形の境界ボックスの座標が返されます。|
|テキスト|サンプルに関連付けられたテキストと、そのテキストの開始および終了タイムスタンプが返されます。|
|その他|その他の一般的なペイロード情報を返します。|

次の例には、サポートされている推論の種類を含む単一のイベントが含まれています。

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>次のステップ

- [gRPC データ コントラクト](./grpc-extension-protocol.md)
- [HTTP データ コントラクト](./http-extension-protocol.md)