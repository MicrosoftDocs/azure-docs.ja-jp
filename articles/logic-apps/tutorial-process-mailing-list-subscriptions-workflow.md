---
title: 承認ベースの自動化されたワークフローを作成する
description: チュートリアル - Azure Logic Apps を使用して、メーリング リストの登録を処理する承認ベースの自動化されたワークフローを作成する
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/07/2020
ms.openlocfilehash: 1690b8d143b86e5caa691f5f8f479f715f57f0c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054655"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>チュートリアル:Azure Logic Apps を使用して承認ベースの自動化されたワークフローを作成する

このチュートリアルでは、承認ベースのワークフローを自動化する[ロジック アプリ](../logic-apps/logic-apps-overview.md)のサンプルを作成する方法について説明します。 具体的には、このサンプルのロジック アプリは [MailChimp](https://mailchimp.com/) サービスによって管理されるメーリング リストの登録申請を処理します。 このロジック アプリには、申請の電子メール アカウントの監視から、承認者への申請の送信、申請が承認されたかどうかの確認、メーリング リストに対する承認されたメンバーの追加、新しいメンバーがリストに追加されたかどうかの確認まで、さまざまなステップが存在します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 空のロジック アプリを作成します。
> * 登録申請のメールを監視するトリガーを追加します。
> * 申請の承認/拒否を依頼するメールの送信アクションを追加します。
> * 承認応答をチェックする条件を追加します。
> * 承認済みのメンバーをメーリング リストに登録するアクションを追加します。
> * これらのメンバーが正常にリストに登録されているかどうかをチェックする条件を追加します。
> * これらのメンバーが正常にリストに登録されているかどうかを伝えるメールの送信アクションを追加します。

完成したロジック アプリの大まかなワークフローは、次のようになります。

![完成したロジック アプリの概観](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* MailChimp アカウント。このアカウントには、ロジック アプリから承認済みメンバーのメール アドレスを追加できるリスト "test-members-ML" を作成しておく必要があります。 アカウントがない場合は、[無料アカウントにサインアップ](https://login.mailchimp.com/signup/)して、[MailChimp リストの作成方法](https://us17.admin.mailchimp.com/lists/#)を確認してください。

* Logic Apps がサポートするメール プロバイダー (Office 365 Outlook、Outlook.com、Gmail など) のメール アカウント。 その他のプロバイダーについては、[こちらのコネクタ一覧を参照](/connectors/)してください。 このクイックスタートでは、職場または学校アカウントで Office 365 Outlook を使用します。 別のメール アカウントを使う場合、おおよその手順は変わりませんが、UI がやや異なることがあります。

* 承認ワークフローに対応した Office 365 Outlook または Outlook.com のメール アカウント。 このチュートリアルでは、Office 365 Outlook を使用します。 別のメール アカウントを使う場合、おおよその手順は変わりませんが、UI の表示がやや異なることがあります。

* ロジック アプリが特定の IP アドレスへのトラフィックを制限するファイアウォールを経由して通信する必要がある場合、そのファイアウォールは、Logic Apps サービスまたはロジック アプリが存在する Azure リージョンのランタイムが使用する [インバウンド](logic-apps-limits-and-config.md#inbound)と [アウトバウンド](logic-apps-limits-and-config.md#outbound)の IP アドレスの "*両方*" のアクセスを許可する必要があります。 また、ロジック アプリが Office 365 Outlook コネクタや SQL コネクタなどの [マネージド コネクタ](../connectors/apis-list.md#managed-api-connectors)を使用している場合、または [カスタム コネクタ](/connectors/custom-connectors/)を使用している場合、そのファイアウォールでは、ロジック アプリの Azure リージョン内の "*すべて*" の [マネージド コネクタ アウトバウンド IP アドレス](logic-apps-limits-and-config.md#outbound)へのアクセスを許可する必要もあります。

## <a name="create-your-logic-app"></a>ロジック アプリを作成する

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。 Azure ホーム ページで、 **[リソースの作成]** を選択します。

1. Azure Marketplace メニューで、 **[統合]**  >  **[ロジック アプリ]** を選択します。

   ![Azure Marketplace メニューで [統合] と [ロジック アプリ] が選択されているスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. **[ロジック アプリ]** ペインで、作成するロジック アプリについて、以下に示す情報を入力します。

   ![ロジック アプリの作成ペインと新しいロジック アプリに設定する情報を示すスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **サブスクリプション** | <*Azure サブスクリプション名*> | Azure サブスクリプション名。 この例では、`Pay-As-You-Go` を使用します。 |
   | **リソース グループ** | LA-MailingList-RG | [Azure リソース グループ](../azure-resource-manager/management/overview.md)の名前。関連するリソースの整理に使用します。 この例では、`LA-MailingList-RG` という名前の新しいリソース グループを作成します。 |
   | **名前** | LA-MailingList | ロジック アプリの名前。文字、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`(`、`)`)、およびピリオド (`.`) のみを含めることができます。 この例では、`LA-MailingList` を使用します。 |
   | **場所** | 米国西部 | ロジック アプリの情報の保存先となるリージョン。 この例では、`West US` を使用します。 |
   | **Log Analytics** | Off | 診断ログの場合は、この設定を **オフ** のままにしてください。 |
   ||||

1. 完了したら、 **[確認および作成]** を選択します。 Azure によってロジック アプリに関する情報が検証されたら、 **[作成]** を選択します。

1. Azure によってアプリがデプロイされたら、 **[リソースに移動]** を選択します。

   Azure によって Logic Apps のテンプレートの選択ペインが開き、紹介動画、一般的に使用されるトリガー、およびロジック アプリのテンプレート パターンが表示されます。

1. 動画と一般的なトリガーのセクションの下にある **[テンプレート]** セクションまでスクロールし、 **[空のロジック アプリ]** を選択します。

   ![Logic Apps のテンプレートの選択ペインで、[空のロジック アプリ] が選択されているスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

次に、登録申請が含まれる受信メールをリッスンする Outlook [トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 ロジック アプリはいずれも必ずトリガーにより起動され、トリガーは、特定のイベントが発生するか、新しいデータが特定の条件を満たしたときに起動されます。 詳細については、[初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="add-trigger-to-monitor-emails"></a>メールを監視するトリガーを追加する

1. Logic Apps デザイナーの検索ボックスに「`when email arrives`」と入力し、**新しい電子メールが届いたとき** という名前のトリガーを選択します。

   * Azure の職場または学校アカウントには、**Office 365 Outlook** を選択します。
   * 個人用 Microsoft アカウントには、**Outlook.com** を選択します。

   この例では、Office 365 Outlook を選択して続行します。

   ![Logic Apps デザイナーの検索ボックスに検索語句「when email arrives」が入力され、"新しい電子メールが届いたとき" トリガーが選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. まだ接続していない場合は、サインインし、メッセージが表示されたら電子メール アカウントへのアクセスを認証します。

   Azure Logic Apps によって、ご自分のメール アカウントへの接続が作成されます。

1. トリガーに、新しいメールを確認する際の条件を指定します。

   1. メールを確認するフォルダーを指定します。他のプロパティは、既定の設定値のままにしておきます。

      !["新しい電子メールが届いたとき" アクションが表示され、[フォルダー] が [受信トレイ] に設定されているデザイナーのスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. メールに対して件名に応じたフィルター処理ができるようにするために、トリガーに **[件名フィルター]** プロパティを追加します。 **[新しいパラメーターの追加]** リストを開き、 **[件名フィルター]** を選択します。

      ![[新しいパラメーターの追加] リストが開いて、[件名フィルター] が選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      このトリガーのプロパティの詳細については、[Office 365 Outlook のコネクタ リファレンス](/connectors/office365/)または [Outlook.com のコネクタ リファレンス](/connectors/outlook/)を参照してください。

   1. このプロパティがトリガーに表示されたら、このテキストを入力します: `subscribe-test-members-ML`。

      ![[件名フィルター] プロパティに「subscribe-test-members-ML」というテキストが入力されている状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. ここでトリガーの詳細を非表示にするには、シェイプのタイトル バー内をクリックしてシェイプを折りたたみます。

   ![折りたたまれたトリガー シェイプを示すスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

ロジック アプリは現在稼働していますが、受信メールをチェックすること以外は何もしていません。 そこで、トリガーが起動したときに反応するアクションを追加します。

## <a name="send-approval-email"></a>承認の電子メールを送信します

トリガーが完成したら、申請の承認/拒否を依頼するメールの送信[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。

1. Logic Apps デザイナーで、 **[新しい電子メールが届いたとき]** トリガーの下にある **[新しいステップ]** を選択します。

1. **[操作を選択してください]** の検索ボックスに「`send approval`」と入力し、 **[承認のメールを送信します]** というアクションを選択します。

   ![フィルター処理により [操作を選択してください] リストに "承認" 関連のアクションが表示され、そのうち [承認のメールを送信します] アクションが選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. ここで、次の画像と説明に従い、特定のプロパティの値を入力します。 その他はすべて既定値のままにしておきます。 これらのプロパティの詳細については、[Office 365 Outlook のコネクタ リファレンス](/connectors/office365/)または [Outlook.com のコネクタ リファレンス](/connectors/outlook/)を参照してください。

   ![[承認のメールを送信します] のプロパティを示すスクリーンショット](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **To** | <*承認者のメールアドレス*> | 承認者のメール アドレス。 テスト目的のため、ご自身のアドレスを使ってください。 この例では、`sophiaowen@fabrikam.com` という架空のメール アドレスを使用しています。 |
   | **件名** | `Approve member request for test-members-ML` | わかりやすいメールの件名 |
   | **ユーザー オプション** | `Approve, Reject` | このプロパティでは、承認者が選択できる応答の選択肢を指定します。既定値は **[Approve]** と **[Reject]** です。 |
   ||||

   > [!NOTE]
   > 一部の編集ボックスは、その内部をクリックすると、動的コンテンツ リストが表示されますが、現時点では無視してかまいません。 このリストには以前のアクションの出力が表示され、ワークフローの後続のアクションに対する入力として選択できます。
 
1. ロジック アプリを保存します。

次に、承認者によって選択された応答を確認する条件を追加します。

## <a name="check-approval-response"></a>承認応答をチェックする

1. **[承認のメールを送信します]** アクションの下で、 **[新しいステップ]** を選択します。

1. **[操作を選択してください]** で **[組み込み]** を選択します。 検索ボックスに「`condition`」と入力し、「 **[条件]** 」という名前のアクションを選択します。

   ![[組み込み] が選択された状態で [操作を選択してください] の検索ボックスに検索語句「check」が入力され、かつ [条件] アクションが選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. **[条件]** のタイトル バーにある **省略記号** ( **...** ) ボタンを押し、 **[名前の変更]** を選択します。 条件の名前をわかりやすく「`If request approved`」に変更します。

   ![省略記号ボタンが押され、[設定] リストが開き、[名前の変更] コマンドが選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. 承認者によって **[Approve]** が選択されたかどうかをチェックする条件を作成します。

   1. 条件の左側にある **[値の選択]** ボックス内をクリックします。

   1. 表示される動的コンテンツ リストの **[承認のメールを送信します]** で、 **[SelectedOption]** プロパティを選択します。

      ![動的コンテンツ リストの [承認のメールを送信します] セクションで [SelectedOption] 出力が選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. 中央の比較ボックスで、 **[次の値に等しい]** 演算子を選択します。

   1. 条件の右側にある **[値の選択]** ボックスには、テキスト「`Approve`」を入力します。

      完成した条件は次の例のようになります。

      ![申請が承認された場合の条件の完成例を示すスクリーンショット](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. ロジック アプリを保存します。

次に、レビュー担当者が申請を承認したときにロジック アプリによって実行されるアクションを指定します。 

## <a name="add-member-to-mailchimp-list"></a>MailChimp リストにメンバーを追加する

次は、承認済みのメンバーをメーリング リストに追加するアクションを追加します。

1. 条件の **[真]** 分岐で、 **[アクションの追加]** を選択します。

1. **[操作を選択してください]** の検索ボックスで、 **[すべて]** を選択します。 検索ボックスに「`mailchimp`」と入力し、 **[メンバーをリストに追加する]** という名前のアクションを選択します。

   ![[操作を選択してください] ボックスに検索語句「mailchimp」が入力され、[メンバーをリストに追加する] アクションが選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. MailChimp アカウントにまだ接続していない場合には、サインインするように求められます。

1. 次の画像と説明に従い、 **[メンバーをリストに追加する]** アクションに情報を指定します。

   ![[メンバーをリストに追加する] アクションの情報を示すスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **リスト ID** | はい | <*メーリングリスト名*> | ご自分の MailChimp メーリング リストの名前を選択します。 この例では、`test-members-ML` を使用します。 |
   | **メール アドレス** | はい | <*new-member-email-address*> | 表示される動的コンテンツ リストの **[新しいメールが届いたとき]** セクションで **[差出人]** を選択します。これはこのトリガーの出力であり、新しいメンバーのメール アドレスを指定します。 |
   | **状態** | はい | <*メンバーの登録状態*> | 新しいメンバーに対して設定する登録状態を選択します。 この例では `subscribed` を選択します。 <p>詳細については、「[Manage subscribers with the MailChimp API (MailChimp API を使った購読者管理)](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/)」を参照してください。 |
   |||||

   **[メンバーをリストに追加する]** アクションのプロパティの詳細については、[MailChimp のコネクタ リファレンス](/connectors/mailchimp/)を参照してください。

1. ロジック アプリを保存します。

次に、新しいメンバーがメーリング リストに正常に登録されたかどうかをチェックするための条件を追加します。 そうすることによって、ロジック アプリからこの操作の成否に関する通知を受け取ることができます。

## <a name="check-for-success-or-failure"></a>成功か失敗かをチェックする

1. **[True]** 分岐の **[メンバーをリストに追加する]** アクションの下にある **[アクションの追加]** を選択します。

1. **[操作を選択してください]** で **[組み込み]** を選択します。 検索ボックスに「`condition`」と入力し、「 **[条件]** 」という名前のアクションを選択します。

1. 条件の名前をわかりやすく「`If add member succeeded`」に変更します。

1. 承認済みのメンバーが正常にメーリング リストに登録されているかどうかをチェックする条件を作成します。

   1. 条件の左側にある **[値の選択]** ボックス内をクリックします。 表示される動的コンテンツ リストの **[メンバーをリストに追加する]** セクションで、 **[状態]** プロパティを選択します。

      たとえば、条件はこの例のようになります。

      ![条件の左側の [値の選択] ボックスに [状態] を入力する手順を示したスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. 中央の比較ボックスで、 **[次の値に等しい]** 演算子を選択します。

   1. 条件の右側にある **[値の選択]** ボックスには、テキスト「`subscribed`」を入力します。

      完成した条件は次の例のようになります。

      ![登録の成否を確認する条件の完成版を示すスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

次に、承認されたメンバーのメーリング リストへの登録に成功または失敗したときに送信されるメールを設定します。

## <a name="send-email-if-member-added"></a>メンバーが追加された場合にメールを送信する

1. **[If add member succeeded]** 条件の下にある **[True]** 分岐で、 **[アクションの追加]** を選択します。

   ![[If add member succeeded] 条件の下にある [True] 分岐で [アクションの追加] を選択した状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. **[操作を選択してください]** の検索ボックスに「`outlook send email`」と入力し、 **[メールの送信]** というアクションを選択します。

   ![[操作を選択してください] の検索ボックスに「outlook send email」と入力され、通知用に [メールの送信] アクションが選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. アクションの名前をわかりやすく「`Send email on success`」に変更します。

1. 次の画面と説明に従い、 **[Send email on success]** アクションに情報を指定します。

   ![[Send email on success] アクションと、成功時のメールに指定する情報を示したスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **本文** | はい | <*成功時のメール本文*> | 成功時に送信されるメールの本文の内容。 このチュートリアルでは、次の手順に従います。 <p>1.「`New member has joined "test-members-ML":`」というテキストを入力します。末尾にはスペースを 1 つ追加してください。 <p>2.表示される動的コンテンツ リストから、 **[メール アドレス]** プロパティを選択します。 <p>**注**:このプロパティが表示されない場合には、 **[メンバーをリストに追加する]** セクション ヘッダーの隣にある **[表示を増やす]** を選択します。 <p>3.次の行には、「`Member opt-in status: `」というテキストを入力します。末尾にはスペースを 1 つ追加してください。 <p>4.動的コンテンツ リストから、 **[メンバーをリストに追加する]** の **[状態]** プロパティを選択します。 |
   | **件名** | はい | <*成功時のメールの件名*> | 成功時に送信されるメールの件名。 このチュートリアルでは、次の手順に従います。 <p>1.「`Success! Member added to "test-members-ML": `」というテキストを入力します。末尾にはスペースを 1 つ追加してください。 <p>2.動的コンテンツ リストから、 **[メンバーをリストに追加する]** の **[メール アドレス]** プロパティを選択します。 |
   | **To** | はい | <*your-email-address*> | 成功時のメールの送信先アドレス。 テスト目的で自分の電子メール アドレスを使用できます。 |
   |||||

1. ロジック アプリを保存します。

## <a name="send-email-if-member-not-added"></a>メンバーが追加されなかった場合にメールを送信する

1. **[If add member succeeded]** 条件の下にある **[False]** 分岐で、 **[アクションの追加]** を選択します。

   ![[If add member succeeded] 条件の [False] 分岐で [アクションの追加] が選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. **[操作を選択してください]** の検索ボックスに「`outlook send email`」と入力し、 **[メールの送信]** というアクションを選択します。

   ![[操作を選択してください] の検索ボックスに「outlook send email」と入力され、[メールの送信] アクションが選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. アクションの名前をわかりやすく「`Send email on failure`」に変更します。

1. 次に示す説明に従って、このアクションの情報を指定します。

   ![[Send email on failure] アクションと、失敗時のメールに指定する情報を示したスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **本文** | はい | <*body-for-failure-email*> | 失敗時に送信されるメールの本文の内容。 このチュートリアルでは、次のテキストを入力します。 <p>`Member might already exist. Check your MailChimp account.` |
   | **件名** | はい | <*subject-for-failure-email*> | 失敗時に送信されるメールの件名。 このチュートリアルでは、次の手順に従います。 <p>1.「`Failed, member not added to "test-members-ML": `」というテキストを入力します。末尾にはスペースを 1 つ追加してください。 <p>2.動的コンテンツ リストから、 **[メンバーをリストに追加する]** の **[メール アドレス]** プロパティを選択します。 |
   | **To** | はい | <*your-email-address*> | 失敗時のメールの送信先アドレス。 テスト目的で自分の電子メール アドレスを使用できます。 |
   |||||

1. ロジック アプリを保存します。 

次に、ロジック アプリをテストします。この時点で、ロジック アプリは次のようになっています。

![完成したロジック アプリ ワークフローの例を示したスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>ロジック アプリを実行する

1. メーリング リストの登録申請メールを自分宛てに送信します。 申請が受信トレイに配信されるまで待ちます。

1. ロジック アプリを手動で開始するには、デザイナーのツール バーの **[実行]** を選択します。 

   メールの件名がトリガーの件名フィルターと一致した場合、登録申請の承認依頼メールがロジック アプリから届きます。

1. 受信した承認メールで **[Approve]** を選択します。

1. 購読者のメール アドレスがメーリング リストに存在しない場合、その人物のメール アドレスが追加され、次のようなメールが届きます。

   ![登録に成功した場合のメールの例を示したスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   購読者を追加できなかった場合は、次のようなメールが届きます。

   ![登録に失敗した場合のメールの例を示したスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > メールが届かない場合は、迷惑メール フォルダーを確認してください。 この種のメールは、迷惑メール フィルターによってリダイレクトされる場合があります。 また、ロジック アプリが正しく動作しているかどうかわからない場合は、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

お疲れさまでした。Azure、Microsoft サービスなどの SaaS アプリの垣根を越えて情報を統合するロジック アプリを作成し、実行することができました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したロジック アプリは、アプリを無効にするか削除するまで実行され続けます。 このサンプル ロジック アプリが不要になったら、ロジック アプリと関連リソースが含まれるリソース グループを削除しましょう。

1. Azure portal の検索ボックスに、作成したリソース グループの名前を入力します。 結果の **[リソース グループ]** で、そのリソース グループを選択します。

   この例では、`LA-MailingList-RG` という名前の新しいリソース グループを作成しました。

   ![Azure の検索ボックスに「la-mailinglist-rg」と入力され、**LA-MailingList-RG** が選択された状態のスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Azure のホーム ページの **[最近のリソース]** にリソース グループが表示されている場合は、ホーム ページからそのグループを選択できます。

1. リソース グループのメニューで、 **[概要]** が選択されていることを確認します。 **[概要]** ペインのツール バーで、 **[リソース グループの削除]** を選択します。

   ![リソース グループの [概要] ペインと、ペインのツール バーの [リソース グループの削除] が選択されているスクリーンショット。](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. 確認ペインが表示されたら、リソース グループ名を入力して、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、メーリング リスト登録申請の承認を処理するロジック アプリを作成しました。 次回は、Azure Storage と Azure Functions など各種 Azure サービスを統合することによって、メールの添付ファイルを処理して保存するロジック アプリを作成する方法について詳しく見ていきましょう。

> [!div class="nextstepaction"]
> [メールの添付ファイルの処理](../logic-apps/tutorial-process-email-attachments-workflow.md)
