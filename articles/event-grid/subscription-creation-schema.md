---
title: Azure Event Grid サブスクリプション スキーマ
description: この記事では、Azure Event Grid でイベントにサブスクライブするためのプロパティについて説明します。 Event Grid サブスクリプション スキーマ。
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: f6e33171cbba65cfeaca49ab6a8954be8bb89acb
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199953"
---
# <a name="event-grid-subscription-schema"></a>Event Grid サブスクリプション スキーマ

Event Grid サブスクリプションを作成するには、 Create Event サブスクリプション操作に対して要求を送信します。 次の形式を使用します。

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

たとえば、`examplegroup` という名前のリソース グループに、`examplestorage` という名前のストレージ アカウントのイベント サブスクリプションを作成するには、次の形式を使用します。

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

イベント サブスクリプションの名前は、長さが 3 から 64 文字でなければならず、使用できるのは a から z、A から Z、0 から 9、"-" だけです。 この記事は、要求の本体のプロパティとスキーマについて説明します。
 
## <a name="event-subscription-properties"></a>イベント サブスクリプションのプロパティ

| プロパティ | 種類 | [説明] |
| -------- | ---- | ----------- |
| destination | object | エンドポイントを定義するオブジェクト。 |
| filter | object | イベントの種類をフィルター処理するための省略可能なフィールド。 |

### <a name="destination-object"></a>対象オブジェクト

| プロパティ | 種類 | 説明 |
| -------- | ---- | ----------- |
| endpointType | string | サブスクリプションのエンドポイントの種類 (webhook/HTTP、イベント ハブ、またはキュー)。 | 
| endpointUrl | string | このイベント サブスクリプションのイベントの送信先 URL。 | 

### <a name="filter-object"></a>フィルター オブジェクト

| プロパティ | 種類 | 説明 |
| -------- | ---- | ----------- |
| includedEventTypes | array | イベント メッセージのイベントの種類がこれらのイベントの種類名のいずれかと完全に一致した場合に、適合します。 イベント名がイベント ソースに対して登録済みのイベントの種類名と一致しない場合は、エラーを発生させます。 既定では、すべてのイベントの種類と一致します。 |
| subjectBeginsWith | string | イベント メッセージの件名フィールドに対する、プレフィックス一致のフィルター。 既定値または空の文字列はすべて一致します。 | 
| subjectEndsWith | string | イベント メッセージの件名フィールドに対する、サフィックス一致のフィルター。 既定値または空の文字列はすべて一致します。 |
| isSubjectCaseSensitive | string | フィルターの大文字小文字の一致を制御します。 |
| enableAdvancedFilteringOnArrays | boolean | 高度なフィルター処理でキーの配列を使用できるようにします。 詳細については、[高度なフィルター処理](event-filtering.md#advanced-filtering)に関するページを参照してください。 |


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
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
