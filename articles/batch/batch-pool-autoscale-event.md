---
title: Azure Batch プールの自動スケーリング イベント
description: プールの自動スケーリングが実行された後に発生する、Batch プールの自動スケーリング イベントのリファレンスです。 ログの内容は、プールの自動スケーリングの数式と評価結果です。
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852070"
---
# <a name="pool-autoscale-event"></a>プールの自動スケーリング イベント

 このイベントは、プールの自動スケーリングが実行されると発生します。 ログの内容は、プールの自動スケーリングの数式と評価結果です。

 以下の例は、サンプル データが不足しているために失敗した、プールの自動スケーリングのプールの自動スケーリング イベントの本文を示しています。

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|要素|Type|Notes|
|-------------|----------|-----------|
|`id`|String|プールの ID。|
|`timestamp`|DateTime|自動スケーリングが実行されたタイミングのタイムスタンプ。|
|`formula`|String|自動スケーリング用に定義された数式。|
|`results`|String|数式で使用されるすべての変数の評価結果。|
|[`error`](#error)|複合型|自動スケーリングの詳細なエラー。|

###  <a name="error"></a><a name="error"></a> エラー

|要素名|Type|Notes|
|------------------|----------|-----------|
|`code`|String|自動スケーリング エラーの識別子。 コードは不変であり、プログラムによって使用されることを意図しています。|
|`message`|String|ユーザー インターフェイスに表示するのに適した、自動スケーリングのエラーについて説明するメッセージ。|
|`values`|Array|自動スケーリング エラーの詳細を説明する名前と値のペアの一覧。|
