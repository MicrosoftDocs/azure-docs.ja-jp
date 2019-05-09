---
title: B2B メッセージのカスタム追跡スキーマ - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps と Enterprise Integration Pack の統合アカウントで B2B メッセージを監視するカスタム追跡スキーマを作成します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: f919e9a7cca210fa5920bcc6bed05a9a41fba8bf
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192386"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Azure Logic Apps でエンド ツー エンドのワークフローを監視するカスタム追跡スキーマを作成する

企業間取引のワークフローにおけるさまざまな部分について、AS2 や X12 メッセージの追跡など、組み込みの追跡を有効にすることができます。 ロジック アプリ、BizTalk Server、SQL Server、またはその他のレイヤーを含むワークフローを作成する場合は、カスタムの追跡を有効にして、ワークフローの最初から最後までのイベントをログに記録することができます。 

この記事では、ロジック アプリの外部レイヤーで使用できるカスタム コードを示します。 

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
         "repeatItemIndex": "",
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
         "record": {                
         }
      }
   ]
}
```

| プロパティ | type | 説明 |
| --- | --- | --- |
| sourceType |   | 実行ソースのタイプ。 許可されている値は、**Microsoft.Logic/workflows** と **custom** です。 (必須) |
| ソース |   | ソースのタイプが **Microsoft.Logic/workflows** である場合は、このスキーマの後にソース情報を続ける必要があります。 ソースのタイプが **custom** の場合、スキーマは JToken です。 (必須) |
| systemId | String | ロジック アプリのシステム ID。 (必須) |
| runId | String | ロジック アプリの実行 ID。 (必須) |
| operationName | String | 操作の名前 (アクションやトリガーなど)。 (必須) |
| repeatItemScopeName | String | アクションが `foreach`/`until` ループ内にある場合の繰り返し項目名。 (必須) |
| repeatItemIndex | 整数 | アクションが `foreach`/`until` ループ内にあるかどうか。 繰り返される項目のインデックスを示します。 (必須) |
| trackingId | String | 追跡 ID (メッセージを関連付けるために使用します)。 (省略可能) |
| correlationId | String | 関連付け ID (メッセージを関連付けるために使用します)。 (省略可能) |
| clientRequestId | String | クライアントがメッセージを関連付けるために設定できます。 (省略可能) |
| eventLevel |   | イベントのレベル。 (必須) |
| eventTime |   | イベントの時刻 (YYYY-MM-DDTHH:MM:SS.00000Z という UTC 形式で示します)。 (必須) |
| recordType |   | 追跡レコードのタイプ。 許可されている値は **custom** です。 (必須) |
| record |   | カスタム レコード タイプ。 許可されている形式は、JToken です。 (必須) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B プロトコル追跡スキーマ

B2B プロトコル追跡スキーマ について詳しくは、次のページをご覧ください。

* [AS2 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12 の追跡スキーマ](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>次の手順

* [B2B メッセージの監視方法](logic-apps-monitor-b2b-message.md)についての詳細情報
* [Azure Monitor ログでの B2B メッセージの追跡](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)について学習します