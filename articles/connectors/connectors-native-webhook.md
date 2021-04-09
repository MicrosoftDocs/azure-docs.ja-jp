---
title: 待機してイベントに応答する
description: Azure Logic Apps を使用して、サービス エンドポイントでのイベントに基づいてトリガー、一時停止、および再開するワークフローを自動化します
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: 7c6f3c4e3e4a2a29fe6a02c03043e3dfb81a2010
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89227901"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Azure Logic Apps で HTTP Webhook を使用して、自動化されたイベントベースのワークフローを作成して実行する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) および組み込みの HTTP Webhook コネクタを使用すると、サービス エンドポイントをサブスクライブし、特定のイベントを待機し、それらのイベントに基づいて実行する自動化されたタスクとワークフローを作成でき、そのエンドポイントを定期的に確認または *ポーリング* する必要はありません。

Webhook ベースのワークフローの例を次に示します。

* [Azure Event Hub](https://github.com/logicappsio/EventHubAPI)からアイテムが到着するのを待ってからロジック アプリの実行をトリガーする。
* 承認を待ってからワークフローを続行する。

この記事では、ロジック アプリがサービス エンドポイントでのイベントを受信してそれに応答できるように Webhook トリガーと Webhook アクションを使用する方法について説明します。

## <a name="how-do-webhooks-work"></a>Webhook のしくみ

Webhook トリガーは、イベントに基づいており、新しい項目の定期的な確認 (ポーリング) に依存しません。 Webhook トリガーで開始するロジック アプリを保存したり、ロジック アプリを無効から有効に変更したりすると、Webhook トリガーは、指定されたサービス エンドポイントに "*コールバック URL*" を登録して、そのエンドポイントを "*サブスクライブ*" します。 その後、このトリガーは、そのサービス エンドポイントが URL を呼び出すまで待ち、その結果、ロジック アプリの実行が開始されます。 [要求トリガー](connectors-native-reqres.md)と同様に、ロジック アプリは、指定されたイベントが発生するとすぐに起動します。 Webhook トリガーを削除してロジック アプリを保存したり、ロジック アプリを有効から無効に変更したりすると、トリガーはサービスまたはエンドポイントから "*サブスクライブを解除*" します。

Webhook アクションもイベントに基づいており、指定されたサービス エンドポイントに "*コールバック URL*" を登録することで、そのエンドポイントを "*サブスクライブ*" します。 Webhook アクションがロジック アプリのワークフローを一時停止し、サービス エンドポイントが URL を呼び出すまで待った後、ロジック アプリの実行が再開されます。 次のような場合、Webhook アクションはサービス エンドポイントの "*サブスクライブを解除*" します。

* Webhook アクションが正常に終了したとき
* 応答を待機しているときにロジック アプリの実行がキャンセルされた場合
* ロジック アプリがタイムアウトになる前

たとえば、Office 365 Outlook コネクタの [**承認メールの送信**](connectors-create-api-office365-outlook.md)アクションは、このパターンに従う Webhook アクションの一例です。 Webhook アクションを使用することで、このパターンをあらゆるサービスに適用できます。

詳細については、以下のトピックを参照してください。

* [Webhook とサブスクリプション](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Webhook をサポートするカスタム API を作成する](../logic-apps/logic-apps-create-api-app.md)

[トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) (以前の Secure Sockets Layer (SSL)) や [Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml) などの、ロジック アプリへの受信呼び出しの暗号化、セキュリティ、認可については、[アクセスとデータのセキュリティ保護 - 要求ベースのトリガーへの受信呼び出しへのアクセス](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 必要に応じて[ロジック アプリの Webhook トリガー](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)または[ロジック アプリの Webhook アクション](../logic-apps/logic-apps-create-api-app.md#webhook-actions)のサブスクライブおよびサブスクライブ解除パターンをサポートする、既にデプロイ済みのエンドポイントまたは API の URL

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

* ターゲット エンドポイントで特定のイベントを待機するロジック アプリ。 HTTP Webhook トリガーで開始するには、[空のロジック アプリを作成します](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 HTTP Webhook アクションを使用するには、任意のトリガーで対象のロジック アプリを起動します。 この例では、最初のステップとして HTTP トリガーを使用します。

## <a name="add-an-http-webhook-trigger"></a>HTTP Webhook トリガーの追加

この組み込みトリガーは、ターゲット サービスでサブスクライブ エンドポイントを呼び出し、そのコールバック URL をターゲット サービスに登録します。 その後、ロジック アプリは、ターゲット サービスがコールバック URL に `HTTP POST` 要求を送信するのを待ちます。 このイベントが発生すると、トリガーが起動され、要求内のすべてのデータがワークフローに渡されます。

1. [Azure portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーで空のロジック アプリを開きます。

1. デザイナーの検索ボックスに、フィルターとして「`http webhook`」と入力します。 **[トリガー]** の一覧から、 **[HTTP Webhook]** トリガーを選択します。

   ![HTTP Webhook トリガーを選択する](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   この例では、トリガー名を `HTTP Webhook trigger` に変更して、このステップにわかりやすい名前を付けています。 また、この例では後で HTTP Webhook アクションを追加します。どちらの名前も一意である必要があります。

1. サブスクライブおよびサブスクライブ解除の呼び出しに使用する [HTTP Webhook トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)の値を指定します。

   この例のトリガーには、サブスクライブおよびサブスクライブ解除の操作を実行するときに使用するメソッド、URI、およびメッセージ本文が含まれています。

   ![HTTP Webhook トリガー パラメーターを入力する](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **サブスクライブ - メソッド** | はい | ターゲット エンドポイントをサブスクライブするときに使用するメソッド |
   | **サブスクライブ - URI** | はい | ターゲット エンドポイントにサブスクライブするために使用する URL |
   | **サブスクライブ - 本文** | いいえ | サブスクライブ要求に含めるメッセージ本文。 この例には、ロジック アプリであるサブスクライバーを一意に識別するコールバック URL が含まれており、`@listCallbackUrl()` 式を使用してロジック アプリのコールバック URL を取得しています。 |
   | **サブスクライブ解除 - メソッド** | いいえ | ターゲット エンドポイントからサブスクライブ解除するときに使用するメソッド |
   | **サブスクライブ解除 - URI** | いいえ | ターゲット エンドポイントからサブスクライブ解除するために使用する URL |
   | **サブスクライブ解除 - 本文** | いいえ | サブスクライブ解除の要求に含めるオプションのメッセージ本文 <p><p>**注**:このプロパティは、`listCallbackUrl()` 関数の使用をサポートしていません。 ただし、トリガーによって自動的にヘッダー (`x-ms-client-tracking-id` および `x-ms-workflow-operation-name`) が含まれ、送信されます。このヘッダーは、ターゲット サービスがサブスクライバーを一意に識別するために使用されます。 |
   ||||

1. その他のトリガー プロパティを追加するには、 **[新しいパラメーターの追加]** の一覧を開きます。

   ![トリガー プロパティをさらに追加する](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   たとえば、認証を使用する必要がある場合は、**サブスクライブ - 認証** と **サブスクライブ解除 - 認証** プロパティを追加します。 HTTP Webhook に使用できる認証の種類の詳細については、[送信呼び出しへの認証の追加](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)に関するページを参照してください。

1. トリガーが起動したときに実行されるアクションを使用して、ロジック アプリのワークフローを引き続き構築します。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

   ロジック アプリを保存すると、ターゲット サービスでサブスクライブ エンドポイントが呼び出され、コールバック URL が登録されます。 その後、ロジック アプリは、ターゲット サービスがコールバック URL に `HTTP POST` 要求を送信するのを待ちます。 このイベントが発生すると、トリガーが起動され、要求内のすべてのデータがワークフローに渡されます。 この操作が正常に完了すると、トリガーはエンドポイントからサブスクライブ解除され、ロジック アプリでは残りのワークフローが続行されます。

## <a name="add-an-http-webhook-action"></a>HTTP Webhook アクションの追加

この組み込みアクションは、ターゲット サービスでサブスクライブ エンドポイントを呼び出し、そのコールバック URL をターゲット サービスに登録します。 その後、ロジック アプリは一時停止し、ターゲット サービスがコールバック URL に `HTTP POST` 要求を送信するのを待ちます。 このイベントが発生すると、アクションは要求内のすべてのデータをワークフローに渡します。 操作が正常に完了すると、アクションはエンドポイントからサブスクライブ解除され、ロジック アプリは残りのワークフローの実行を続行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーでロジック アプリを開きます。

   この例では、最初のステップとして HTTP Webhook トリガーを使用しています。

1. HTTP Webhook アクションを追加するステップで、 **[新しいステップ]** を選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. デザイナーの検索ボックスに、フィルターとして「`http webhook`」と入力します。 **[アクション]** の一覧で、 **[HTTP Webhook]** アクションを選択します。

   ![HTTP Webhook アクションを選択する](./media/connectors-native-webhook/select-http-webhook-action.png)

   この例では、このステップの名前がわかりやすくなるように、アクション名を "HTTP Webhook action" に変更します。

1. HTTP Webhook アクション パラメーターの値を指定します。これは、サブスクライブおよびサブスクライブ解除の呼び出しに使用する [HTTP Webhook トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)に似ています。

   この例のアクションには、サブスクライブおよびサブスクライブ解除の操作を実行するときに使用するメソッド、URI、およびメッセージ本文が含まれています。

   ![HTTP Webhook アクション パラメーターを入力する](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **サブスクライブ - メソッド** | はい | ターゲット エンドポイントをサブスクライブするときに使用するメソッド |
   | **サブスクライブ - URI** | はい | ターゲット エンドポイントにサブスクライブするために使用する URL |
   | **サブスクライブ - 本文** | いいえ | サブスクライブ要求に含めるメッセージ本文。 この例には、ロジック アプリであるサブスクライバーを一意に識別するコールバック URL が含まれており、`@listCallbackUrl()` 式を使用してロジック アプリのコールバック URL を取得しています。 |
   | **サブスクライブ解除 - メソッド** | いいえ | ターゲット エンドポイントからサブスクライブ解除するときに使用するメソッド |
   | **サブスクライブ解除 - URI** | いいえ | ターゲット エンドポイントからサブスクライブ解除するために使用する URL |
   | **サブスクライブ解除 - 本文** | いいえ | サブスクライブ解除の要求に含めるオプションのメッセージ本文 <p><p>**注**:このプロパティは、`listCallbackUrl()` 関数の使用をサポートしていません。 ただし、アクションによってヘッダー (`x-ms-client-tracking-id` および `x-ms-workflow-operation-name`) が自動的に送信されます。このヘッダーは、ターゲット サービスがサブスクライバーを一意に識別するために使用されます。 |
   ||||

1. その他のアクション プロパティを追加するには、 **[新しいパラメーターの追加]** の一覧を開きます。

   ![アクション プロパティをさらに追加する](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   たとえば、認証を使用する必要がある場合は、**サブスクライブ - 認証** と **サブスクライブ解除 - 認証** プロパティを追加します。 HTTP Webhook に使用できる認証の種類の詳細については、[送信呼び出しへの認証の追加](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)に関するページを参照してください。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

   これで、アクションが実行されるとターゲット サービスでサブスクライブ エンドポイントが呼び出され、コールバック URL が登録されます。 その後、ロジック アプリはワークフローを一時停止して、ターゲット サービスがコールバック URL に `HTTP POST` 要求を送信するのを待ちます。 このイベントが発生すると、アクションは要求内のすべてのデータをワークフローに渡します。 操作が正常に完了すると、アクションはエンドポイントからサブスクライブ解除され、ロジック アプリは残りのワークフローの実行を続行します。

## <a name="trigger-and-action-outputs"></a>トリガーとアクションの出力

ここでは、以下の情報を返す HTTP Webhook トリガーまたはアクションからの出力の詳細情報を示します。

| プロパティ名 | Type | 説明 |
|---------------|------|-------------|
| headers | object | 要求のヘッダー |
| body | object | JSON オブジェクト | 要求の本文の内容を含むオブジェクト |
| status code | INT | 要求の状態コード |
|||

| status code | 説明 |
|-------------|-------------|
| 200 | [OK] |
| 202 | 承認済み |
| 400 | 正しくない要求 |
| 401 | 権限がありません |
| 403 | Forbidden |
| 404 | 見つかりません |
| 500 | 内部サーバー エラー。 不明なエラーが発生しました。 |
|||

## <a name="connector-reference"></a>コネクタのレファレンス

互いに類似するトリガーおよびアクション パラメーターの詳細については、[HTTP Webhook のパラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [アクセスとデータのセキュリティ保護 - 要求ベースのトリガーへの受信呼び出しへのアクセス](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Logic Apps のコネクタ](../connectors/apis-list.md)
