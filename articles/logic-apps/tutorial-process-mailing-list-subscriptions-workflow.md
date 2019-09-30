---
title: 承認ベースの自動化されたワークフローを作成する - Azure Logic Apps
description: チュートリアル - Azure Logic Apps を使用して、メーリング リストの登録を処理する承認ベースの自動化されたワークフローを作成する
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: 734a6be81a8052b2894f4c27b165bb8dc4f14caf
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71171552"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>チュートリアル:Azure Logic Apps を使用して承認ベースの自動化されたワークフローを作成する

このチュートリアルでは、承認ベースのワークフローを自動化する[ロジック アプリ](../logic-apps/logic-apps-overview.md)を作成する方法について説明します。 具体的には、このロジック アプリは [MailChimp](https://mailchimp.com/) サービスによって管理されるメーリング リストの登録申請を処理します。 このロジック アプリは、登録申請用のメール アカウントを監視して承認依頼を送信し、承認済みのメンバーをメーリング リストに追加するものです。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 空のロジック アプリを作成します。
> * 登録申請のメールを監視するトリガーを追加します。
> * 申請の承認/拒否を依頼するメールの送信アクションを追加します。
> * 承認応答をチェックする条件を追加します。
> * 承認済みのメンバーをメーリング リストに登録するアクションを追加します。
> * これらのメンバーが正常にリストに登録されているかどうかをチェックする条件を追加します。
> * これらのメンバーが正常にリストに登録されているかどうかを伝えるメールの送信アクションを追加します。

完成したロジック アプリの大まかなワークフローは、次のようになります。

![完成したロジック アプリの概観](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションがない場合は、始める前に[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* ご自分のロジック アプリで承認済みメンバーのメール アドレスを追加できる "test-members-ML" という名前のリストが含まれた MailChimp アカウント。 アカウントがない場合は、[無料アカウントにサインアップ](https://login.mailchimp.com/signup/)して、[MailChimp リストの作成方法](https://us17.admin.mailchimp.com/lists/#)を確認してください。

* 承認ワークフローに対応した Office 365 Outlook または Outlook.com のメール アカウント。 この記事では Office 365 Outlook を使います。 別のメール アカウントを使う場合、おおよその手順は変わりませんが、UI の表示がやや異なることがあります。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-your-logic-app"></a>ロジック アプリを作成する

1. Azure のメイン メニューで、 **[リソースの作成]**  >  **[統合]**  >  **[ロジック アプリ]** の順に選択します。

   ![ロジック アプリを作成する](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

1. **[ロジック アプリの作成]** で、ロジック アプリに関する情報を次のように入力します。 完了したら **[作成]** を選択します。

   ![ロジック アプリに関する情報の入力](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **Name** | LA-MailingList | ロジック アプリの名前。文字、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`(`、`)`)、およびピリオド (`.`) のみを含めることができます。 この例では、"LA-MailingList" を使用します。 |
   | **サブスクリプション** | <*Azure サブスクリプションの名前*> | お使いの Azure サブスクリプション名 |
   | **リソース グループ** | LA-MailingList-RG | [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前。関連するリソースの整理に使用します。 この例では、"LA-MailingList-RG" を使用します。 |
   | **Location** | 米国西部 | ロジック アプリの情報の保存先となるリージョン。 この例では "米国西部" を使用します。 |
   | **Log Analytics** | オフ | 診断ログの場合は、この設定を**オフ**のままにしてください。 |
   ||||

1. Azure によってアプリがデプロイされた後、Azure ツール バーで、対象のデプロイされたロジック アプリに対して、 **[通知]**  >  **[リソースに移動]** の順に選択します。

   ![リソースに移動](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app.png)

   または、検索ボックスに名前を入力して、目的のロジック アプリを見つけて選択することもできます。

   Logic Apps デザイナーが開き、紹介ビデオとよく使用されるトリガーおよびロジック アプリのパターンが含まれたページが表示されます。 **[テンプレート]** で **[空のロジック アプリ]** を選択します。

   ![[空のロジック アプリ] テンプレートを選択する](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

次に、登録申請を含んだ受信メールをリッスンする[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 各ロジック アプリは必ずトリガーから起動されます。これは、特定のイベントが発生するか、新しいデータが特定の条件を満たしたときに起動されます。 詳細については、[初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="add-trigger-to-monitor-emails"></a>メールを監視するトリガーを追加する

1. ロジック アプリ デザイナー上の検索ボックスに、フィルターとして「`when email arrives`」と入力します。 **[トリガー]** リストから、自分のメール プロバイダーの **[新しいメールが届いたとき]** トリガーを選択します。

   この例では Office 365 Outlook トリガーを使用します。

   ![メール プロバイダーの [新しいメールが届いたとき] トリガーの選択](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Azure の職場または学校アカウントには、Office 365 Outlook を選択します。
   * 個人用 Microsoft アカウントには、Outlook.com を選択します。

1. 求められた場合は、お使いのメール アカウントへの接続が Logic Apps によって作成されるように、自分の資格情報を使用してそのメール アカウントにサインインします。

1. トリガーで、すべての新しいメールを確認するための条件を指定します。

   1. メールをチェックするフォルダー、間隔、頻度を指定します。

      ![メールをチェックするフォルダー、間隔、頻度を指定](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | プロパティ | 値 | 説明 |
      |----------|-------|-------------|
      | **フォルダー** | `Inbox` | 監視するメール フォルダー |
      | **間隔** | `1` | チェックの間隔 (単位数) |
      | **頻度** | `Hour` | 定期実行の時間の単位 |
      ||||

   1. 次に、メールの件名に対してフィルターを適用できるよう、トリガーに次のプロパティを追加します。 **[新しいパラメーターの追加] リスト**を開いて、 **[件名フィルター]** プロパティを選択します。

      ![新しいパラメーターの追加](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      このトリガーのプロパティの詳細については、[Office 365 Outlook のコネクタ リファレンス](https://docs.microsoft.com/connectors/office365/)または [Outlook.com のコネクタ リファレンス](https://docs.microsoft.com/connectors/outlook/)を参照してください。

   1. このプロパティがトリガーに表示されたら、このテキストを入力します: `subscribe-test-members-ML`。

      ![件名フィルター テキストの入力、新しいパラメーターの追加](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. ここではトリガーの詳細を非表示にするために、トリガーのタイトル バーをクリックします。

   ![シェイプを折りたたんで詳細を非表示](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. ロジック アプリを保存し、 デザイナーのツール バーで、 **[保存]** を選択します。

ロジック アプリは現在稼働していますが、受信メールをチェックすること以外は何もしていません。 そこで、トリガーが起動したときに反応するアクションを追加します。

## <a name="send-approval-email"></a>承認の電子メールを送信します

トリガーが完成したら、申請の承認/拒否を依頼するメールの送信[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。

1. トリガーで、 **[新しいステップ]** を選択します。 

1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「`approval`」と入力します。 アクションの一覧で、ご自分のメール プロバイダーの **[承認のメールを送信します]** アクションを選択します。 

   この例では Office 365 Outlook アクションを使用します。

   ![[承認のメールを送信します] アクションの選択](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. 説明に従って、このアクションに関する情報を指定します。 

   ![[承認のメールを送信します] のプロパティ](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **To** | <*your-email-address*> | 承認者のメール アドレス。 テスト目的のため、ご自身のアドレスを使ってください。 この例では、架空の "sophia.owen@fabrikam.com" メール アドレスを使用します。 |
   | **[件名]** | `Approve member request for test-members-ML` | わかりやすいメールの件名 |
   | **ユーザー オプション** | `Approve, Reject` | 承認者が選択できる回答の選択肢。 既定では、承認者が回答として [Approve] または [Reject] を選択できます。 |
   ||||

   特定の編集ボックス内をクリックしたときに表示される動的コンテンツ リストは、ここでは無視してください。 このリストでは、先行するアクションから、ワークフローの入力として使用できる出力を選択できます。

   このアクションのプロパティの詳細については、[Office 365 Outlook のコネクタ リファレンス](https://docs.microsoft.com/connectors/office365/)または [Outlook.com のコネクタ リファレンス](https://docs.microsoft.com/connectors/outlook/)を参照してください。
 
1. ロジック アプリを保存し、

次に、承認者によって選択された回答をチェックする条件を追加します。

## <a name="check-approval-response"></a>承認応答をチェックする

1. **[承認のメールを送信します]** アクションの下で、 **[新しいステップ]** を選択します。

1. **[アクションを選択してください]** で **[Built-in]\(組み込み\)** を選択します。 検索ボックスに、フィルターとして「`condition`」と入力します。 アクションの一覧で、 **[条件]** アクションを選択します。

   ![[条件] を選択](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition.png)

1. 条件の名前をわかりやすい名前に変更します。

   1. 条件のタイトル バーにある**省略記号** ( **...** ) ボタン、 **[名前の変更]** の順に選択します。

      ![条件の名前の変更](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition.png)

   1. 条件の名前をわかりやすく「`If request approved`」に変更します。

1. 承認者によって **[Approve]** が選択されたかどうかをチェックする条件を作成します。

   1. 条件内で、左側にある **[値の選択]** ボックス内をクリックします。

   1. 表示される動的コンテンツ リストの **[承認のメールを送信します]** で、 **[SelectedOption]** プロパティを選択します。

      ![[承認の電子メールを送信します] の [SelectedOption] を選択](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. 中央の比較ボックスで、 **[次の値に等しい]** 演算子を選択します。

   1. 条件の右側にある **[値の選択]** ボックスで、「`Approve`」というテキストを入力します。

      完成した条件は次の例のようになります。

      ![完成した条件](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. ロジック アプリを保存し、

次に、レビュー担当者が申請を承認したときにロジック アプリによって実行されるアクションを指定します。 

## <a name="add-member-to-mailchimp-list"></a>MailChimp リストにメンバーを追加する

次は、承認済みのメンバーをメーリング リストに追加するアクションを追加します。

1. 条件の **[true の場合]** 分岐で、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** でフィルターとして「`mailchimp`」を入力し、 **[メンバーをリストに追加する]** アクションを選択します。

   ![[メンバーをリストに追加する] アクションの選択](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. MailChimp アカウントにアクセスするよう求められた場合は、ご自分の MailChimp 資格情報を使用してサインインします。

1. 次に示す説明に従って、このアクションの情報を指定します。

   ![[メンバーをリストに追加する] の情報を入力](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **リスト ID** | はい | `test-members-ML` | ご自分の MailChimp メーリング リストの名前。 この例では、"test-members-ML" を使用します。 |
   | **状態** | はい | `subscribed` | 新しいメンバーの登録状態を選択します。 この例では "subscribed" を使用します。 <p>詳細については、「[Manage subscribers with the MailChimp API (MailChimp API を使った購読者管理)](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/)」を参照してください。 |
   | **メール アドレス** | はい | <*new-member-email-address*> | 動的コンテンツ リストから、 **[新しいメールが届いたとき]** の **[差出人]** を選択します。ここから、新しいメンバーのメール アドレスが渡されます。 |
   ||||

   このアクションのプロパティの詳細については、[MailChimp のコネクタ リファレンス](https://docs.microsoft.com/connectors/mailchimp/)を参照してください。

1. ロジック アプリを保存し、

次に、新しいメンバーがメーリング リストに正常に登録されたかどうかをチェックするための条件を追加します。 そうすることで、この操作の成否に関する通知をロジック アプリから受け取ることができます。

## <a name="check-for-success-or-failure"></a>成功か失敗かをチェックする

1. **[true の場合]** 分岐の **[メンバーをリストに追加する]** アクションで、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** で **[Built-in]\(組み込み\)** を選択します。 検索ボックスに、フィルターとして「`condition`」と入力します。 アクションの一覧で、 **[条件]** を選択します。

1. 条件の名前をわかりやすく「`If add member succeeded`」に変更します。

1. 承認済みのメンバーが正常にメーリング リストに登録されているかどうかをチェックする条件を作成します。

   1. 条件の **[値の選択]** ボックス内をクリックします。これは条件の左側にあります。 動的コンテンツ リストから、 **[メンバーをリストに追加する]** の **[状態]** プロパティを選択します。

      たとえば、条件はこの例のようになります。

      ![[メンバーをリストに追加する] の [Status]\(ステータス\) を選択](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. 中央の比較ボックスで、 **[次の値に等しい]** 演算子を選択します。

   1. 条件の右側にある **[値の選択]** ボックスで、「`subscribed`」というテキストを入力します。

      完成した条件は次の例のようになります。

      ![完成した条件](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

次に、承認されたメンバーのメーリング リストへの登録に成功するか失敗したときに送信されるメールを設定します。

## <a name="send-email-if-member-added"></a>メンバーが追加された場合にメールを送信する

1. **[If add member succeeded]** 条件の下にある **[true の場合]** 分岐で、 **[アクションの追加]** を選択します。

   ![条件の [true の場合] 分岐で [アクションの追加] を選択](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. **[アクションの選択]** の検索ボックスにフィルターとして「`outlook send email`」と入力し、 **[メールの送信]** アクションを選択します。

   ![[メールの送信] アクションの追加](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. アクションの名前をわかりやすく「`Send email on success`」に変更します。

1. このアクションの情報を次のように入力します。

   ![成功時に送信されるメールの情報を入力](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **To** | はい | <*your-email-address*> | 成功時のメールの送信先アドレス。 テスト目的で自分の電子メール アドレスを使用できます。 |
   | **[件名]** | はい | <*subject-for-success-email*> | 成功時に送信されるメールの件名。 このチュートリアルでは、次のテキストを入力します。 <p>`Success! Member added to "test-members-ML": ` <p>動的コンテンツ リストから、 **[メンバーをリストに追加する]** の **[メール アドレス]** プロパティを選択します。 |
   | **本文** | はい | <*body-for-success-email*> | 成功時に送信されるメールの本文の内容。 このチュートリアルでは、次のテキストを入力します。 <p>`New member has joined "test-members-ML":` <p>動的コンテンツ リストから、 **[メール アドレス]** プロパティを選択します。 <p>次の行で、「`Member opt-in status: `」というテキストを入力します。 <p> 動的コンテンツ リストから、 **[メンバーをリストに追加する]** の **[状態]** プロパティを選択します。 |
   |||||

1. ロジック アプリを保存し、

## <a name="send-email-if-member-not-added"></a>メンバーが追加されなかった場合にメールを送信する

1. **[If add member succeeded]** 条件の下にある **[false の場合]** 分岐で、 **[アクションの追加]** を選択します。

   ![条件の [false の場合] 分岐で [アクションの追加] を選択](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. **[アクションの選択]** の検索ボックスにフィルターとして「`outlook send email`」と入力し、 **[メールの送信]** アクションを選択します。

   ![[電子メールの送信] アクションを追加](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. アクションの名前をわかりやすく「`Send email on failure`」に変更します。

1. 次に示す説明に従って、このアクションの情報を指定します。

   ![失敗時に送信されるメールの情報を入力](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **To** | はい | <*your-email-address*> | 失敗時のメールの送信先アドレス。 テスト目的で自分の電子メール アドレスを使用できます。 |
   | **[件名]** | はい | <*subject-for-failure-email*> | 失敗時に送信されるメールの件名。 このチュートリアルでは、次のテキストを入力します。 <p>`Failed, member not added to "test-members-ML": ` <p>動的コンテンツ リストから、 **[メンバーをリストに追加する]** の **[メール アドレス]** プロパティを選択します。 |
   | **本文** | はい | <*body-for-failure-email*> | 失敗時に送信されるメールの本文の内容。 このチュートリアルでは、次のテキストを入力します。 <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. ロジック アプリを保存し、 

次に、ロジック アプリをテストします。この時点で、ロジック アプリは次のようになっています。

![完成したロジック アプリ](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>ロジック アプリを実行する

1. メーリング リストの登録申請メールを自分宛てに送信します。 申請が受信トレイに配信されるまで待ちます。

1. ロジック アプリを手動で開始するには、デザイナーのツール バーの **[実行]** を選択します。 

   メールの件名がトリガーの件名フィルターと一致した場合、登録申請の承認依頼メールがロジック アプリから届きます。

1. 承認メールで **[Approve]** を選択します。

1. 購読者のメール アドレスがメーリング リストに存在しない場合、その人物のメール アドレスが追加され、次のようなメールが届きます。

   ![成功時のメール](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   購読者を追加できなかった場合は、次のようなメールが届きます。

   ![失敗時のメール](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   メールが届かない場合は、迷惑メール フォルダーを確認してください。 この種のメールは、迷惑メール フィルターによってリダイレクトされる場合があります。 また、ロジック アプリが正しく動作しているかどうかわからない場合は、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

お疲れさまでした。Azure、Microsoft サービスなどの SaaS アプリの垣根を越えて情報を統合するロジック アプリを作成し、実行することができました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このサンプル ロジック アプリが不要になったら、ロジック アプリと関連リソースが含まれるリソース グループを削除しましょう。 

1. Azure のメイン メニューから **[リソース グループ]** に移動し、対象のロジック アプリのリソース グループを選択します。

1. リソース グループ メニューで、 **[概要]**  >  **[リソース グループの削除]** を選択します。 

   ![[概要] > [リソース グループの削除]](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. 確認のためにリソース グループ名を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、メーリング リスト登録申請の承認依頼を管理するロジック アプリを作成しました。 次回は、Azure Storage と Azure Functions など各種 Azure サービスを統合することによって、メールの添付ファイルを処理して保存するロジック アプリを作成する方法について詳しく見ていきましょう。

> [!div class="nextstepaction"]
> [メールの添付ファイルの処理](../logic-apps/tutorial-process-email-attachments-workflow.md)
