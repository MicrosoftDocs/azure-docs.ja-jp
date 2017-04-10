---
title: "Logic App の Webhook コネクタ | Microsoft Docs"
description: "配列のフィルター処理などのアクションを実行するための Webhook アクションおよびトリガーの概要。"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c0edbe421050ad46f6d31fd6416df4b344b233ad
ms.openlocfilehash: 2c24699f6253fc7952b331ebcce4b937f1c83603
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-webhook-connector"></a>Webhook コネクタの概要
Webhook アクションおよびトリガーを使用すると、以下を実現するためにフローをトリガー、一時停止、再開できます。

* 項目を受信するとすぐに [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) からトリガーする
* 承認を待ってからワークフローを続行する

Webhook のサブスクライブをサポートする API の作成については、 [Logic Apps のコネクタの作成に関するこちらの記事](../logic-apps/logic-apps-create-api-app.md)をご覧ください。

- - -
## <a name="use-the-webhook-trigger"></a>Webhook トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 [トリガーの詳細についてはこちらを参照してください](connectors-overview.md)。  Webhook トリガーは、イベントが発生するとすぐにロジック アプリが起動する[要求トリガー](connectors-native-reqres.md)と同様に、新しい項目のポーリングに依存しないので特に便利です。  Webhook トリガーでは、必要に応じてロジック アプリの起動に使用できるサービスに "コールバック URL" を登録することでこれを実行します。**

ロジック アプリ デザイナーで HTTP トリガーをセットアップする方法の例を次に示します。  これは、 [Logic Apps で使用される Webhook のサブスクライブ/サブスクライブ解除パターン](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)に従う API を既にデプロイしているか、その API にアクセスしていることを前提としています。  サブスクライブ呼び出しは、ロジック アプリが新しい Webhook と共に保存されるか、無効から有効に切り替えられるたびに実行されます。  サブスクライブ解除呼び出しは、ロジック アプリの Webhook トリガーが削除されて保存されるか、有効から無効に切り替えられるたびに実行されます。

1. ロジック アプリでの最初の手順として、 **HTTP Webhook** トリガーを追加します。
2. Webhook のサブスクライブ呼び出しとサブスクライブ解除呼び出しのパラメーターを入力します。

   * これは、[HTTP アクション](connectors-native-http.md) 形式と同じパターンに従います。

     ![HTTP トリガー](./media/connectors-native-webhook/using-trigger.png)
3. 1 つ以上のアクションを追加します。
4. [保存] をクリックしてロジック アプリを発行します。これにより、このロジック アプリをトリガーするために必要なコールバック URL を使用して、サブスクライブ エンドポイントが呼び出されます。
5. サービスがコールバック URL に対して `HTTP POST` を実行するたびに、ロジック アプリが起動します (また、ロジック アプリには、要求で渡されたすべてのデータが含まれます)。

## <a name="use-the-webhook-action"></a>Webhook アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 [アクションの詳細についてはこちらを参照してください。](connectors-overview.md)  Webhook アクションは、をサービスに登録し、その URL が呼び出されるまで待機してから再開するので特に便利です。**  ["承認電子メールの送信"](connectors-create-api-office365-outlook.md) は、このパターンに従うコネクタの一例です。  Webhook アクションを使用することで、このパターンをどのサービスにも適用できます。  これは、 [Logic Apps で使用される Webhook のサブスクライブ/サブスクライブ解除パターン](../logic-apps/logic-apps-create-api-app.md#webhook-actions)に従う API を既にデプロイしているか、その API にアクセスしていることを前提としています。  ロジック アプリが Webhook アクションを実行すると、サブスクライブ呼び出しが実行されます。  応答を待っている間またはロジック アプリが実行タイムアウトになる前に実行がキャンセルされると、サブスクライブ解除呼び出しが実行されます。

Webhook アクションを追加するには、次の手順に従います。

1. **[新しいステップ]** をクリックします。
2. **[アクションの追加]**
3. アクションの検索ボックスに「webhook」と入力して、 **[HTTP Webhook]** アクションを表示します。

    ![クエリ アクションの選択](./media/connectors-native-webhook/using-action-1.png)
4. Webhook のサブスクライブ呼び出しとサブスクライブ解除呼び出しのパラメーターを入力します。

   * これは、[HTTP アクション](connectors-native-http.md)形式と同じパターンに従います。

     ![クエリ アクションの完了](./media/connectors-native-webhook/using-action-2.png)
   * ロジック アプリは、実行時に該当のステップに到達すると、サブスクライブ エンドポイントを呼び出します。
5. ツール バーの左上隅にある [保存] をクリックすると、ロジック アプリが保存および発行 (アクティブ化) されます。

- - -
## <a name="technical-details"></a>技術的な詳細
Webhook でサポートされているトリガーとアクションの詳細を以下に示します。

## <a name="webhook-triggers"></a>Webhook トリガー
トリガーとは、ワークフローを開始するための操作です。 [トリガーの詳細についてはこちらを参照してください。](connectors-overview.md) このコネクタにはトリガー 1 つあります。

| アクションを表示します。 | 説明 |
| --- | --- |
| HTTP Webhook |コールバック URL をサービスにサブスクライブします。サービスは、必要に応じてこの URL を呼び出してロジック アプリを起動することができます。 |

### <a name="trigger-details"></a>トリガーの詳細
Webhook コネクタには、使用可能なトリガーが 1 つ用意されています。 アクションの情報、必須および省略可能な入力フィールド、アクションの使用に関連した対応する出力の詳細を次に示します。

#### <a name="http-webhook"></a>HTTP Webhook
コールバック URL をサービスにサブスクライブします。サービスは、必要に応じてこの URL を呼び出してロジック アプリを起動することができます。
* は必須フィールドを意味します。

| 表示名 | プロパティ名 | 説明 |
| --- | --- | --- |
| Subscribe Method (メソッドのサブスクライブ)* |method |サブスクライブ要求に使用する HTTP メソッド |
| Subscribe URI (URI のサブスクライブ)* |uri |サブスクライブ要求に使用する HTTP URI |
| Unsubscribe Method (メソッドのサブスクライブ解除)* |method |サブスクライブ解除要求に使用する HTTP メソッド |
| Unsubscribe URI (URI のサブスクライブ解除)* |uri |サブスクライブ解除要求に使用する HTTP URI |
| Subscribe Body (本文のサブスクライブ) |body |サブスクライブの HTTP 要求本文 |
| Subscribe Headers (ヘッダーのサブスクライブ) |headers |サブスクライブの HTTP 要求ヘッダー |
| Subscribe Authentication (認証のサブスクライブ) |authencation |サブスクライブに使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |
| Unsubscribe Body (本文のサブスクライブ解除) |body |サブスクライブ解除の HTTP 要求本文 |
| Unsubscribe Headers (ヘッダーのサブスクライブ解除) |headers |サブスクライブ解除の HTTP 要求ヘッダー |
| Unsubscribe Authentication (認証のサブスクライブ解除) |authentication |サブスクライブ解除に使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |

<br>

**出力の詳細**

Webhook 要求

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| headers |オブジェクト |Webhook 要求ヘッダー |
| body |オブジェクト |Webhook 要求オブジェクト |
| 状態コード |int |Webhook 要求の状態コード |

## <a name="webhook-actions"></a>Webhook アクション
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 [アクションの詳細についてはこちらを参照してください。](connectors-overview.md) このコネクタには、使用可能なアクションが 1 つあります。

| アクションを表示します。 | 説明 |
| --- | --- |
| HTTP Webhook |コールバック URL をサービスにサブスクライブします。サービスは、必要に応じてこの URL を呼び出してワークフロー ステップを再開できます。 |

### <a name="action-details"></a>アクションの詳細
Webhook コネクタには、使用可能なアクションが 1 つ用意されています。 アクションの情報、必須および省略可能な入力フィールド、アクションの使用に関連した対応する出力の詳細を次に示します。

#### <a name="http-webhook"></a>HTTP Webhook
コールバック URL をサービスにサブスクライブします。サービスは、必要に応じてこの URL を呼び出してワークフロー ステップを再開できます。
* は必須フィールドを意味します。

| 表示名 | プロパティ名 | 説明 |
| --- | --- | --- |
| Subscribe Method (メソッドのサブスクライブ)* |method |サブスクライブ要求に使用する HTTP メソッド |
| Subscribe URI (URI のサブスクライブ)* |uri |サブスクライブ要求に使用する HTTP URI |
| Unsubscribe Method (メソッドのサブスクライブ解除)* |method |サブスクライブ解除要求に使用する HTTP メソッド |
| Unsubscribe URI (URI のサブスクライブ解除)* |uri |サブスクライブ解除要求に使用する HTTP URI |
| Subscribe Body (本文のサブスクライブ) |body |サブスクライブの HTTP 要求本文 |
| Subscribe Headers (ヘッダーのサブスクライブ) |headers |サブスクライブの HTTP 要求ヘッダー |
| Subscribe Authentication (認証のサブスクライブ) |authencation |サブスクライブに使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |
| Unsubscribe Body (本文のサブスクライブ解除) |body |サブスクライブ解除の HTTP 要求本文 |
| Unsubscribe Headers (ヘッダーのサブスクライブ解除) |headers |サブスクライブ解除の HTTP 要求ヘッダー |
| Unsubscribe Authentication (認証のサブスクライブ解除) |authentication |サブスクライブ解除に使用する HTTP 認証  (詳細については、[HTTP コネクタ](connectors-native-http.md#authentication)に関する記事を参照) |

<br>

**出力の詳細**

Webhook 要求

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| headers |オブジェクト |Webhook 要求ヘッダー |
| body |オブジェクト |Webhook 要求オブジェクト |
| 状態コード |int |Webhook 要求の状態コード |

- - -
## <a name="next-steps"></a>次のステップ
ロジック アプリに取り組む方法とコミュニティの詳細を次に示します。

## <a name="create-a-logic-app"></a>ロジック アプリを作成します
プラットフォームを試用し、 [ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md) してください。 [API リスト](apis-list.md)を参照すると、ロジック アプリで使用可能な他のコネクタについて確認できます。

