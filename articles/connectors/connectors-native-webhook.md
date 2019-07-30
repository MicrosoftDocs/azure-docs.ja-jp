---
title: Azure Logic Apps でイベントベースのタスクとワークフローを作成する
description: Azure Logic Apps を使用して、エンドポイントで発生したイベントに基づいて、自動化されたタスク、プロセス、およびワークフローをトリガー、一時停止、再開します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541331"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Azure Logic Apps で HTTP Webhook を使用してイベントベースのタスクとワークフローを自動化する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と組み込み HTTP Webhook コネクタを使用すると、ロジック アプリを構築することで、HTTP または HTTPS エンドポイントで発生する特定のイベントに基づいて待機したり実行されたりするワークフローを自動化できます。 たとえば、定期的にサービス エンドポイントを確認、つまり "*ポーリング*" するのではなく、特定のイベントを待ってからワークフローをトリガーし、指定されたアクションを実行することでそのエンドポイントを監視するロジック アプリを作成できます。

イベントベースのワークフローの例を次に示します。

* [Azure イベント ハブ](https://github.com/logicappsio/EventHubAPI)からアイテムが到着するのを待ってからロジック アプリの実行をトリガーする。
* 承認を待ってからワークフローを続行する。

## <a name="how-do-webhooks-work"></a>Webhook のしくみ

HTTP Webhook トリガーは、イベントに基づいており、新しい項目の定期的な確認 (ポーリング) に依存しません。 Webhook トリガーで開始するロジック アプリを保存したり、ロジック アプリを無効から有効に変更したりすると、Webhook トリガーは、特定のサービスまたはエンドポイントに "*コールバック URL*" を登録して、そのサービスまたはエンドポイントを "*サブスクライブ*" します。 その後、このトリガーは、そのサービスまたはエンドポイントが URL を呼び出すまで待ち、その結果、ロジック アプリの実行が開始されます。 [要求トリガー](connectors-native-reqres.md)と同様に、ロジック アプリは、指定されたイベントが発生するとすぐに起動します。 トリガーを削除してロジック アプリを保存したり、ロジック アプリを有効から無効に変更したりすると、トリガーはサービスまたはエンドポイントから "*サブスクライブを解除*" します。

HTTP Webhook アクションもイベントに基づいており、特定のサービスまたはエンドポイントに "*コールバック URL*" を登録することで、そのサービスまたはエンドポイントを "*サブスクライブ*" します。 Webhook アクションがロジック アプリのワークフローを一時停止し、サービスまたはエンドポイントが URL を呼び出すまで待った後、ロジック アプリの実行が再開されます。 次のような場合、アクション ロジック アプリはサービスまたはエンドポイントから "*サブスクライブを解除*" します。

* Webhook アクションが正常に終了したとき
* 応答を待機しているときにロジック アプリの実行がキャンセルされた場合
* ロジック アプリがタイムアウトになる前

たとえば、Office 365 Outlook コネクタの[**承認メールの送信**](connectors-create-api-office365-outlook.md)アクションは、このパターンに従う Webhook アクションの一例です。 Webhook アクションを使用することで、このパターンをあらゆるサービスに適用できます。

詳細については、以下のトピックを参照してください。

* [HTTP Webhook トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhook とサブスクリプション](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Webhook をサポートするカスタム API を作成する](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 必要に応じて[ロジック アプリの Webhook トリガー](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)または[ロジック アプリの Webhook アクション](../logic-apps/logic-apps-create-api-app.md#webhook-actions)のサブスクライブおよびサブスクライブ解除パターンをサポートする、既にデプロイ済みのエンドポイントまたは API の URL

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

* ターゲット エンドポイントで特定のイベントを待機するロジック アプリ。 HTTP Webhook トリガーで開始するには、[空のロジック アプリを作成します](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 HTTP Webhook アクションを使用するには、任意のトリガーで対象のロジック アプリを起動します。 この例では、最初のステップとして HTTP トリガーを使用します。

## <a name="add-an-http-webhook-trigger"></a>HTTP Webhook トリガーの追加

この組み込みトリガーは、指定されたサービスにコールバック URL を登録し、そのサービスがその URL に対して HTTP POST 要求を送信するのを待ちます。 このイベントが発生すると、トリガーが起動してすぐにロジック アプリを実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーで空のロジック アプリを開きます。

1. デザイナーの検索ボックスに、フィルターとして「http webhook」と入力します。 **[トリガー]** の一覧から、 **[HTTP Webhook]** トリガーを選択します。

   ![HTTP Webhook トリガーを選択する](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   この例では、このステップの名前がよりわかりやすくなるように、トリガー名を "HTTP Webhook trigger" に変更します。 また、この例では後で HTTP Webhook アクションを追加します。どちらの名前も一意である必要があります。

1. サブスクライブおよびサブスクライブ解除の呼び出しに使用する [HTTP Webhook トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)の値を指定します。次に例を示します。

   ![HTTP Webhook トリガー パラメーターを入力する](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. その他の使用可能なパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

   HTTP Webhook に使用できる認証の種類の詳細については、「[HTTP トリガーとアクションを認証する](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)」を参照してください。

1. トリガーが起動したときに実行されるアクションを使用して、ロジック アプリのワークフローを引き続き構築します。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

   ロジック アプリを保存すると、サブスクライブ エンドポイントが呼び出され、このロジック アプリをトリガーするためのコールバック URL が登録されます。

1. これで、ターゲット サービスがコールバック URL に `HTTP POST` 要求を送信するたびに、ロジック アプリが起動し、要求を通じて渡されたすべてのデータが取得されます。

## <a name="add-an-http-webhook-action"></a>HTTP Webhook アクションの追加

この組み込みアクションは、指定されたサービスにコールバック URL を登録し、ロジック アプリのワークフローを一時停止して、そのサービスがその URL に HTTP POST 要求を送信するのを待ちます。 このイベントが発生すると、アクションはロジック アプリの実行を再開します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーでロジック アプリを開きます。

   この例では、最初のステップとして HTTP Webhook トリガーを使用しています。

1. HTTP Webhook アクションを追加するステップで、 **[新しいステップ]** を選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. デザイナーの検索ボックスに、フィルターとして「http webhook」と入力します。 **[アクション]** の一覧で、 **[HTTP Webhook]** アクションを選択します。

   ![HTTP Webhook アクションを選択する](./media/connectors-native-webhook/select-http-webhook-action.png)

   この例では、このステップの名前がわかりやすくなるように、アクション名を "HTTP Webhook action" に変更します。

1. HTTP Webhook アクション パラメーターの値を指定します。これは、サブスクライブおよびサブスクライブ解除の呼び出しに使用する [HTTP Webhook トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger)に似ています。次に例を示します。

   ![HTTP Webhook アクション パラメーターを入力する](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   実行中、ロジック アプリは、このアクションを実行するときにサブスクライブ エンドポイントを呼び出します。 その後、ロジック アプリは、ワークフローを一時停止して、ターゲット サービスがコールバック URL に `HTTP POST` 要求を送信するのを待ちます。 そのアクションが正常に完了すると、そのアクションはエンドポイントからサブスクライブが解除され、対象のロジック アプリがワークフローの実行を再開します。

1. その他の使用可能なパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

   HTTP Webhook に使用できる認証の種類の詳細については、「[HTTP トリガーとアクションを認証する](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)」を参照してください。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

## <a name="connector-reference"></a>コネクタのレファレンス

互いに類似するトリガーおよびアクション パラメーターの詳細については、[HTTP Webhook のパラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger)に関するページを参照してください。

### <a name="output-details"></a>出力の詳細

ここでは、以下の情報を返す HTTP Webhook トリガーまたはアクションからの出力の詳細情報を示します。

| プロパティ名 | Type | 説明 |
|---------------|------|-------------|
| headers | object | 要求のヘッダー |
| body | object | JSON オブジェクト | 要求の本文の内容を含むオブジェクト |
| status code | int | 要求の状態コード |
|||

| status code | 説明 |
|-------------|-------------|
| 200 | OK |
| 202 | 承認済み |
| 400 | 正しくない要求 |
| 401 | 権限がありません |
| 403 | 許可されていません |
| 404 | 見つかりません |
| 500 | 内部サーバー エラー。 不明なエラーが発生しました。 |
|||

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
