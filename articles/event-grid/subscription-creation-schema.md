---
title: "Azure Event Grid サブスクリプション スキーマ"
description: "Azure Event Grid でイベントにサブスクライブするためのプロパティについて説明します。"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/17/2017
ms.author: babanisa
ms.openlocfilehash: eff2352066a76010d6d882a7b7e1961870cd2d46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="event-grid-subscription-schema"></a>Event Grid サブスクリプション スキーマ

Event Grid サブスクリプションを作成するには、 Create Event サブスクリプション操作に対して要求を送信します。 次の形式を使用します。

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

たとえば、`examplegroup` という名前のリソース グループに、`examplestorage` という名前のストレージ アカウントのイベント サブスクリプションを作成するには、次の形式を使用します。

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

この記事は、要求の本体のプロパティとスキーマについて説明します。
 
## <a name="event-subscription-properties"></a>イベント サブスクリプションのプロパティ

| プロパティ | 型 | Description |
| -------- | ---- | ----------- |
| destination | オブジェクト | エンドポイントを定義するオブジェクト。 |
| filter | オブジェクト | イベントの種類をフィルター処理するための省略可能なフィールド。 |

### <a name="destination-object"></a>対象オブジェクト

| プロパティ | 型 | Description |
| -------- | ---- | ----------- |
| endpointType | string | サブスクリプションのエンドポイントの種類 (webhook/HTTP、イベント ハブ、またはキュー)。 | 
| endpointUrl | string |  | 

### <a name="filter-object"></a>フィルター オブジェクト

| プロパティ | 型 | Description |
| -------- | ---- | ----------- |
| includedEventTypes | array | イベント メッセージのイベントの種類がこれらのイベントの種類名のいずれかと完全に一致した場合に、適合します。 イベント名がイベント ソースに対して登録済みのイベントの種類名と一致しない場合は、エラーを発生させます。 既定では、すべてのイベントの種類と一致します。 |
| subjectBeginsWith | string | イベント メッセージの件名フィールドに対する、プレフィックス一致のフィルター。 既定値または空の文字列はすべて一致します。 | 
| subjectEndsWith | string | イベント メッセージの件名フィールドに対する、サフィックス一致のフィルター。 既定値または空の文字列はすべて一致します。 |
| subjectIsCaseSensitive | string | フィルターの大文字小文字の一致を制御します。 |


## <a name="example-subscription-schema"></a>サブスクリプション スキーマの例

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "blobCreated", "blobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "subjectIsCaseSensitive": "true"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。