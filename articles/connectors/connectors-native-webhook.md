---
title: イベント ベースのワークフローまたはアクションを作成する - Azure Logic Apps | Microsoft Docs
description: Webhook と Azure Logic Apps を使ってイベント ベースのワークフローまたはアクションを自動化します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895581"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Webhook と Azure Logic Apps を使ってイベント ベースのワークフローまたはアクションを作成する

webhook アクションおよびトリガーを使用すると、フローを開始、一時停止、および再開して、次のようなタスクを実行できます。

* 項目を受信したら [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) からトリガーする
* 承認を待ってからワークフローを続行する

詳細については、「[webhook をサポートするカスタム API を作成する方法](../logic-apps/logic-apps-create-api-app.md)」を参照してください。

## <a name="use-the-webhook-trigger"></a>Webhook トリガーの使用

[*トリガー*](../connectors/apis-list.md)は、ロジック アプリ ワークフローを開始するイベントです。 Webhook トリガーはイベントに基づいており、新しい項目のポーリングに依存しません。 Webhook トリガーを使用してロジック アプリを保存したり、ロジック アプリを無効から有効に変更したりすると、Webhook トリガーは、指定されたサービスまたはエンドポイントに*コールバック URL* を登録して、そのサービスまたはエンドポイントを*サブスクライブ*します。 次に、トリガーは必要に応じてその URL を使用してロジック アプリを実行します。 [要求トリガー](connectors-native-reqres.md)と同様に、ロジック アプリは、予期されたイベントが発生するとすぐに起動します。 トリガーを削除してロジック アプリを保存した場合や、ロジック アプリを有効から無効に変更すると、トリガーは*サブスクライブを解除*します。

ロジック アプリ デザイナーで HTTP トリガーをセットアップする方法の例を次に示します。 これらの手順では、[ロジック アプリでの webhook のサブスクライブおよびサブスクライブ解除パターン](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)に従う API を既にデプロイしているか、その API にアクセスしていることを前提としています。 

**webhook トリガーを追加するには、次の手順に従います。**

1. ロジック アプリでの最初の手順として、**HTTP Webhook** トリガーを追加します。
2. webhook サブスクライブ呼び出しおよびサブスクライブ解除呼び出しのパラメーターを入力します。

   この手順は、[HTTP アクション](connectors-native-http.md)形式と同じパターンに従います。

     ![HTTP トリガー](./media/connectors-native-webhook/using-trigger.png)

3. 1 つ以上のアクションを追加します。
4. **[保存]** をクリックして、ロジック アプリを発行します。 これにより、このロジック アプリをトリガーするために必要なコールバック URL を使用して、サブスクライブ エンドポイントが呼び出されます。
5. サービスがコールバック URL に対して `HTTP POST` を実行するたびに、ロジック アプリが起動します。また、ロジック アプリには、要求に渡されたすべてのデータが含まれます。

## <a name="use-the-webhook-action"></a>Webhook アクションの使用

[*アクション*](../connectors/apis-list.md)とは、ロジック アプリのワークフローによって定義されて実行される操作です。 ロジック アプリが Webhook アクションを実行すると、そのアクションは、指定されたサービスまたはエンドポイントに*コールバック URL* を登録することにより、そのサービスまたはエンドポイントを*サブスクライブ*します。 Webhook アクションは、ロジック アプリが実行を再開する前に、そのサービスが URL を呼び出すまで待機します。 次のような場合、ロジック アプリはサービスまたはエンドポイントのサブスクライブを解除します。 

* Webhook アクションが正常に終了したとき
* 応答を待機しているときにロジック アプリの実行がキャンセルされた場合
* ロジック アプリがタイムアウトになる前

たとえば、[**承認メールの送信**](connectors-create-api-office365-outlook.md)アクションは、このパターンに従う Webhook アクションの例です。 Webhook アクションを使用することで、このパターンをどのサービスにも適用できます。 

ロジック アプリ デザイナーで webhook アクションをセットアップする方法の例を次に示します。 これらの手順では、[ロジック アプリでの webhook のサブスクライブおよびサブスクライブ解除パターン](../logic-apps/logic-apps-create-api-app.md#webhook-actions)に従う API を既にデプロイしているか、その API にアクセスしていることを前提としています。 

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

| Action | 説明 |
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
| Subscribe Authentication (認証のサブスクライブ) |認証 |サブスクライブに使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |
| Unsubscribe Body (本文のサブスクライブ解除) |body |サブスクライブ解除の HTTP 要求本文 |
| Unsubscribe Headers (ヘッダーのサブスクライブ解除) |headers |サブスクライブ解除の HTTP 要求ヘッダー |
| Unsubscribe Authentication (認証のサブスクライブ解除) |authentication |サブスクライブ解除に使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |

**出力の詳細**

Webhook 要求

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| headers |object |Webhook 要求ヘッダー |
| 本文 |object |Webhook 要求オブジェクト |
| 状態コード |int |Webhook 要求の状態コード |

## <a name="webhook-actions"></a>Webhook アクション

| Action | 説明 |
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
| Subscribe Authentication (認証のサブスクライブ) |認証 |サブスクライブに使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |
| Unsubscribe Body (本文のサブスクライブ解除) |body |サブスクライブ解除の HTTP 要求本文 |
| Unsubscribe Headers (ヘッダーのサブスクライブ解除) |headers |サブスクライブ解除の HTTP 要求ヘッダー |
| Unsubscribe Authentication (認証のサブスクライブ解除) |authentication |サブスクライブ解除に使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |

**出力の詳細**

Webhook 要求

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| headers |object |Webhook 要求ヘッダー |
| 本文 |object |Webhook 要求オブジェクト |
| 状態コード |int |Webhook 要求の状態コード |

## <a name="next-steps"></a>次の手順

* [ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [他のコネクタを見つけます](apis-list.md)
