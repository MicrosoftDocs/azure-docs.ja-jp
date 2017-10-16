---
title: "Azure Logic Apps の webhook コネクタ | Microsoft Docs"
description: "webhook アクションおよびトリガーを使用して、ロジック アプリからフィルター配列などのアクションを実行する方法"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: fbfef291334109c6dcfcde80741874549fb7929f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-webhook-connector"></a>Webhook コネクタの概要

webhook アクションおよびトリガーを使用すると、フローを開始、一時停止、および再開して、次のようなタスクを実行できます。

* 項目を受信したら [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) からトリガーする
* 承認を待ってからワークフローを続行する

詳細については、「[webhook をサポートするカスタム API を作成する方法](../logic-apps/logic-apps-create-api-app.md)」を参照してください。

## <a name="use-the-webhook-trigger"></a>Webhook トリガーの使用

[*トリガー*](connectors-overview.md)は、ロジック アプリ ワークフローを開始するイベントです。 Webhook トリガーは、イベントに基づいており、新しい項目のポーリングに依存しません。 [要求トリガー](connectors-native-reqres.md)と同様に、ロジック アプリは、イベントが発生するとすぐに起動します。 webhook トリガーは、*コールバック URL* をサービスに登録し、必要に応じて、その URL を使用してロジック アプリを起動します。

ロジック アプリ デザイナーで HTTP トリガーをセットアップする方法の例を次に示します。 これらの手順では、[ロジック アプリでの webhook のサブスクライブおよびサブスクライブ解除パターン](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)に従う API を既にデプロイしているか、その API にアクセスしていることを前提としています。 サブスクライブ呼び出しは、ロジック アプリが新しい Webhook と共に保存されるか、無効から有効に切り替えられるたびに実行されます。 サブスクライブ解除呼び出しは、ロジック アプリの webhook トリガーが削除されて保存されるか、有効から無効に切り替えられたときに実行されます。

**webhook トリガーを追加するには、次の手順に従います。**

1. ロジック アプリでの最初の手順として、**HTTP Webhook** トリガーを追加します。
2. webhook サブスクライブ呼び出しおよびサブスクライブ解除呼び出しのパラメーターを入力します。

   この手順は、[HTTP アクション](connectors-native-http.md)形式と同じパターンに従います。

     ![HTTP トリガー](./media/connectors-native-webhook/using-trigger.png)

3. 1 つ以上のアクションを追加します。
4. **[保存]** をクリックして、ロジック アプリを発行します。 これにより、このロジック アプリをトリガーするために必要なコールバック URL を使用して、サブスクライブ エンドポイントが呼び出されます。
5. サービスがコールバック URL に対して `HTTP POST` を実行するたびに、ロジック アプリが起動します。また、ロジック アプリには、要求に渡されたすべてのデータが含まれます。

## <a name="use-the-webhook-action"></a>Webhook アクションの使用

[*アクション*](connectors-overview.md)とは、ロジック アプリで定義されたワークフローによって実行される操作です。 webhook アクションは、*コールバック URL* をサービスに登録して、その URL が呼び出されるまで待機してから再開します。 ["承認電子メールの送信"](connectors-create-api-office365-outlook.md) は、このパターンに従うコネクタの一例です。 Webhook アクションを使用することで、このパターンをどのサービスにも適用できます。 

ロジック アプリ デザイナーで webhook アクションをセットアップする方法の例を次に示します。 これらの手順では、[ロジック アプリでの webhook のサブスクライブおよびサブスクライブ解除パターン](../logic-apps/logic-apps-create-api-app.md#webhook-actions)に従う API を既にデプロイしているか、その API にアクセスしていることを前提としています。 サブスクライブ呼び出しは、ロジック アプリが Webhook アクションを実行すると実行されます。 サブスクライブ解除呼び出しは、応答を待っている間に、またはロジック アプリがタイムアウトになる前に実行がキャンセルされると実行されます。

**webhook アクションを追加するには、次の手順に従います。**

1. **[新しいステップ]** > **[アクションの追加]** の順に選択します。

2. 検索ボックスに "webhook" と入力して、**[HTTP Webhook]** アクションを検索します。

    ![クエリ アクションの選択](./media/connectors-native-webhook/using-action-1.png)

3. Webhook のサブスクライブ呼び出しとサブスクライブ解除呼び出しのパラメーターを入力します。

   この手順は、[HTTP アクション](connectors-native-http.md)形式と同じパターンに従います。

     ![クエリ アクションの完了](./media/connectors-native-webhook/using-action-2.png)
   
   実行時、ロジック アプリは、該当するステップを完了すると、サブスクライブ エンドポイントを呼び出します。

4. **[保存]** をクリックして、ロジック アプリを発行します。

## <a name="technical-details"></a>技術的な詳細

webhook でサポートされているトリガーとアクションの詳細を以下に示します。

## <a name="webhook-triggers"></a>Webhook トリガー

| アクション | 説明 |
| --- | --- |
| HTTP Webhook |コールバック URL をサービスにサブスクライブします。サービスは、必要に応じてこの URL を呼び出してロジック アプリを起動することができます。 |

### <a name="trigger-details"></a>トリガーの詳細

#### <a name="http-webhook"></a>HTTP Webhook

コールバック URL をサービスにサブスクライブします。サービスは、必要に応じてこの URL を呼び出してロジック アプリを起動することができます。
\* は必須フィールドを意味します。

| 表示名 | プロパティ名 | 説明 |
| --- | --- | --- |
| Subscribe Method (メソッドのサブスクライブ)* |method |サブスクライブ要求に使用する HTTP メソッド |
| Subscribe URI (URI のサブスクライブ)* |uri |サブスクライブ要求に使用する HTTP URI |
| Unsubscribe Method (メソッドのサブスクライブ解除)* |method |サブスクライブ解除要求に使用する HTTP メソッド |
| Unsubscribe URI (URI のサブスクライブ解除)* |uri |サブスクライブ解除要求に使用する HTTP URI |
| Subscribe Body (本文のサブスクライブ) |body |サブスクライブの HTTP 要求本文 |
| Subscribe Headers (ヘッダーのサブスクライブ) |headers |サブスクライブの HTTP 要求ヘッダー |
| Subscribe Authentication (認証のサブスクライブ) |authentication |サブスクライブに使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |
| Unsubscribe Body (本文のサブスクライブ解除) |body |サブスクライブ解除の HTTP 要求本文 |
| Unsubscribe Headers (ヘッダーのサブスクライブ解除) |headers |サブスクライブ解除の HTTP 要求ヘッダー |
| Unsubscribe Authentication (認証のサブスクライブ解除) |authentication |サブスクライブ解除に使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |

**出力の詳細**

Webhook 要求

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| headers |オブジェクト |Webhook 要求ヘッダー |
| body |オブジェクト |Webhook 要求オブジェクト |
| 状態コード |int |Webhook 要求の状態コード |

## <a name="webhook-actions"></a>Webhook アクション

| アクション | 説明 |
| --- | --- |
| HTTP Webhook |コールバック URL をサービスにサブスクライブします。サービスは、必要に応じてこの URL を呼び出してワークフロー ステップを再開できます。 |

### <a name="action-details"></a>アクションの詳細

#### <a name="http-webhook"></a>HTTP Webhook

コールバック URL をサービスにサブスクライブします。サービスは、必要に応じてこの URL を呼び出してワークフロー ステップを再開できます。
\* は必須フィールドを意味します。

| 表示名 | プロパティ名 | 説明 |
| --- | --- | --- |
| Subscribe Method (メソッドのサブスクライブ)* |method |サブスクライブ要求に使用する HTTP メソッド |
| Subscribe URI (URI のサブスクライブ)* |uri |サブスクライブ要求に使用する HTTP URI |
| Unsubscribe Method (メソッドのサブスクライブ解除)* |method |サブスクライブ解除要求に使用する HTTP メソッド |
| Unsubscribe URI (URI のサブスクライブ解除)* |uri |サブスクライブ解除要求に使用する HTTP URI |
| Subscribe Body (本文のサブスクライブ) |body |サブスクライブの HTTP 要求本文 |
| Subscribe Headers (ヘッダーのサブスクライブ) |headers |サブスクライブの HTTP 要求ヘッダー |
| Subscribe Authentication (認証のサブスクライブ) |authentication |サブスクライブに使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |
| Unsubscribe Body (本文のサブスクライブ解除) |body |サブスクライブ解除の HTTP 要求本文 |
| Unsubscribe Headers (ヘッダーのサブスクライブ解除) |headers |サブスクライブ解除の HTTP 要求ヘッダー |
| Unsubscribe Authentication (認証のサブスクライブ解除) |authentication |サブスクライブ解除に使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |

**出力の詳細**

Webhook 要求

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| headers |オブジェクト |Webhook 要求ヘッダー |
| body |オブジェクト |Webhook 要求オブジェクト |
| 状態コード |int |Webhook 要求の状態コード |

## <a name="next-steps"></a>次のステップ

* [ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)
* [他のコネクタを見つけます](apis-list.md)