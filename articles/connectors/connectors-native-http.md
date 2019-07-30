---
title: Azure Logic Apps から HTTP または HTTPS エンドポイントに接続する
description: Azure Logic Apps を使用して、自動化されたタスク、プロセス、およびワークフロー内で HTTP または HTTPS エンドポイントを監視します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: fa5fd3ef8b144826468f56ea2a14be592cef5dc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541300"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して HTTP または HTTPS エンドポイントを呼び出す

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と組み込み HTTP コネクタを使用すると、ロジック アプリを構築することで、任意の HTTP または HTTPS エンドポイントを定期的に呼び出すワークフローを自動化できます。 たとえば、対象の Web サイトのサービス エンドポイントは、そのエンドポイントを指定したスケジュールで確認することで監視できます。 Web サイトの停止など、そのエンドポイントで特定のイベントが発生すると、そのイベントによって対象のロジック アプリのワークフローがトリガーされ、指定したアクションが実行されます。

定期的にエンドポイントを確認する、つまり "*ポーリング*" するために、ワークフローの最初のステップとして HTTP トリガーを使用できます。 チェックごとに、トリガーからエンドポイントに対する呼び出し、つまり*要求*が送信されます。 エンドポイントの応答によって、ロジック アプリのワークフローが実行されるかどうかが決定します。 トリガーによって、応答の任意のコンテンツがロジック アプリのアクションに渡されます。

必要に応じてエンドポイントを呼び出すために、ワークフローの他の手順として HTTP アクションを使用できます。 エンドポイントの応答によって、ワークフローの以降のアクションの実行方法が決まります。

この HTTP コネクタは、ターゲット エンドポイントの機能に基づき、トランスポート層セキュリティ (TLS) バージョン 1.0、1.1、1.2 をサポートしています。 Logic Apps は、考えられる最高のサポート バージョンを使用してエンドポイントとネゴシエートします。 そのため、たとえば、エンドポイントが 1.2 をサポートする場合、コネクタは、まず 1.2 を使用します。 それ以外の場合、コネクタは、2 番目に高いサポート バージョンを使用します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 呼び出すターゲット エンドポイントの URL

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

* ターゲット エンドポイントの呼び出し元となるロジック アプリ。 HTTP トリガーで開始するには、[空のロジック アプリを作成します](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 HTTP アクションを使用するには、任意のトリガーで対象のロジック アプリを起動します。 この例では、最初のステップとして HTTP トリガーを使用します。

## <a name="add-an-http-trigger"></a>HTTP トリガーの追加

この組み込みトリガーは、エンドポイントに指定された URL に対して HTTP 呼び出しを実行し、応答を返します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーで空のロジック アプリを開きます。

1. デザイナーの検索ボックスに、フィルターとして「http」と入力します。 **[トリガー]** の一覧から、 **[HTTP]** トリガーを選択します。

   ![HTTP トリガーを選択する](./media/connectors-native-http/select-http-trigger.png)

   この例では、トリガー名を "HTTP trigger" に変更して、このステップにわかりやすい名前を付けています。 また、この例では後で HTTP アクションを追加します。どちらの名前も一意である必要があります。

1. ターゲット エンドポイントへの呼び出しに含める [HTTP トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)の値を指定します。 トリガーでターゲット エンドポイントを確認する頻度を設定します。

   ![HTTP トリガー パラメーターを入力する](./media/connectors-native-http/http-trigger-parameters.png)

   HTTP に使用できる認証の種類の詳細については、「[HTTP トリガーとアクションを認証する](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)」を参照してください。

1. その他の使用可能なパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

1. トリガーが起動したときに実行されるアクションを使用して、ロジック アプリのワークフローを引き続き構築します。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

## <a name="add-an-http-action"></a>HTTP アクションの追加

この組み込みアクションは、エンドポイントに指定された URL に対して HTTP 呼び出しを実行し、応答を返します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーでロジック アプリを開きます。

   この例では、最初のステップとして HTTP トリガーを使用します。

1. HTTP アクションを追加するステップで、 **[新しいステップ]** を選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. デザイナーの検索ボックスに、フィルターとして「http」と入力します。 **[アクション]** の一覧で、 **[HTTP]** アクションを選択します。

   ![HTTP アクションを選択する](./media/connectors-native-http/select-http-action.png)

   この例では、アクション名を "HTTP action" に変更して、このステップにわかりやすい名前を付けています。

1. ターゲット エンドポイントへの呼び出しに含める [HTTP アクション パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)の値を指定します。

   ![HTTP アクションのパラメーターを入力する](./media/connectors-native-http/http-action-parameters.png)

   HTTP に使用できる認証の種類の詳細については、「[HTTP トリガーとアクションを認証する](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)」を参照してください。

1. その他の使用可能なパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

## <a name="connector-reference"></a>コネクタのレファレンス

トリガーとアクションのパラメーターの詳細については、以下のセクションを参照してください。

* [HTTP トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [HTTP アクション パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>出力の詳細

ここでは、以下の情報を返す HTTP トリガーまたはアクションからの出力の詳細情報を示します。

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
