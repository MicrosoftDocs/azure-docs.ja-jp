---
title: EDI メッセージをグループまたはコレクションとしてバッチ処理を行う - Azure Logic Apps | Microsoft Docs
description: ロジック アプリでバッチ処理を行う EDI メッセージを送信します
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: d6d3a7111f3a5e49e32eba8ca4f09d692538cb87
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "64715789"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>Azure Logic Apps を使用して取引先パートナーに EDI メッセージをバッチで送信する

企業間 (B2B) シナリオでは、パートナーがグループまたは*バッチ*でメッセージを交換することがよくあります。 Logic Apps を使用してバッチ処理ソリューションを構築すると、取引先パートナーにメッセージを送信し、それらのメッセージをバッチで処理することができます。 この記事では、X12 を例にして、"バッチ送信" ロジック アプリと "バッチ受信" ロジック アプリを作成して EDI メッセージをバッチ処理する方法について説明します。 

バッチ X12 メッセージは、他のメッセージをバッチ処理する場合と同様に機能します。つまり、メッセージをバッチにまとめるバッチ トリガーと、メッセージをバッチに送信するバッチ アクションを使用します。 また、X12 バッチ処理には、メッセージが取引先パートナーまたは他の宛先に送信される前に X12 のエンコード手順が含まれています。 バッチ トリガーとアクションの詳細については、[メッセージのバッチ処理](../logic-apps/logic-apps-batch-process-send-receive-messages.md)に関するページを参照してください。

この記事では、バッチ処理ソリューションを構築します。同じ Azure サブスクリプション、同じ Azure リージョン内に、次の順序で 2 つのロジック アプリを作成します。

* ["バッチ受信"](#receiver) ロジック アプリ。メッセージをリリースして処理する条件を指定しておくと、その条件が満たされるまでメッセージを受け取り、バッチとして収集します。 このシナリオでは、バッチ受信アプリも、指定された X12 契約またはパートナー ID を使用してバッチ内のメッセージをエンコードします。

  バッチ送信アプリを作成するときにバッチの送信先を選ぶことができるよう、必ずバッチ受信アプリを先に作成してください。

* ["バッチ送信"](#sender) ロジック アプリ。先に作成しておいたバッチ受信アプリにメッセージを送信します。 

バッチ受信アプリとバッチ送信アプリは、同じ Azure サブスクリプションを共有し、*なおかつ*同じ Azure リージョンを共有している必要があります。 異なる場合は互いを認識できず、バッチ送信アプリを作成するときにバッチ受信アプリを選択できません。

## <a name="prerequisites"></a>前提条件

この例に従うには、次の項目が必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントで作業を開始](https://azure.microsoft.com/free/)できます。 また、[従量課金制サブスクリプション](https://azure.microsoft.com/pricing/purchase-options/)にサインアップすることもできます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Azure サブスクリプションに関連付けられ、ロジック アプリにリンクされている既存の[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) が1 つ

* 統合アカウント内の既存の[パートナー](../logic-apps/logic-apps-enterprise-integration-partners.md)が 2 つ以上。 各パートナーは、パートナーのプロパティで X12 (Standard Carrier Alpha Code) 修飾子をビジネス ID として使用する必要があります。

* 統合アカウントの既存の [X12 契約](../logic-apps/logic-apps-enterprise-integration-x12.md)

* Azure portal ではなく Visual Studio を使う場合は、[Logic Apps と連携するように Visual Studio を設定](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)してください。

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>X12 バッチ受信アプリを作成する

バッチにメッセージを送信するには、その送信先となるバッチがあらかじめ存在していなければなりません。 そのためまず、**Batch** トリガーを開始する "バッチ受信" ロジック アプリを作成する必要があります。 そうすることで、"バッチ送信" ロジック アプリを作成するときに、そのバッチ受信ロジック アプリを選択することができます。 メッセージをリリースして処理する条件を指定しておくと、バッチ受信アプリは、その条件が満たされるまでメッセージを収集し続けます。 バッチ受信アプリは、バッチ送信アプリについての情報を一切必要としませんが、バッチ送信アプリには、どこにメッセージを送信すべきかの情報が必要です。 

このバッチ受信アプリに対し、バッチのモード、名前、リリース条件、X12 契約などの設定を指定します。 

1. [Azure portal](https://portal.azure.com) または Visual Studio で、"BatchX12Messages" という名前のロジック アプリを作成します

2. [ロジック アプリを統合アカウントにリンクします](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

3. Logic Apps デザイナーで、ロジック アプリのワークフローを開始する**バッチ** トリガーを追加します。 検索ボックスに、フィルターとして「batch」と入力します。 トリガーとして、**メッセージのバッチ処理**

   ![バッチ トリガーを追加する](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. バッチ受信アプリのプロパティを次のように設定します。 

   | プロパティ | 値 | メモ | 
   |----------|-------|-------|
   | **バッチ モード** | インライン |  |  
   | **バッチ名** | TestBatch | **[インライン]** バッチ モードの場合にのみ使用できます | 
   | **リリース条件** | メッセージ数ベース、スケジュール ベース | **[インライン]** バッチ モードの場合にのみ使用できます | 
   | **メッセージ数** | 10 | リリース条件が **[メッセージ数ベース]** の場合にのみ使用できます | 
   | **間隔** | 10 | リリース条件が **[スケジュール ベース]** の場合にのみ使用できます | 
   | **頻度** | 分 | リリース条件が **[スケジュール ベース]** の場合にのみ使用できます | 
   ||| 

   ![バッチ トリガーの詳細を指定する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > この例では、バッチのパーティションを設定していないため、各バッチは同じパーティション キーを使用します。 パーティションの詳細については、[メッセージのバッチ処理](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender)に関する説明を参照してください。

5. 次は、各バッチをエンコードするアクションを追加します。 

   1. バッチ トリガーで、 **[新しいステップ]** を選択します。

   2. 検索ボックスにフィルターとして「X12 batch」と入力します。また、(いずれのバージョンでも) 次のアクションを選択します。**バッチ エンコード <*バージョン*> - X12** 

      ![X12 バッチ エンコード アクションを選択する](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. 以前に統合アカウントに接続していない場合は、ここで接続を作成します。 接続の名前を入力し、必要な統合アカウントを選択して、 **[作成]** を選択します。

      ![バッチ エンコーダーと統合アカウント間の接続を作成する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. バッチ エンコーダー アクションに以下のプロパティを設定します。

      | プロパティ | 説明 |
      |----------|-------------|
      | **X12 契約の名前** | 一覧を開き、既存の契約を選択します。 <p>一覧が空の場合は、必要な契約がある[統合アカウントにロジック アプリをリンク](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)します。 | 
      | **BatchName** | このボックス内をクリックし、動的コンテンツ リストが表示されたら、 **[バッチ名]** トークンを選択します。 | 
      | **PartitionName** | このボックス内をクリックし、動的コンテンツ リストが表示されたら、 **[パーティション名]** トークンを選択します。 | 
      | **アイテム** | アイテムの詳細ボックスを閉じて、このボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[バッチ処理されたアイテム]** トークンを選択します。 | 
      ||| 

      ![バッチ エンコード アクションの詳細](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      **[アイテム]** ボックスの場合:

      ![バッチ エンコード アクションのアイテム](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. ロジック アプリを保存し、 

7. Visual Studio を使用している場合、[バッチ受信ロジック アプリを Azure にデプロイ](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)する必要があります。 そうしないと、バッチ送信アプリを作成するときにバッチ受信アプリを選択できません。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

バッチ受信アプリが期待どおりに動作するように、テスト用の HTTP アクションを追加し、バッチ処理されたメッセージを[要求ビン サービス](https://requestbin.fullcontact.com/)に送信することができます。 

1. X12 エンコード アクションで、 **[新しいステップ]** を選択します。 

2. 検索ボックスに、フィルターとして「http」と入力します。 このアクションを選択: **[HTTP - HTTP]** アクションを選択します。
    
   ![HTTP アクションを選択する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. HTTP アクションのプロパティを設定します。

   | プロパティ | 説明 | 
   |----------|-------------|
   | **メソッド** | このリストから **[POST]** を選択します。 | 
   | **Uri** | 要求ビンの URI を生成し、その URI をこのボックスに入力します。 | 
   | **本文** | このボックス内をクリックし、動的コンテンツ リストが開いたら、 **[契約名によるバッチ エンコード]** セクションに表示される **[本文]** トークンを選択します。 <p>**[本文]** トークンが表示されない場合は、 **[契約名によるバッチ エンコード]** の横の **[詳細表示]** を選択します。 | 
   ||| 

   ![HTTP アクションの詳細を指定する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. ロジック アプリを保存し、 

   バッチ受信ロジック アプリは次の例のようになります。 

   ![バッチ受信ロジック アプリを保存する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>X12 バッチ送信アプリを作成する

次は、バッチ受信ロジック アプリにメッセージを送信するロジック アプリを 1 つまたは複数作成します。 各バッチ送信アプリで、バッチ受信ロジック アプリ、バッチ名、メッセージ コンテンツなどの設定を指定します。 一意のパーティション キーを指定してバッチをサブセットに分割し、そのキーを持つメッセージを収集するように設定することもできます。 

* バッチ送信アプリを作成するときに、その送信先バッチとして既存のバッチ受信アプリを選択できるよう、必ず[バッチ受信アプリ](#receiver)を先に作成してください。 バッチ受信アプリは、バッチ送信アプリについての情報を一切必要としませんが、バッチ送信アプリには、どこにメッセージを送信すべきかの情報が必要です。 

* バッチ受信アプリとバッチ送信アプリは、同じ Azure リージョンを共有し、"*なおかつ*" 同じ Azure サブスクリプションを共有している必要があります。 異なる場合は互いを認識できず、バッチ送信アプリを作成するときにバッチ受信アプリを選択できません。

1. 次の名前の別のロジック アプリを作成します:"SendX12MessagesToBatch" 

2. 検索ボックスに、フィルターとして「HTTP 要求の」と入力します。 トリガーとして、**HTTP 要求の受信時** 
   
   ![要求トリガーを追加する](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. メッセージをバッチに送信するアクションを追加します。

   1. HTTP 要求アクションで **[新しいステップ]** を選択します。

   2. 検索ボックスに、フィルターとして「batch」と入力します。 
   **[アクション]** リストを選択し、 **[バッチ トリガーを含む Logic Apps ワークフローを選択します - バッチ処理するメッセージの送信]** アクションを選択します。

      ![[バッチ トリガーを含む Logic Apps ワークフローを選択します] を選択します](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. ここで、前に作成した、"BatchX12Messages" ロジック アプリを選択します。

      !["バッチ受信" ロジック アプリを選択する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. このアクションを選択: **[メッセージのバッチ処理 - <*your-batch-receiver*>]**

      !["Batch_messages" アクションを選択する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. バッチ送信アプリのプロパティを設定します。

   | プロパティ | 説明 | 
   |----------|-------------| 
   | **バッチ名** | 受信ロジック アプリによって定義されたバッチ名 (この例では "TestBatch") <p>**重要**:バッチ名は実行時に検証されます。また、受信ロジック アプリによって指定された名前と一致している必要があります。 バッチ名を変更すると、バッチ送信アプリが失敗します。 | 
   | **メッセージのコンテンツ** | 送信するメッセージのコンテンツ (この例では **[本文]** トークンです) | 
   ||| 
   
   ![バッチのプロパティを設定する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. ロジック アプリを保存し、 

   バッチ送信ロジック アプリは次の例のようになります。

   ![バッチ送信ロジック アプリを保存する](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>ロジック アプリをテストする

バッチ処理ソリューションをテストするには、[Postman](https://www.getpostman.com/postman) または類似のツールを使用して、X12 メッセージをバッチ送信ロジック アプリに送信します。 まもなくして、10 分ごとまたは 10 個ずつのバッチで X12 メッセージが要求ビンに取得され始めます。いずれのメッセージもパーティション キーは同じです。

## <a name="next-steps"></a>次の手順

* [メッセージをバッチとして処理する](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
