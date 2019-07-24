---
title: Azure Event Grid でのイベントのフィルター処理
description: Azure Event Grid サブスクリプションを作成するときにイベントをフィルター処理する方法について説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 87599b05a3569bf6f28880352185a131f48a7f52
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470629"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Event Grid サブスクリプションでのイベントのフィルター処理を理解します

この記事では、ご利用のエンドポイントに送信されるイベントをフィルター処理するさまざまな方法について説明します。 イベント サブスクリプションを作成するとき、フィルター処理を行うためのオプションとして次の 3 つがあります。

* イベントの種類
* 指定の値で開始または終了する件名
* 高度なフィールドおよび演算子

## <a name="event-type-filtering"></a>イベントの種類のフィルター処理

既定では、イベント ソースに関するすべての[イベントの種類](event-schema.md)がエンドポイントに送信されます。 特定のイベントの種類のみをご利用のエンドポイントに送信するように決めることができます。 たとえば、ご利用のリソースに対する更新は通知されても、削除のような他の操作は通知されないようにすることができます。 その場合は、イベントの種類 `Microsoft.Resources.ResourceWriteSuccess` でフィルター処理します。 イベントの種類を含む配列を指定するか、またはイベント ソースに関するすべてのイベントの種類を取得する `All` を指定します。

イベントの種類でフィルター処理を行うための JSON 構文を次に示します。

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>件名のフィルター処理

件名によるシンプルなフィルター処理の場合は、件名の開始値または終了値を指定します。 たとえば、`.txt` で終わる件名を指定することで、ストレージ アカウントへのテキスト ファイルのアップロードに関連するイベントのみを取得できます。 または、`/blobServices/default/containers/testcontainer` で終わる件名をフィルター処理することで、ストレージ アカウント内の他のコンテナーではなく、そのコンテナーのすべてのイベントを取得できます。

イベントをカスタム トピックに発行する場合は、サブスクライバーがそのイベントに関心があるかどうかを簡単に知ることができるイベントの件名を作成してください。 サブスクライバーは、件名のプロパティを使用してイベントのフィルター処理またはルーティングを行います。 そのイベントが発生したパスを追加することにより、サブスクライバーがそのパスのセグメントでフィルター処理できるように考慮してください。 このパスにより、サブスクライバーはイベントを狭く、または幅広くフィルター処理できます。 `/A/B/C` のように件名に 3 つのセグメント パスを示した場合、サブスクライバーは最初のセグメント `/A` でフィルター処理して幅広い一連のイベントを取得できます。 これらのサブスクライバーは、`/A/B/C` や `/A/D/E` などの件名を持つイベントを取得します。 他のサブスクライバーは、`/A/B` でフィルター処理して、より狭い一連のイベントを取得できます。

イベントの種類でフィルター処理を行うための JSON 構文を次に示します。

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>高度なフィルター処理

データ フィールド内の値でフィルター処理して、比較演算子を指定するには、高度なフィルター処理オプションを使用します。 高度なフィルター処理では、次を指定します。

* 演算子の種類 - 比較の種類。
* キー - フィルター処理のために使用するイベント データ内のフィールド。 キーとして数値、ブール値、または文字列を指定できます。
* 値 - キーと比較する 1 つ以上の値。

高度なフィルターを使用する場合の JSON 構文を次に示します。

```json
"filter": {
  "advancedFilters": [
    {
      "operatorType": "NumberGreaterThanOrEquals",
      "key": "Data.Key1",
      "value": 5
    },
    {
      "operatorType": "StringContains",
      "key": "Subject",
      "values": ["container1", "container2"]
    }
  ]
}
```

### <a name="operator"></a>operator

数値に対して使用できる演算子は次のとおりです。

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

ブール値に対して使用できる演算子は BoolEquals です。

文字列に対して使用できる演算子は次のとおりです。

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

すべての文字列比較で、大文字と小文字が区別されます。

### <a name="key"></a>キー

Event Grid スキーマ内のイベントの場合、キーには次の値を使用します。

* Id
* トピック
* Subject
* EventType
* DataVersion
* イベント データ (Data.key1 など)

Cloud Events スキーマのイベントの場合は、キーの次の値を使用します。

* EventId
* ソース
* EventType
* EventTypeVersion
* イベント データ (Data.key1 など)

カスタム入力スキーマの場合は、イベント データ フィールドを使用します (Data.key1 など)。

### <a name="values"></a>値

指定できる値は次のとおりです。

* number
* 文字列
* ブール値
* array

### <a name="limitations"></a>制限事項

高度なフィルター処理には次の制限事項があります。

* Event Grid サブスクリプションあたり高度なフィルターは 5 つ
* 文字列値あたり 512 文字
* **in** 演算子および **not in** 演算子の値は 5 つ
* キーの入れ子レベルは 1 つまでとします (data.key1 など)
* カスタム イベント スキーマは、トップレベルのフィールドでのみフィルター処理できます。

複数のフィルターで同じキーを使用できます。

## <a name="next-steps"></a>次の手順

* PowerShell および Azure CLI を使用したイベント フィルター処理については、[Event Grid でのイベントのフィルター処理](how-to-filter-events.md)に関するページを参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
