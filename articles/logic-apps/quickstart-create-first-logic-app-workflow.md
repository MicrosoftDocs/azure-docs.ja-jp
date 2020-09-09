---
title: 自動化された統合ワークフローを作成する
description: クイックスタート - システム統合と Enterprise Application Integration (EAI) ソリューション向けに Azure Logic Apps を使用して、初めての自動化されたワークフローを構築します
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/23/2020
ms.openlocfilehash: 980e3e036257bbf5aa9743025bbfb55065176a39
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133301"
---
# <a name="quickstart-create-your-first-automated-integration-workflow-by-using-azure-logic-apps---azure-portal"></a>クイック スタート:Azure Logic Apps を使用して初めての自動化された統合ワークフローを作成する - Azure portal

このクイックスタートでは、空のロジック アプリの作成、トリガーとアクションの追加、ロジック アプリのテストなど、[Azure Logic Apps](logic-apps-overview.md) を使用して、初めてのワークフローを構築する方法を裏付ける基本的な一般概念を紹介します。 このクイックスタートでは、新しい項目のために Web サイトの RSS フィードを定期的にチェックするロジック アプリを構築します。 新しい項目が存在する場合、ロジック アプリから項目ごとにメールが送信されます。 完成したロジック アプリの大まかなワークフローは、次のようになります。

![サンプル ロジック アプリの大まかなワークフローを示す概念図。](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

このシナリオでは、Azure サブスクリプションまたは[無料 Azure アカウントへのサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)、Office 365 Outlook、Outlook.com、Gmail など、Azure Logic Apps によってサポートされているサービスの電子メール アカウントが必要です。 サポートされているその他の電子メール サービスについては、[こちらでコネクタの一覧を確認](/connectors/)してください。 この例のロジック アプリでは、Office 365 Outlook アカウントを使用します。 別の電子メール サービスを使用する場合、一般的な手順全体は同じですが、ユーザー インターフェイスはやや異なる可能性があります。

> [!IMPORTANT]
> Gmail コネクタの使用を希望する場合、ロジック アプリで制限なしにこのコネクタを使用できるのは、G-Suite ビジネス アカウントだけです。 Gmail コンシューマー アカウントを持っている場合は、Google によって承認された特定のサービスのみでこのコネクタを使用できるほか、[認証に使用する Google クライアント アプリを Gmail コネクタで作成する](/connectors/gmail/#authentication-and-bring-your-own-application)ことができます。 詳細については、「[Azure Logic Apps での Google コネクタのデータ セキュリティとプライバシー ポリシー](../connectors/connectors-google-data-security-privacy-policy.md)」を参照してください。

## <a name="create-your-logic-app"></a>ロジック アプリを作成する

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure portal の検索ボックスに「`logic apps`」と入力し、 **[Logic Apps]** を選択します。

   ![検索語句として "logic apps"、選択した検索結果として "Logic Apps" が表示された Azure portal の検索ボックスを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. **[Logic Apps]** ページで **[追加]** を選択します。

   ![ロジック アプリの一覧と選択された [追加] ボタンを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. **[Logic App]** に、以下に示すようにロジック アプリに関する詳細を入力します。

   ![新しいロジック アプリの詳細を含む、ロジック アプリの作成ペインを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **名前** | <*ロジック アプリ名*> | ロジック アプリの名前。リージョン間で一意である必要があり、文字、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`(`、`)`)、およびピリオド (`.`) のみを含めることができます。 この例では、"My-First-Logic-App" を使用します。 |
   | **サブスクリプション** | <*Azure サブスクリプション名*> | お使いの Azure サブスクリプション名 |
   | **リソース グループ** | <*Azure-resource-group-name*> | [Azure リソース グループ](../azure-resource-manager/management/overview.md)の名前。リージョン間で一意である必要があり、関連するリソースを整理するために使用されます。 この例では、"My-First-LA-RG" を使用します。 |
   | **場所** | <*Azure-region*> | ロジック アプリの情報の保存先となるリージョン。 この例では "米国西部" を使用します。 |
   | **Log Analytics** | Off | 診断ログの場合は、この設定を**オフ**のままにしてください。 |
   ||||

1. 準備ができたら、 **[確認および作成]** を選択します。 入力した詳細を確認し、 **[作成]** を選択します。

1. Azure によってアプリが正常にデプロイされたら、 **[リソースに移動]** を選択します。

   ![リソースのデプロイ ページと選択された [リソースに移動] ボタンを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   または、検索ボックスに名前を入力して、目的のロジック アプリを見つけて選択することもできます。

   ロジック アプリ デザイナーが開き、紹介ビデオとよく使用されるトリガーが含まれたページが表示されます。 **[テンプレート]** で **[空のロジック アプリ]** を選択します。

   ![Logic Apps デザイナーのテンプレート ギャラリーと選択された "空のロジック アプリ" テンプレートを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

次に、新しい RSS フィード項目が発行されたときに起動される[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 すべてのロジック アプリは必ずトリガーから起動され、トリガーは、特定のイベントが発生するか特定の条件が満たされたときに起動されます。 トリガーが起動されるたびに、Azure Logic Apps エンジンによって、ワークフローを開始および実行するロジック アプリ インスタンスが作成されます。

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>RSS トリガーを追加する

1. **ロジック アプリ デザイナー**の検索ボックスの下で、 **[すべて]** を選択します。

1. RSS コネクタを見つけるために、検索ボックスに「`rss`」と入力します。 トリガーの一覧から、RSS トリガー **[フィード項目が発行される場合]** を選択します。

   ![検索ボックスに入力した「rss」と、選択されたトリガー [フィード項目が発行される場合] が表示されている Logic Apps デザイナーを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. この手順の説明に基づき、使用するトリガーの情報を指定します。

   ![RSS URL、頻度、間隔などの RSS トリガー設定が表示されている Logic Apps デザイナーを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **RSS フィードの URL** | <*RSS-feed-URL*> | 監視する RSS フィードのリンク。 この例では、ウォール ストリート ジャーナルの RSS フィード (`https://feeds.a.dj.com/rss/RSSMarketsMain.xml`) を使用していますが、必要に応じて独自の RSS フィード URL を使用できます。 |
   | **間隔** | 1 | チェックの間隔 (単位数) |
   | **頻度** | 分 | チェックの間隔に使う時間の単位 |
   ||||

   ロジック アプリのトリガーには、間隔と頻度の組み合わせでそのスケジュールを定義します。 このロジック アプリでは、1 分おきにフィードをチェックします。

1. ここでは、トリガーの詳細を非表示にするために、トリガーのタイトル バー内をクリックします。

   ![ロジック アプリの図形が折りたたまれた状態の Logic Apps デザイナーを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

ロジック アプリは現在稼働していますが、RSS フィードをチェックすること以外は何もしていません。 そこで、トリガーが起動したときに反応するアクションを追加します。

## <a name="add-the-send-email-action"></a>"電子メールの送信" アクションを追加する

今度は、RSS フィードに新しい項目が発行されたときにメールを送信する[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。

1. **[フィード項目が発行される場合]** トリガーの下の **[新しいステップ]** を選択します。

   ![[新しいステップ] が表示された Logic Apps デザイナーを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. **[アクションを選択してください]** と検索ボックスの下の **[すべて]** を選択します。

1. 検索ボックスに「`send an email`」と入力すると、このアクションを提供するコネクタが見つかります。 アクションの一覧にフィルターをかけて特定のアプリまたはサービスに絞り込むには、最初にそのアプリまたはサービスを選択できます。

   たとえば、Microsoft の職場または学校アカウントを使用していて、Office 365 Outlook を使用する場合は、 **[Office 365 Outlook]** を選択します。 また、個人用の Microsoft アカウントを使用している場合は、Outlook.com を選択することもできます。 この例では、Office 365 Outlook が引き続き使用されます。

   ![Logic Apps デザイナーと選択された Office 365 Outlook コネクタを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   これで、`send an email` など、使用するアクションをより簡単に見つけて選択できるようになりました。

   ![Logic Apps デザイナーとフィルターで絞り込まれたアクションの一覧を示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. 選択した電子メール コネクタによって ID の認証を求められた場合は、その手順を完了して、ロジック アプリと電子メール サービス間の接続を作成します。

   > [!NOTE]
   > 今回の例では、手動で ID を認証します。 ただし、認証を必要とするコネクタは、それらがサポートしている認証の種類によって異なります。 また、認証の処理方法を設定するオプションも用意されています。 たとえば、デプロイに Azure Resource Manager テンプレートを使用する場合、接続情報など、頻繁または簡単に変更される入力をパラメーター化してセキュリティを向上させることができます。 詳細については、以下のトピックを参照してください。
   >
   > * [デプロイ用のテンプレート パラメーター](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth 接続を承認する](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [マネージド ID を利用してアクセスを認証する](../logic-apps/create-managed-service-identity.md)
   > * [ロジック アプリのデプロイ用の接続を認証する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. **[メールの送信]** アクションで、メールに含める情報を指定します。

   1. **[宛先]** ボックスに、受信者の電子メール アドレスを入力します。 テスト目的のために、ご自身の電子メール アドレスを使用できます。

      **[動的なコンテンツの追加]** リストが表示されますが、ここでは無視してください。 いくつかの編集ボックス内をクリックすると、このリストが表示され、現在のアクションの入力として使用できる、前のステップからの使用可能な出力がすべて表示されます。

   1. **[件名]** ボックスに「`New RSS item: `」と入力し、その後ろに空白スペースを追加します。

      ![[メールの送信] アクションと、[件名] プロパティ ボックス内のカーソルが表示されている Logic Apps デザイナーを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. **[動的なコンテンツの追加]** リストから、[フィード項目が発行される場合] トリガーからの出力である **[フィード タイトル]** を選択します。これにより、RSS 項目のタイトルを使用できるようになります。

      ![[メールの送信] アクションのほか、動的なコンテンツのリストが開かれ、出力の [フィード タイトル] が選択されている [件名] プロパティ ボックス内のカーソルが表示されている Logic Apps デザイナーを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > 動的なコンテンツのリストで、[フィード項目が発行される場合] トリガーからの出力が表示されない場合は、アクションのヘッダーの横にある **[表示を増やす]** を選択します。
      > 
      > ![開かれた動的なコンテンツのリストと、トリガーに対して [表示を増やす] が選択されている Logic Apps デザイナーを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      操作を終了すると、電子メールの件名は、次の例のようになります。

      ![[メールの送信] アクションと、[フィード タイトル] プロパティが含まれるサンプルのメールの件名が表示されている Logic Apps デザイナーを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      デザイナーに "For each" ループが表示される場合、 **[categories-item]\(カテゴリ-項目\)** トークンなど、配列のトークンを選択したことが原因です。 この種のトークンを参照するアクションの前後には、デザイナーによってこのループが自動的に追加されます。 そうすることで個々の配列項目に同じアクションが実行されます。 ループを削除するには、ループのタイトル バーの**省略記号** ( **...** ) を選択し、 **[削除]** を選択します。

   1. **[本文]** ボックスには、メール本文に使用するテキストを次のように入力し、対応するトークンを選択します。 編集ボックスで空白行を追加するには、Shift + Enter キーを押します。

      ![[メールの送信] アクションと、[本文] ボックス内で選択されたプロパティが表示されている Logic Apps デザイナーを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

      | プロパティ | 説明 |
      |----------|-------------|
      | **フィード タイトル** | 項目のタイトル |
      | **フィードの公開日付** | 項目の公開日時 |
      | **プライマリ フィード リンク** | 項目の URL |
      |||

1. ロジック アプリを保存します。

ロジック アプリのテストに進みます。

## <a name="run-your-logic-app"></a>ロジック アプリを実行する

ロジック アプリを手動で開始するには、デザイナーのツール バーの **[実行]** を選択します。 または、指定したスケジュール (1 分おき) に従ってロジック アプリが RSS フィードをチェックするのを待ちます。

![デザイナーのツール バーの [実行] ボタンが選択されている Logic Apps デザイナーを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

RSS フィードに新しい項目がある場合、ロジック アプリは新しい項目ごとに電子メールを送信します。 それ以外の場合、ロジック アプリは、次の機会を待ってからもう一度チェックします。 電子メールが届かない場合は、迷惑メール フォルダーを確認してください。

このロジック アプリからは、次のようなメールが送信されます。

![新しい RSS フィード項目が生成されたときに受信したサンプルの電子メールを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

技術的には、トリガーによって RSS フィードがチェックされて新しい項目が見つかると、トリガーが起動され、ワークフローのアクションを実行するロジック アプリ ワークフローのインスタンスが Azure Logic Apps エンジンによって作成されます。 トリガーで新しい項目が見つからなかった場合、トリガーは起動せず、ワークフローのインスタンス作成は "スキップ" されます。

お疲れさまでした。初めてのロジック アプリを Azure portal で正しく作成し、実行することができました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このサンプルの必要がなくなったら、ロジック アプリと関連リソースが含まれるリソース グループを削除しましょう。

1. Azure の検索ボックスに「`resource groups`」と入力し、 **[リソース グループ]** を選択します。

   ![検索語句 "resource groups" が入力されている Azure portal の検索ボックスを示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. 目的のロジック アプリのリソース グループを見つけて選択します。 **[概要]** ウィンドウで、 **[リソース グループの削除]** を選択します。

   ![選択したリソース グループと [リソース グループの削除] ボタンが表示されている Azure portal を示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. 確認ウィンドウが表示されたら、リソース グループ名を入力して、 **[削除]** を選択します。

   ![確認ペインと削除対象として入力したリソース グループ名が表示されている Azure portal を示すスクリーンショット。](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> ロジック アプリを削除にすると、新しい実行は開始されなくなります。 すべての進行中および保留中の実行は取り消されます。 何千もの実行がある場合、取り消しが完了するまでかなりの時間がかかる場合があります。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、初めてのロジック アプリを作成しました。作成したロジック アプリは、指定したスケジュール (1 分おき) で RSS の更新をチェックし、更新があったときはアクション (メール送信) を実行するものです。 さらに理解を深めるために、より高度なスケジュール ベースのワークフローを作成するチュートリアルに取り組みましょう。

> [!div class="nextstepaction"]
> [スケジュールに基づいたロジック アプリでトラフィックをチェックする](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
