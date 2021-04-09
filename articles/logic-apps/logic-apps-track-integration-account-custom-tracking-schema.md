---
title: B2B メッセージのカスタム トラッキング スキーマ
description: Azure Logic Apps で B2B メッセージを監視するためのカスタム追跡スキーマを作成する
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76903057"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Azure Logic Apps でエンド ツー エンドのワークフローを監視するカスタム追跡スキーマを作成する

Azure Logic Apps には、ワークフローの一部に対して有効にできる組み込みの追跡機能があります。 ロジック アプリ、BizTalk Server、SQL Server、またはその他のレイヤーを含むワークフローなどのワークフローの最初から最後までのイベントをログに記録することができるカスタム追跡を設定できます。 この記事では、ロジック アプリの外部レイヤーで使用できるカスタム コードを示します。

## <a name="custom-tracking-schema"></a>カスタム追跡スキーマ

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| プロパティ | 必須 | Type | 説明 |
|----------|----------|------|-------------|
| sourceType | はい | String | 次の許可された値を持つ実行ソースの種類: `Microsoft.Logic/workflows`、`custom` |
| source | はい | 文字列または JToken | ソースのタイプが `Microsoft.Logic/workflows` である場合、このスキーマの後にソース情報を続ける必要があります。 ソースのタイプが `custom` である場合、スキーマは JToken です。 |
| systemId | はい | String | ロジック アプリのシステム ID |
| runId | はい | String | ロジック アプリの実行 ID |
| operationName | はい | String | 操作の名前 (アクションやトリガーなど) |
| repeatItemScopeName | はい | String | アクションが `foreach` または `until` ループ内にある場合の繰り返し項目名 |
| repeatItemIndex | はい | Integer | アクションが `foreach` または `until` ループ内にあり、繰り返される項目のインデックス番号を示します |
| trackingId | いいえ | String | 追跡 ID (メッセージを関連付けるために使用します) |
| correlationId | いいえ | String | 関連付け ID (メッセージを関連付けるために使用します) |
| clientRequestId | いいえ | String | クライアントがメッセージを関連付けるためにこのプロパティを設定できます |
| eventLevel | はい | String | イベントのレベル |
| eventTime | はい | DateTime | イベントの時刻 (*YYYY-MM-DDTHH:MM:SS.00000Z* という UTC 形式で示します) |
| recordType | はい | String | この許可された値のみを持つトラックレコードの種類: `custom` |
| レコード (record) | はい | JToken | JToken 形式のみのカスタムレコードの種類 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B プロトコル追跡スキーマ

B2B プロトコル追跡スキーマ について詳しくは、次のページをご覧ください。

* [AS2 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 の追跡スキーマ](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログで B2B メッセージを監視する](../logic-apps/monitor-b2b-messages-log-analytics.md)方法についての詳細について説明します