---
title: B2B エンタープライズ統合シナリオのメッセージ交換
description: Enterprise Integration Pack を使用して Azure Logic Apps 内の取引先間で B2B メッセージを送受信する
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: e16cc8934407a5c54c84fd045c99e28116e656c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93310437"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack を使用して B2B AS2 メッセージを受信して確認する

取引先と契約を定義する統合アカウントがあれば、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) を使用して、取引先間でメッセージを交換するための自動化された企業間 (B2B) ワークフローを作成できます。 Azure Logic Apps は、AS2、X12、EDIFACT、RosettaNet の業界標準プロトコルをサポートするコネクタで動作します。 また、これらのコネクタを、[Logic Apps で使用可能な他のコネクタ](../connectors/apis-list.md) (たとえば、Salesforce、Office 365 Outlook など) と組み合わせることもできます。

この記事では、要求トリガーを使用して HTTP 要求を受信し、AS2 アクションと X12 アクションを使用してメッセージのコンテンツをデコードした後、応答アクションを使用して応答を返すロジック アプリの作成方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [要求](../connectors/connectors-native-reqres.md)トリガーを使用し、その後に次に示すアクションを使用して、B2B ワークフローを作成できるようにする空のロジック アプリ。

  * [[AS2 デコード]](../logic-apps/logic-apps-enterprise-integration-as2.md) を選択したことを確認します。

  * [条件](../logic-apps/logic-apps-control-flow-conditional-statement.md)。AS2 デコード アクションの成功または失敗に基づいて[要求](../connectors/connectors-native-reqres.md)を送信します。

  * [X12 メッセージのデコード](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイックスタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

* Azure サブスクリプションに関連付けられ、ロジック アプリにリンクされている[統合アカウント](./logic-apps-enterprise-integration-create-integration-account.md)。 使用するロジックアプリと統合アカウントは両方とも同じ場所または同じ Azure リージョンに存在する必要があります。

* 統合アカウントで、パートナーの [AS2 契約および X12 契約](logic-apps-enterprise-integration-agreements.md)と共に既に定義されている 2 つ以上の[取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)。

## <a name="add-the-request-trigger"></a>要求トリガーを追加する

この例では、Azure portal のロジック アプリ デザイナーを使用しますが、Visual Studio のロジック アプリ デザイナーを使用して同様のステップに従うこともできます。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーで空のロジック アプリを開きます。

1. 検索ボックスに「`when a http request`」と入力し、トリガーとして使用する **[HTTP 要求の受信時]** を選択します。

   ![ロジック アプリ ワークフローを開始するための要求トリガーを選択する](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. X12 メッセージはフラット ファイルであるため、 **[要求本文の JSON スキーマ]** ボックスは空のままにしておきます。

   ![[要求本文の JSON スキーマ] は空のままにしておく](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

   このステップでは、ロジック アプリをトリガーする要求を送信する場合に使用する **HTTP POST URL** を生成します。 この URL をコピーするには、URL の横にあるコピー アイコンを選択します。

   ![呼び出しを受信するために要求トリガー用に生成された URL](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-the-as2-decode-action"></a>AS2 デコード アクションを追加する

次に、使用する B2B アクションを追加します。 この例では、AS2 アクションと X12 アクションを使用します。

1. トリガーで、 **[新しいステップ]** を選択します。 トリガーの詳細を非表示にするには、トリガーのタイトル バーをクリックします。

   ![ロジック アプリ ワークフローに別のステップを追加する](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. **[アクションを選択してください]** の下にある検索ボックスに、「`as2 decode`」と入力して、 **[AS2 デコード (v2)]** を選択します。

   !["AS2 デコード (v2)" を検索して選択する](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. **[デコードするメッセージ]** プロパティでは、AS2 アクションでデコードする入力を入力します。この入力は、HTTP 要求トリガーで受信される `body` のコンテンツです。 このコンテンツを入力として指定するには、動的コンテンツ リストから選択する方法と、式として指定する方法があります。

   * 使用可能なトリガー出力を示す一覧から選択するには、 **[デコードするメッセージ]** ボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[HTTP 要求の受信時]** で、 **[本文]** プロパティ値を選択します。次に例を示します。

     ![トリガーから値 "本文" を選択する](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * トリガーの `body` 出力を参照する式を入力するには、 **[デコードするメッセージ]** ボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[式]** を選択します。 式エディターに式を入力して、 **[OK]** を選択します。

     `triggerOutputs()['body']`

     または、 **[デコードするメッセージ]** ボックスに次の式を直接入力します。

     `@triggerBody()`

     この式は、 **[本文]** トークンに解決されます。

     ![トリガーからの解決済みの本文の出力](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. **[メッセージ ヘッダー]** プロパティでは、AS2 アクションに必要なヘッダーを入力します。これは、HTTP 要求トリガーによって受信される `headers` のコンテンツによって記述されます。

   1. トリガーの `headers` 出力を参照する式を入力するには、 **[Switch Message headers to text mode]\(メッセージ ヘッダーをテキスト モードに切り替える\)** を選択します。

      ![[Switch Message headers to text mode]\(メッセージ ヘッダーをテキスト モードに切り替える\) が選択されているスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/as2-decode-switch-text-mode.png)

   1. **[メッセージ ヘッダー]** ボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[式]** を選択します。 式エディターに式を入力して、 **[OK]** を選択します。

      `triggerOutputs()['Headers']`

      AS2 デコード アクションでは、式はトークンとして表示されるようになりました。

      ![[メッセージ ヘッダー] ボックス内の "@triggerOutputs()['Headers']" トークンを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/as2-decode-message-header-expression.png)

   1. **Headers** トークンに解決される式トークンを取得するには、デザイナーとコード ビューを切り替えます。 この手順の後、AS2 デコード アクションは次の例のようになります。

      ![トリガーからの解決済みのヘッダーの出力](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>メッセージ受信通知の応答アクションを追加する

メッセージを受信したことを取引先に通知するには、**応答** アクションを使用して、AS2 メッセージ処理通知 (MDN) を含む応答を返すことができます。 このアクションを **AS2 デコード** アクションの直後に追加すると、AS2 デコード アクションが失敗した場合、ロジック アプリは処理を続行しません。

1. **[AS2 デコード]** アクションで、 **[新しいステップ]** を選択します。

1. **[アクションを選択してください]** の下にある検索ボックスの下で、 **[ビルトイン]** を選択します。 検索ボックスに「 `condition`」と入力します。 **[アクション]** の一覧で、 **[条件]** を選択します。

   !["条件" アクションを追加する](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   以上で、条件を満たしている場合と満たしていない場合のパスを含む条件図形が表示されます。

   ![条件図形と空のパスを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. 次に、評価する条件を指定します。 **[値の選択]** ボックスに、次の式を入力します。

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   中央のボックスの比較演算が `is equal to` に設定されていることを確認します。 右側のボックスに、値 `Expected` を入力します。 式をこのトークンとして解決するには、[デザイナー] と [コード ビュー] を切り替えます。

   ![条件が追加された条件図形を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. 次に、**AS2 デコード** アクションが成功したかどうかを返す応答を指定します。

   1. **AS2 デコード** アクションが成功した場合については、 **[true の場合]** 図形で、 **[アクションの追加]** を選択します。 **[アクションを選択してください]** の下にある検索ボックスに、「`response`」と入力して、 **[応答]** を選択します。

      !["応答" アクションを検索して追加する](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. **AS2 デコード** アクションの出力から AS2 MDN にアクセスするには、次の式を指定します。

      * **[応答]** アクションの **[ヘッダー]** プロパティに、次の式を入力します。

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * **[応答]** アクションの **[本文]** プロパティに、次の式を入力します。

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 式をトークンとして解決するには、[デザイナー] と [コード ビュー] を切り替えます。

      ![AS2 MDN にアクセスするための解決済みの式](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. **AS2 デコード** アクションが失敗した場合については、 **[false の場合]** 図形で、 **[アクションの追加]** を選択します。 **[アクションを選択してください]** の下にある検索ボックスに、「`response`」と入力して、 **[応答]** を選択します。 必要な状態とエラーを返すように **応答** アクションを設定します。

1. ロジック アプリを保存します。

## <a name="add-decode-x12-message-action"></a>X12 メッセージのデコード アクションを追加する

1. 次に、**X12 メッセージのデコード** アクションを追加します。 **[応答]** アクションで、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** の下にある検索ボックスに、「`x12 decode`」と入力して、 **[X12 メッセージのデコード]** を選択します。

   !["X12 メッセージのデコード" アクションを検索して選択する](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. X12 アクションで接続情報の入力を求められたら、接続の名前を指定して、使用する統合アカウントを選択した後、 **[作成]** を選択します。

   ![統合アカウントへの X12 接続を作成する](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. 次に、X12 アクションの入力を指定します。 この例では、AS2 アクションからの出力を使用します。これは、メッセージのコンテンツですが、このコンテンツは JSON オブジェクト形式で、Base64 でエンコードされることに注意してください。 このため、このコンテンツを文字列に変換する必要があります。

   **[デコードする X12 フラット ファイル メッセージ]** ボックスに、AS2 出力を変換する次の式を入力します。

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    式をこのトークンとして解決するには、[デザイナー] と [コード ビュー] を切り替えます。

    ![Base64 でエンコードされたコンテンツを文字列に変換する](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. ロジック アプリを保存します。

   このロジック アプリに追加のステップが必要な場合 (たとえば、メッセージのコンテンツをデコードして、そのコンテンツを JSON オブジェクト形式で出力する場合)、ロジック アプリのビルドを続行します。

これで、B2B ロジック アプリの設定が完了しました。 実際のアプリでは、デコードした X12 データを基幹業務 (LOB) アプリやデータ ストアに保存できます。 たとえば、次の記事を参照してください。

* [Azure Logic Apps から SAP システムに接続する](../logic-apps/logic-apps-using-sap-connector.md)
* [SSH と Azure Logic Apps を使用して SFTP ファイルの監視、作成、および管理を行う](../connectors/connectors-sftp-ssh.md)

独自の LOB アプリに接続してロジック アプリでこれらの API を使用するために、さらにアクションを追加したり、[カスタム API を作成](../logic-apps/logic-apps-create-api-app.md)したりすることができます。

## <a name="next-steps"></a>次のステップ

* [HTTPS 呼び出しを受信して応答する](../connectors/connectors-native-reqres.md)
* [B2B エンタープライズ統合用の AS2 メッセージを交換する](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [B2B エンタープライズ統合用の X12 メッセージを交換する](../logic-apps/logic-apps-enterprise-integration-x12.md)
* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) について詳細を確認する
