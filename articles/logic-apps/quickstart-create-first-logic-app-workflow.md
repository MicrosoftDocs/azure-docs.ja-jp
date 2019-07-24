---
title: クイック スタート - ワークフローを初めて作成して自動化する - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps を使用してタスク、プロセス、ワークフローを自動化するロジック アプリを初めて作成します。 お使いのシステム & クラウド サービスのシステム統合ソリューションと Enterprise Application Integration (EAI) ソリューション用のロジック アプリを作成する
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 577a047fb88208d7e14e8ba06a1b9e7e87c5ef91
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58884238"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>クイック スタート:Azure Logic Apps を使用して自動化されたワークフローを初めて作成する - Azure portal

このクイックスタートでは、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用して、自動化されたワークフローを初めてビルドする基本的な方法を紹介しています。 この記事では、Web サイトの RSS フィードを定期的にチェックして新しい項目を検出するロジック アプリを作成します。 新しい項目が存在する場合、ロジック アプリから項目ごとにメールが送信されます。 完成したロジック アプリの大まかなワークフローは、次のようになります。

![概要 - ロジック アプリの例](./media/quickstart-create-first-logic-app-workflow/overview.png)

このクイックスタートの手順に従うためには、Office 365 Outlook、Outlook.com、Gmail など、Logic Apps に対応しているプロバイダーのメール アカウントが必要です。 その他のプロバイダーについては、[こちらのコネクタ一覧を参照](https://docs.microsoft.com/connectors/)してください。 このロジック アプリでは、Office 365 Outlook アカウントを使います。 別のメール アカウントをお使いの場合でも、全体的な手順は同じですが、UI がやや異なる場合があります。 

また、Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

Azure アカウントの資格情報で <a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。

## <a name="create-your-logic-app"></a>ロジック アプリを作成する 

1. Azure のメイン メニューで、**[リソースの作成]** > **[統合]** > **[ロジック アプリ]** の順に選択します。

   ![ロジック アプリを作成する](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

3. **[ロジック アプリの作成]** で、次に示すようにロジック アプリの詳細を入力します。 操作が完了したら、**[作成]** を選択します。

   ![ロジック アプリの詳細を指定する](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | プロパティ | 値 | 説明 | 
   |----------|-------|-------------| 
   | **Name** | MyFirstLogicApp | ロジック アプリの名前 | 
   | **サブスクリプション** | <*Azure サブスクリプションの名前*> | Azure サブスクリプションの名前 | 
   | **リソース グループ** | My-First-LA-RG | 関連するリソースの整理に使用する[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前 | 
   | **場所** | 米国西部 | ロジック アプリの情報の保存先となるリージョン | 
   | **Log Analytics** | オフ | 診断ログの場合は、この設定を**オフ**のままにしてください。 | 
   |||| 

3. アプリのデプロイ後、Logic Apps デザイナーが開き、紹介ビデオやよく使用されるトリガーが含まれたページが表示されます。 **[テンプレート]** で **[空のロジック アプリ]** を選択します。

   ![空のロジック アプリ テンプレートを選択する](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

次に、新しい RSS フィード項目が発行されたときに起動される[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 すべてのロジック アプリは必ずトリガーから起動され、トリガーは、特定のイベントが発生するか特定の条件が満たされたときに起動されます。 トリガーが起動するたびに、Logic Apps エンジンによって、ワークフローを開始および実行するロジック アプリ インスタンスが作成されます。

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>トリガーを使って RSS フィードをチェックする

1. デザイナーの検索ボックスに「rss」と入力します。 トリガーとして、**[RSS - フィード項目が発行される場合]** を選択します。

   ![トリガーの選択: "RSS - フィード項目が発行される場合"](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

2. トリガーに関する情報を次のように入力します。 

   ![RSS フィード、頻度、および間隔を指定してトリガーを設定する](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | プロパティ | 値 | 説明 | 
   |----------|-------|-------------| 
   | **RSS フィードの URL** | ```http://feeds.reuters.com/reuters/topNews``` | 監視する RSS フィードのリンク | 
   | **間隔** | 1 | チェックの間隔 (単位数) | 
   | **頻度** | 分 | チェックの間隔に使う時間の単位  | 
   |||| 

   ロジック アプリのトリガーには、間隔と頻度の組み合わせでそのスケジュールを定義します。 
   このロジック アプリでは、1 分おきにフィードをチェックします。

3. ここではトリガーの詳細を非表示にするために、トリガーのタイトル バー内をクリックします。

   ![シェイプを折りたたんで詳細を非表示](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

4. ロジック アプリを保存し、 デザイナーのツール バーで、**[保存]** を選択します。 

ロジック アプリは現在稼働していますが、RSS フィードをチェックすること以外は何もしていません。 そこで、トリガーが起動したときに反応するアクションを追加します。

## <a name="send-email-with-an-action"></a>アクションを使ってメールを送信する

今度は、RSS フィードに新しい項目が発行されたときにメールを送信する[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 

1. **[フィード項目が発行される場合]** トリガーから **[新しいステップ]** を選択します。

   ![アクションを追加する](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

2. **[アクションを選択してください]** で、検索ボックスに「メールの送信」と入力します。 検索ボックスで、**[すべて]** を選択します。 アクション リストから、目的のメール プロバイダーの "メールの送信" アクションを選択します。 

   ![このアクションを選択: "Office 365 Outlook - 電子メールの送信"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   アクション リストを絞り込んで特定のアプリまたはサービスだけが表示されるようにするには、最初にアプリまたはサービスを選択します。

   * Azure の職場または学校アカウントには、Office 365 Outlook を選択します。 
   * 個人用 Microsoft アカウントには、Outlook.com を選択します。

3. 資格情報を求められた場合は、お使いのメール アカウントへの接続が Logic Apps によって作成されるように、そのメール アカウントにサインインします。

4. **[電子メールの送信]** アクションで、メールに設定するデータを指定します。 

   1. **[宛先]** ボックスに、受信者の電子メール アドレスを入力します。 
   テスト目的で自分の電子メール アドレスを使用できます。

      **[動的なコンテンツの追加]** リストが表示されますが、ここでは無視してください。 
      いくつかの編集ボックスは、その内部をクリックすると、このリストが表示され、先行するステップから、ワークフローへの入力として追加できるパラメーターがすべて表示されます。 

   2. **[件名]** ボックスに「```New RSS item:```」と入力し、その後ろに空白スペースを追加します。

      ![電子メールの件名を入力する](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)
 
   3. **[動的なコンテンツの追加]** リストから **[フィード タイトル]** を選択して RSS 項目のタイトルを追加します。

      ![動的コンテンツ リスト - "フィード タイトル"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      操作を終了すると、電子メールの件名は、次の例のようになります。

      ![追加されたフィード タイトル](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      デザイナーに "For each" ループが表示される場合、**[categories-item]\(カテゴリ-項目\)** トークンなど、配列のトークンを選択したことが原因です。 
      この種のトークンを参照するアクションの前後には、デザイナーによってこのループが自動的に追加されます。 
      そうすることで個々の配列項目に同じアクションが実行されます。 
      ループを削除するには、ループのタイトル バーで**省略記号** (**...**) を選択し、**[削除]** を選択します。

   4. **[本文]** ボックスには、メール本文に使用するテキストを次のように入力し、対応するトークンを選択します。 
   編集ボックスで空白行を追加するには、Shift + Enter キーを押します。 

      ![電子メールの本文の内容を追加する](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | プロパティ | 説明 | 
      |----------|-------------| 
      | **フィード タイトル** | 項目のタイトル | 
      | **フィードの公開日付** | 項目の公開日時 | 
      | **プライマリ フィード リンク** | 項目の URL | 
      ||| 
   
5. ロジック アプリを保存し、

ロジック アプリのテストに進みます。

## <a name="run-your-logic-app"></a>ロジック アプリを実行する

ロジック アプリを手動で開始するには、デザイナーのツール バーで **[実行]** を選択します。 または、指定したスケジュール (1 分おき) に従ってロジック アプリが RSS フィードをチェックするのを待ちます。 RSS フィードに新しい項目がある場合、ロジック アプリは新しい項目ごとに電子メールを送信します。 それ以外の場合、ロジック アプリは、次の機会を待ってからもう一度チェックします。 

このロジック アプリからは、次のようなメールが送信されます。 電子メールが届かない場合は、迷惑メール フォルダーを確認してください。

![新しい RSS フィード項目について送信された電子メール](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

技術的には、トリガーが RSS フィードをチェックして新しい項目を見つけると、トリガーが起動し、ワークフローのアクションを実行するロジック アプリのワークフローのインスタンスが Logic Apps エンジンによって作成されます。
トリガーで新しい項目が見つからなかった場合、トリガーは起動せず、ワークフローのインスタンス作成は "スキップ" されます。

お疲れさまでした。初めてのロジック アプリを Azure Portal で正しく作成し、実行することができました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このサンプルの必要がなくなったら、ロジック アプリと関連リソースが含まれるリソース グループを削除しましょう。 

1. Azure のメイン メニューから **[リソース グループ]** に移動し、対象のロジック アプリのリソース グループを選択します。 **[概要]** ページで **[リソース グループの削除]** を選択します。 

   ![[リソース グループ] > [概要] > [リソース グループの削除]](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

2. 確認のためにリソース グループ名を入力し、**[削除]** を選択します。

   ![削除の確定](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> ロジック アプリを削除にすると、新しい実行は開始されなくなります。 すべての進行中および保留中の実行は取り消されます。 何千もの実行がある場合、取り消しが完了するまでかなりの時間がかかる場合があります。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、初めてのロジック アプリを作成しました。作成したロジック アプリは、指定したスケジュール (1 分おき) で RSS の更新をチェックし、更新があったときはアクション (メール送信) を実行するものです。 さらに理解を深めるために、より高度なスケジュール ベースのワークフローを作成するチュートリアルに取り組みましょう。

> [!div class="nextstepaction"]
> [スケジュールに基づいたロジック アプリでトラフィックをチェックする](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
