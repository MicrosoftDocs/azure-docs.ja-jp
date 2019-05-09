---
title: 要求アクションと応答アクションの使用 | Microsoft Docs
description: Azure ロジック アプリの要求および応答のトリガーとアクションの概要
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 0f6ee8729cbed9cb8baf3668f7b1a332bc5eddc1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58892828"
---
# <a name="get-started-with-the-request-and-response-components"></a>要求コンポーネントと応答コンポーネントの概要
ロジック アプリで要求コンポーネントと応答コンポーネントを使用すると、リアルタイムでイベントに応答できます。

たとえば、次のようなことができます。

* ロジック アプリから、オンプレミスのデータベースのデータを使用して HTTP 要求に応答します。
* 外部の webhook イベントからロジック アプリをトリガーします。
* 他のロジック アプリ内からの要求アクションと応答アクションを使用してロジック アプリを呼び出します。

ロジック アプリでの要求アクションと応答アクションの使用を開始する方法については、 [ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事をご覧ください。

## <a name="use-the-http-request-trigger"></a>HTTP 要求トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 
トリガーの詳細については[こちら](../connectors/apis-list.md)を参照してください。

ロジック アプリ デザイナーで HTTP 要求をセットアップする方法の例を次に示します。

1. ロジック アプリに **[要求 - HTTP 要求の受信時]** トリガーを追加します。 必要に応じて、要求本文に JSON スキーマを指定できます ( [JSONSchema.net](https://jsonschema.net)などのツールを使用します)。 これにより、デザイナーで HTTP 要求のプロパティのトークンを生成できます。
2. ロジック アプリを保存できるように別のアクションを追加します。
3. ロジック アプリの保存後、要求カードから HTTP 要求の URL を取得できます。
4. URL への HTTP POST ( [Postman](https://www.getpostman.com/)などのツールを使用できます) によってロジック アプリがトリガーされます。

> [!NOTE]
> 応答アクションを定義していない場合は、 `202 ACCEPTED` 応答がすぐに呼び出し元に返されます。 応答アクションを使用すると、応答をカスタマイズできます。
> 
> 

![Response Trigger](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>HTTP 応答アクションの使用
HTTP 応答アクションは、HTTP 要求によってトリガーされたワークフローで使用した場合にのみ有効です。 応答アクションを定義していない場合は、 `202 ACCEPTED` 応答がすぐに呼び出し元に返されます。  応答アクションは、ワークフロー内のどのステップにでも追加できます。 ロジック アプリは、応答のために受信要求を 1 分間だけオープンしたままにします。  ワークフローから応答が送信されなかった場合は、(応答アクションが定義内に存在していても) 1 分後に `504 GATEWAY TIMEOUT` が呼び出し元に返されます。

HTTP 応答アクションの追加方法を次に示します。

1. **[新しいステップ]** をクリックします。
2. **[アクションの追加]** を選択します。
3. アクションの検索ボックスに「 **応答** 」と入力して、応答アクションの一覧を表示します。
   
    ![Select the response action](./media/connectors-native-reqres/using-action-1.png)
4. HTTP 応答メッセージに必要なすべてのパラメーターを追加します。
   
    ![Complete the response action](./media/connectors-native-reqres/using-action-2.png)
5. ツール バーの左上隅にある [保存] をクリックすると、ロジック アプリが保存されて発行 (アクティブ化) されます。

## <a name="request-trigger"></a>Request トリガー
ここでは、このコネクタでサポートされているトリガーの詳細について説明します。 1 つの要求トリガーがあります。

| トリガー | 説明 |
| --- | --- |
| Request |HTTP 要求を受信したときに実行されます。 |

## <a name="response-action"></a>応答アクション
ここでは、このコネクタでサポートされているアクションの詳細について説明します。 要求トリガーに伴う場合にのみ使用可能な応答アクションが 1 つあります。

| Action | 説明 |
| --- | --- |
| Response |関連する HTTP 要求に応答を返します。 |

### <a name="trigger-and-action-details"></a>トリガーとアクションの詳細
次の表に、トリガーとアクションの必須および省略可能な入力フィールドと、対応する出力の詳細を示します。

#### <a name="request-trigger"></a>要求トリガー
受信 HTTP 要求からのトリガーの入力フィールドを次に示します。

| Display name | プロパティ名 | 説明 |
| --- | --- | --- |
| JSON スキーマ |schema |HTTP 要求本文の JSON スキーマ |

<br>

**出力の詳細**

要求の出力の詳細を次に示します。

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| headers |object |要求ヘッダー |
| 本文 |object |要求オブジェクト |

#### <a name="response-action"></a>応答アクション
HTTP 応答アクションの入力フィールドを次に示します。 \* は、必須フィールドであることを示しています。

| Display name | プロパティ名 | 説明 |
| --- | --- | --- |
| 状態コード* |StatusCode |HTTP 状態コード |
| headers |headers |含める任意の応答ヘッダーの JSON オブジェクト |
| 本文 |body |応答本文 |

## <a name="next-steps"></a>次の手順
プラットフォームを試用し、 [ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)してください。 [API リスト](apis-list.md)を参照すると、Logic Apps で使用可能な他のコネクタについて確認できます。

