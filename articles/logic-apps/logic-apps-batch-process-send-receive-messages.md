---
title: "メッセージをグループまたはコレクションとしてバッチ処理を行う - Azure Logic Apps | Microsoft Docs"
description: "ロジック アプリでバッチ処理を行うメッセージを送受信する"
keywords: "バッチ、バッチ処理"
author: jonfancey
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 480ffce5dbe7c25181bb0ba5639de884e98ff4e6
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---

# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>ロジック アプリでメッセージの送信、受信、バッチ処理を行う

メッセージをグループで一緒に処理するには。データ項目 (メッセージ) を*バッチ*に送信し、それらの項目をバッチとして処理できます。 このアプローチは、データ項目を特定の方法でグループ化し、一緒に処理する必要がある場合に便利です。 

**バッチ** トリガーを使用して項目をバッチとして受信するロジック アプリを作成できます。 **バッチ** アクションを使用して項目をバッチとして送信するロジック アプリを作成できます。

このトピックでは、次のタスクを実行することでバッチ処理ソリューションを構築する方法を示します。 

* [項目をバッチとして受信して収集するロジック アプリを作成する](#batch-receiver)。 この "バッチ受信" ロジック アプリは、バッチ名とその受信ロジック アプリが項目をリリースして処理する前に満たすリリース条件を指定します。 

* [項目をバッチに送信するロジック アプリを作成する](#batch-sender)。 この "バッチ送信" ロジック アプリは、項目を送信する場所を指定します。場所は既存のバッチ受信ロジック アプリである必要があります。 また、顧客番号のような一意のキーを指定して、そのキーの基づいてターゲット バッチをサブセットに "パーティション" (分割) することもできます。 こうすることで、そのキーを持つすべての項目が収集されて一緒に処理されます。 

## <a name="requirements"></a>必要条件

この例に従うには、次の項目が必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントで作業を開始](https://azure.microsoft.com/free/)できます。 また、[従量課金のサブスクリプションにサインアップ](https://azure.microsoft.com/pricing/purchase-options/)することもできます。

* [ロジック アプリの作成方法](../logic-apps/logic-apps-create-a-logic-app.md)に関する基本的な知識 

* 任意の [Azure Logic Apps でサポートされる電子メール プロバイダー](../connectors/apis-list.md)の電子メール アカウント

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>メッセージをバッチとして受信するロジック アプリを作成する

メッセージをバッチに送信する前に、まず**バッチ** トリガーを使用して "バッチ受信" ロジック アプリを作成する必要があります。 こうすることで、送信ロジック アプリを作成するときにこの受信ロジック アプリを選択できます。 受信アプリでは、バッチ名、リリース条件、およびその他の設定を指定します。 

送信ロジック アプリでは項目を送信する場所を指定する必要がありますが、受信ロジック アプリに送信アプリの情報は不要です。

1. [Azure Portal](https://portal.azure.com) で、"BatchReceiver" という名前のロジック アプリを作成します。 

2. Logic Apps デザイナーで、ロジック アプリのワークフローを開始する**バッチ** トリガーを追加します。 検索ボックスに、フィルターとして「batch」と入力します。 **[バッチ - メッセージのバッチ処理]** というトリガーを選択します。

   ![バッチ トリガーを追加する](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. バッチの名前およびバッチをリリースするための条件を次のように指定します。

   * **[バッチ名]**: バッチの識別に使用する名前 (この例では "TestBatch")。
   * **[メッセージ数]**: リリースして処理するまでにバッチとして保持するメッセージの数 (この例では "5")。

   ![バッチ トリガーの詳細を指定する](./media/logic-apps-batch-process-send-receive-messages/receive-batch-trigger-details.png)

4. バッチ トリガーが発動したときに電子メールを送信する別のアクションを追加します。 バッチの項目が 5 個になるたびに、ロジック アプリが電子メールを送信します。

   1. バッチ トリガーで、**[+ 新しいステップ]** > **[アクションの追加]** の順に選択します。

   2. 検索ボックスに、フィルターとして「email」と入力します。
   電子メール プロバイダーに基づいて、電子メール コネクタを選択します。
   
      たとえば、職場または学校アカウントがある場合は、Office 365 Outlook コネクタを選択します。 
      Gmail アカウントがある場合は、Gmail コネクタを選択します。

   3. コネクタで [**{'*電子メール プロバイダー*'} - 電子メールの送信]** というアクションを選択します。

      For example:

      ![電子メール プロバイダーの "電子メールの送信" アクションを選択する](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. 以前に電子メール プロバイダーの接続を作成していなかった場合は、求められたときに認証用の電子メールの資格情報を指定します。 詳しくは、[電子メールの資格情報の認証](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事をご覧ください。

6. 今追加したアクションのプロパティを設定します。

   * **[宛先]** ボックスに、受信者の電子メール アドレスを入力します。 
   テスト目的で自分の電子メール アドレスを使用できます。

   * **[件名]** ボックスで、**[動的なコンテンツ]** の一覧が表示されたら、**[パーティション名]** フィールドを選択します。

     ![[動的なコンテンツ] の一覧で [パーティション名] を選択する](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     後のセクションで、ターゲット バッチをメッセージの送信先となる論理セットに分割する一意のパーティション キーを指定します。 
     各セットには送信ロジック アプリで生成された固有の番号があります。 
     この機能により複数のサブセットを持つ単一のバッチを使用できるようになり、各サブセットを指定した名前で定義できます。

   * **[本文]** ボックスで、**[動的なコンテンツ]** の一覧が表示されたら、**[メッセージ ID]** フィールドを選択します。

     ![[本文] で [メッセージ ID] を選択する](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     電子メールの送信アクションの入力は配列であるため、デザイナーは **[電子メールの送信]** アクションの周囲に **[For each]** ループを自動的に追加します。 
     このループはバッチ内の各項目に対して内部アクションを実行します。 
     つまり、バッチ トリガーで項目が 5 に設定されている場合は、トリガーが発動するたびに 5 通の電子メールを取得します。

7.  これでバッチ受信ロジック アプリが作成されました。次はロジック アプリを保存します。

    ![ロジック アプリを保存する](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>バッチにメッセージを送信するロジック アプリを作成する

ここでは受信ロジック アプリで定義されたバッチに項目を送信するロジック アプリを 1 つ以上作成します。 送信アプリには、受信ロジック アプリ、バッチ名、メッセージ コンテンツ、およびその他の設定を指定します。 一意のパーティション キーを指定してバッチをサブセットに分割し、そのキーを持つ項目を収集するように設定することもできます。

送信ロジック アプリでは項目を送信する場所を指定する必要がありますが、受信ロジック アプリに送信アプリの情報は不要です。

1. "BatchSender" という名前の別のロジック アプリを作成します。

   1. 検索ボックスに、フィルターとして「recurrence」と入力します。 
   トリガー **[スケジュール - 繰り返し]** を選択します。

      ![[スケジュール - 繰り返し] トリガーを追加する](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. 送信ロジック アプリの実行頻度と間隔を毎分に設定します。

      ![繰り返しトリガーの頻度と間隔を設定する](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. メッセージをバッチに送信する新しいステップを追加します。

   1. 繰り返しトリガーで、**[+ 新しいステップ]** > **[アクションの追加]** の順に選択します。

   2. 検索ボックスに、フィルターとして「batch」と入力します。 

   3. アクション **[バッチ処理するメッセージの送信 – バッチ トリガーを含む Logic Apps ワークフローを選択します]** を選択します。

      ![[バッチ処理するメッセージの送信] を選択する](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. ここで前に作成したアクションとして表示されている "BatchReceiver" ロジック アプリを選択します。

      !["バッチ受信" ロジック アプリを選択する](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > 一覧にはバッチ トリガーがあるその他のロジック アプリも表示されます。

3. バッチのプロパティを設定します。

   * **[バッチ名]**: 受信ロジック アプリによって定義されたバッチ名 (この例では "TestBatch" で実行時に検証されます)。

     > [!IMPORTANT]
     > 受信ロジック アプリで指定されたバッチ名と一致している必要があるため、バッチ名は変更しないように注意してください。
     > バッチ名を変更すると、送信ロジック アプリが失敗します。

   * **[メッセージのコンテンツ]**: 送信するメッセージのコンテンツ。 
   この例では、バッチに送信するメッセージのコンテンツの現在の日付と時間を挿入するこの式を追加します。

     1. **[動的なコンテンツ]** の一覧が表示されたら、**[式]** を選択します。 
     2. 式 **utcnow()** を選択し、**[OK]** を選択します。 

        ![[メッセージのコンテンツ] で [式] を選択する 「utcnow()」と入力します。](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. ここでバッチのパーティションを設定します。 "BatchReceiver" アクションで、**[詳細オプションの表示]** を選択します。

   * **[パーティション名]**: ターゲット バッチの分割に使用する省略可能な一意のパーティション キー。 この例では、1 から 5 の間の数をランダムに生成する式を追加します。
   
     1. **[動的なコンテンツ]** の一覧が表示されたら、**[式]** を選択します。
     2. 式を「**rand(1,6)**」と入力します。

        ![ターゲット バッチのパーティションをセットアップする](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        この **rand** 関数は 1 から 5 の間の数字を生成します。 
        これにより、この式が動的に設定した 5 つの番号のパーティションにこのバッチが分割されます。

   * **[メッセージ ID]**: 省略可能なメッセージ識別子。空のときは生成された GUID になります。 
   この例では、このボックスを空のままにします。

5. ロジック アプリを保存し、 送信ロジック アプリは次の例のようになります。

   ![送信ロジック アプリを保存する](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>ロジック アプリをテストする

バッチ ソリューションをテストするには、数分間ロジック アプリを実行したままにします。 間もなく、同じパーティション キーを持つ電子メールを 5 個ずつ取得し始めます。

BatchSender ロジック アプリは毎分実行され、1 から 5 の間の数字をランダムに生成し、この生成された番号をメッセージが送信されるターゲット バッチのパーティション キーとして使用します。 バッチに同じパーティション キーの項目が 5 つ入るたびに BatchReceiver ロジック アプリが発動し、各メッセージについてメールを送信します。

> [!IMPORTANT]
> テストが完了したら、BatchSender ロジック アプリを無効にしてメッセージの送信を停止し、受信トレイのオーバーロードを防止します。

## <a name="next-steps"></a>次のステップ

* [JSON を使用してロジック アプリの定義に基づいて構築する](../logic-apps/logic-apps-author-definitions.md)
* [Azure Logic Apps と関数を使用して Visual Studio でサーバーレス アプリを構築する](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [ロジック アプリの例外処理とエラーのログ記録](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

