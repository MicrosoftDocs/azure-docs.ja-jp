---
title: 仮想マシンの変更の監視 - Azure Event Grid と Logic Apps
description: Azure Event Grid と Logic Apps を使用して、仮想マシン (VM) の変更を確認します
services: logic-apps, event-grid
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: estfan, LADocs
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 1a5d8c36382433024efd1f1cc6ba9fd878d28ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92329527"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>チュートリアル:Azure Event Grid と Logic Apps を使用して仮想マシンの変更を監視する

Azure やサードパーティのリソースに生じた特定のイベントを監視してそれに対処するタスクは、最小限のコードを使った[ロジック アプリ](../logic-apps/logic-apps-overview.md)を作成することにより、ワークフローとして自動化して実行できます。 そうしたリソースのイベントは、[Azure Event Grid](../event-grid/overview.md) に発行できます。 また、そのイベントは、イベント グリッドによって、エンドポイントとしてキュー、webhook、または [Event Hubs](../event-hubs/event-hubs-about.md) を持つサブスクライバーにプッシュされます。 ロジック アプリはサブスクライバーとして、イベント グリッドからのイベントを待機してから、自動ワークフローを実行してタスクを実行できます。

たとえば、Azure Event Grid サービスを介して発行元がサブスクライバーに送信できるイベントをいくつか次に示します。

* リソースの作成、読み取り、更新、または削除。 たとえば、Azure サブスクリプションで料金を発生させ、課金内容に影響する可能性がある変更を監視できます。

* Azure サブスクリプションのユーザーの追加または削除。

* アプリによる特定のアクションの実行。

* キューでの新着メッセージ。

このチュートリアルでは、仮想マシンの変更を監視し、それらの変更に関する電子メールを送信するロジック アプリを作成します。 Azure リソースのイベント サブスクリプションでロジック アプリを作成する場合、イベントはそのリソースから、イベント グリッドを介してロジック アプリに渡されます。 このチュートリアルでは、このロジック アプリを構築する方法を説明します。

![Event Grid で VM を監視するためのワークフローが表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * イベント グリッドからイベントを監視するロジック アプリを作成する。
> * 仮想マシンの変更を主に確認する条件を追加する。
> * 仮想マシンの変更時に電子メールを送信する。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 通知の送信先となるメール アカウント。Logic Apps がサポートするメール プロバイダーは、Office 365 Outlook、Outlook.com、Gmail などです。 その他のプロバイダーについては、[こちらのコネクタ一覧を参照](/connectors/)してください。

  このチュートリアルでは、Office 365 Outlook のアカウントを使用します。 別のメール アカウントを使う場合、おおよその手順は変わりませんが、UI の表示がやや異なることがあります。

  > [!IMPORTANT]
  > Gmail コネクタの使用を希望する場合、ロジック アプリで制限なしにこのコネクタを使用できるのは、G-Suite ビジネス アカウントだけです。 Gmail コンシューマー アカウントを持っている場合は、Google によって承認された特定のサービスのみでこのコネクタを使用できるほか、[認証に使用する Google クライアント アプリを Gmail コネクタで作成する](/connectors/gmail/#authentication-and-bring-your-own-application)ことができます。 詳細については、「[Azure Logic Apps での Google コネクタのデータ セキュリティとプライバシー ポリシー](../connectors/connectors-google-data-security-privacy-policy.md)」を参照してください。

* 単体で自身の Azure リソース グループに属する[仮想マシン](https://azure.microsoft.com/services/virtual-machines)。 仮想マシンをまだ作成していない場合は、[VM を作成するチュートリアル](../virtual-machines/windows/quick-create-portal.md)に従って作成してください。 仮想マシンでイベントを発行する場合は、[他の操作を行う必要はありません](../event-grid/overview.md)。

## <a name="create-blank-logic-app"></a>空のロジック アプリを作成する

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメイン メニューで、 **[リソースの作成]**  >  **[統合]**  >  **[ロジック アプリ]** の順に選択します。

   ![ロジック アプリ リソースを作成するためのボタンが表示されている、Azure portal のスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. **[ロジック アプリ]** で、ロジック アプリ リソースに関する情報を入力します。 完了したら **[作成]** を選択します。

   ![名前、サブスクリプション、リソース グループ、場所などの詳細が表示されている、ロジック アプリの [作成] メニューのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **名前** | はい | <*ロジック アプリ名*> | 一意の名前をロジック アプリに指定します。 |
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | このチュートリアルでは、すべてのサービスで同じ Azure サブスクリプションを選択します。 |
   | **リソース グループ** | はい | <*Azure-resource-group*> | ロジック アプリに使用する Azure リソース グループの名前。このチュートリアルのすべてのサービスで、このリソース グループ名を選択できます。 |
   | **場所** | はい | <*Azure-region*> | このチュートリアルでは、すべてのサービスで同じ リージョンを選択します。 |
   |||

1. ロジック アプリのデプロイ後、紹介ビデオやよく使用されるトリガーが含まれたページが Logic Apps デザイナーに表示されます。 ビデオとトリガーを超えてスクロールします。

1. **[テンプレート]** で **[空のロジック アプリ]** を選択します。

   ![空のロジック アプリを作成するための選択が示されている、Logic Apps テンプレートのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Logic Apps デザイナーには、ロジック アプリの起動に使用できる [*トリガー*](../logic-apps/logic-apps-overview.md#logic-app-concepts)が表示されています。 すべてのロジック アプリは必ずトリガーから起動され、トリガーは、特定のイベントが発生するか特定の条件が満たされたときに起動されます。 トリガーが起動されるたびに、ロジック アプリを実行するワークフロー インスタンスが Azure Logic Apps によって作成されます。

## <a name="add-an-event-grid-trigger"></a>イベント グリッド トリガーを追加する

次に、イベント グリッド トリガーを追加します。イベント グリッド トリガーを使用して、自分の仮想マシンのリソース グループを監視します。

1. デザイナーの検索ボックスに、フィルターとして「`event grid`」と入力します。 トリガーの一覧から **[リソース イベントが発生したとき]** トリガーを選択します。

   ![リソース イベントでの Event Grid トリガーの選択が示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. メッセージが表示されたら、自分の Azure アカウントの資格情報を使用して Azure Event Grid にサインインします。 自分の Azure サブスクリプションに関連付けられている Azure Active Directory テナントが表示される **[テナント]** の一覧で、適切なテナントが表示されていることを確認します。その例を次に示します。

   ![Event Grid に接続するための Azure サインイン プロンプトが示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > @outlook.com または @hotmail.com などの個人用の Microsoft アカウントでサインインした場合、Event Grid トリガーが正しく表示されないことがあります。 この問題を回避するには、[[サービス プリンシパルを使用して接続する]](../active-directory/develop/howto-create-service-principal-portal.md) を選択するか、 *<ユーザー名>* @emailoutlook.onmicrosoft.com などの Azure サブスクリプションに関連する Azure Active Directory のメンバーとして認証します。

1. 次に、イベントを発行者からサブスクライブするようにロジック アプリを設定します。 以下の表で指定したように、イベント サブスクリプションの詳細を指定します。その例を次に示します。

   ![リソース イベントが発生したときのトリガーの詳細エディターが表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | プロパティ | 必須 | 値 | 説明 |
   | -------- | -------- | ----- | ----------- |
   | **サブスクリプション** | はい | <*イベント発行者の Azure サブスクリプション名*> | "*イベント発行者*" に関連付けられている Azure サブスクリプションの名前を選択します。 このチュートリアルでは、自分の仮想マシンの Azure サブスクリプション名を選択します。 |
   | **リソースの種類** | はい | <*イベント発行者の Azure リソースの種類*> | イベント発行者の Azure リソースの種類を選択します。 Azure リソースの種類の詳細については、「[Azure リソースプロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください このチュートリアルでは、Azure リソース グループを監視するために `Microsoft.Resources.ResourceGroups` 値を選択します。 |
   | **リソース名** |  はい | <*イベント発行者の Azure リソース名*> | イベント発行者の Azure リソース名を選択します。 この一覧は、選択したリソースの種類によって異なります。 このチュートリアルでは、自分の仮想マシンがある Azure リソース グループの名前を選択します。 |
   | **イベントの種類の項目** |  いいえ | <*イベントの種類*> | フィルター処理してイベント グリッドに送信する特定のイベントの種類を 1 つ以上選択します。 たとえば、リソースが変更または削除された場合にそれを検出するために、これらのイベントの種類を必要に応じて追加することができます。 <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>詳細については、以下のトピックを参照してください。 <p><p>- [Azure Event Grid のリソース グループ用のイベント スキーマ](../event-grid/event-schema-resource-groups.md) <br>- [イベントのフィルター処理について](../event-grid/event-filtering.md) <br>- [Event Grid のイベントのフィルター処理](../event-grid/how-to-filter-events.md) |
   | オプションのプロパティを追加するには、 **[新しいパラメーターの追加]** を選択して、目的のプロパティを選択します。 | いいえ | {説明を参照} | * **プレフィックス フィルター**:このチュートリアルでは、このプロパティは空のままにします。 既定の動作はすべての値と一致します。 ただし、フィルターとしてプレフィックス文字列を指定できます。たとえば、特定のリソースのパスとパラメーターを指定できます。 <p>* **サフィックス フィルター**:このチュートリアルでは、このプロパティは空のままにします。 既定の動作はすべての値と一致します。 ただし、フィルターとしてサフィックス文字列を指定できます。たとえば、特定の種類のファイルが必要なときは、ファイル名拡張子を指定できます。 <p>* **サブスクリプション名**:このチュートリアルでは、イベント サブスクリプションの一意の名前を指定します。 |
   |||

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。 ロジック アプリでアクションの詳細を折りたたんで非表示にするには、アクションのタイトル バーを選択します。

   ![ワークフローの編集を保存するための [保存] ボタンが表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   イベント グリッド トリガーを含むロジック アプリを保存すると、Azure は選択したリソースのロジック アプリのイベント サブスクリプションを自動的に作成します。 このため、リソースがイベントをイベント グリッドに発行したとき、イベント グリッドは、そのイベントをロジック アプリに自動的にプッシュします。 このイベントはロジック アプリをトリガーし、次の手順で定義するワークフローのインスタンスを作成して実行します。

ロジック アプリは現在アクティブで、イベント グリッドからイベントをリッスンしますが、アクションをワークフローに追加するまでは何も行いません。

## <a name="add-a-condition"></a>条件を追加する

特定のイベントまたは操作が発生したときにだけロジック アプリが実行されるようにするため、`Microsoft.Compute/virtualMachines/write` 操作の有無を調べる条件を追加します。 この条件が true の場合、ロジック アプリによって、更新された仮想マシンに関する詳細が電子メールで送信されます。

1. ロジック アプリ デザイナーのイベント グリッド トリガーで、 **[新しいステップ]** を選択します。

   ![新しいステップをワークフローに追加するためのボタンが表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「`condition`」と入力します。 アクションの一覧で、 **[条件]** アクションを選択します。

   ![条件アクションを追加するためのボタンが表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   ロジック アプリ デザイナーにより、空の条件 (true か false かに応じて従うアクション パスなど) がワークフローに追加されます。

   ![ワークフローに追加された空が表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. 条件のタイトルの名前を `If a virtual machine in your resource group has changed` に変更します。 条件のタイトル バーにある省略記号 ( **...** ) ボタンを選択し、 **[名前の変更]** を選択します。

   ![[名前の変更] オプションが選択された条件エディターのコンテキスト メニューが表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. `operationName` プロパティが `Microsoft.Compute/virtualMachines/write` 操作と等しい `data` オブジェクトのイベントの `body` をチェックする条件を作成します。 詳細については、「[Event Grid event schema (Event Grid イベント スキーマ)](../event-grid/event-schema.md)」を参照してください。

   1. 最初の行の **[And]** の下にある左側のボックス内をクリックします。 表示された動的なコンテンツの一覧から **[式]** を選択します。

      ![式エディターが選択された条件が表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. 式エディターに次の式 (トリガーから操作の名前を取得する) を入力し、 **[OK]** を選択します。

      `triggerBody()?['data']['operationName']`

      次に例を示します。

      ![条件エディターと操作名を抽出する式が表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. 中央のボックスでは、演算子を **[is equal to]** のままにしておきます。

   1. 右側のボックスに、次の値 (監視対象となる具体的な操作) を入力します。

      `Microsoft.Compute/virtualMachines/write`

   完成した条件は、この例のようになります。

   ![操作を比較する条件が表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   デザイン ビューからコード ビューに切り替えてからデザイン ビューに戻ると、条件で指定した式が **data.operationName** トークンに解決されます。

   ![条件と解決済みのトークンが表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. ロジック アプリを保存します。

## <a name="send-email-notifications"></a>メール通知を送信する

次は、指定した条件が true の場合に電子メールを受信できるよう "[*アクション*](../logic-apps/logic-apps-overview.md#logic-app-concepts)" を追加します。

1. 条件の **[true の場合]** ボックスで、 **[アクションの追加]** を選択します。

   ![条件が true の場合にアクションを追加するボタンが表示されている、Logic Apps デザイナーの条件エディターのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「`send an email`」と入力します。 電子メール プロバイダーに基づいて、一致するコネクタを検索して選択します。 次に、コネクタに対して "電子メールの送信" アクションを選択します。 次に例を示します。

   * たとえば、Azure 職場または学校アカウントの場合は、Office 365 Outlook コネクタを選択します。

   * 個人用 Microsoft アカウントの場合は、Outlook.com コネクタを選択します。

   * Gmail アカウントの場合は、Gmail コネクタを選択します。

   このチュートリアルでは Office 365 Outlook コネクタを使用して続行します。 別のプロバイダーを使用する場合、手順は変わりませんが、UI の表示が若干異なることがあります。

   ![Office 365 Outlook コネクタの [電子メールの送信] アクションの検索が表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. 電子メール プロバイダーに接続していない場合は、認証を求められた時点で、電子メール アカウントにサインインします。

1. "電子メールの送信" アクションを `Send email when virtual machine updated` というタイトルに変更します。

1. 以下の表に記載されているとおりにメールの情報を指定します。

   ![true 条件の電子メールの件名に動的コンテンツが追加されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > 前のワークフロー ステップからの出力を選択するには、編集ボックス内をクリックして動的なコンテンツの一覧を表示するか、 **[動的なコンテンツの追加]** を選択します。 さらに結果を表示するには、一覧の各セクションの **[もっと見る]** を選択します。 動的なコンテンツの一覧を閉じるには、 **[動的なコンテンツの追加]** を再度選択します。

   | プロパティ | 必須 | 値 | 説明 |
   | -------- | -------- | ----- | ----------- |
   | **To** | はい | <*受信者\@ドメイン*> | 受信者の電子メール アドレスを入力します。 テスト目的で自分の電子メール アドレスを使用できます。 |
   | **件名** | はい | `Resource updated:` **件名** | 電子メールの件名の内容を入力します。 このチュートリアルでは、指定されたテキストを入力し、イベントの **[件名]** フィールドを選択します。 ここでは、電子メールの件名には更新リソース (仮想マシン) の名前が含まれています。 |
   | **本文** | はい | `Resource:` **トピック** <p>`Event type:` **イベントの種類**<p>`Event ID:` **ID**<p>`Time:` **イベント時間** | 電子メールの本文の内容を入力します。 このチュートリアルでは、指定されたテキストを入力し、イベントの **[トピック]** 、 **[イベントの種類]** 、 **[ID]** 、 **[イベント時間]** の各フィールドを選択して、その更新に関してイベントを発生させたリソース、イベントの種類、イベント タイムスタンプ、イベント ID が電子メールに含まれるようにします。 このチュートリアルの場合、リソースはトリガーで選択された Azure リソース グループです。 <p>コンテンツに空白行を追加するには、Shift + Enter キーを押します。 |
   ||||

   > [!NOTE]
   > 配列を表すフィールドを選択すると、その配列を参照するアクションに **[For each]** ループが自動的に追加されます。 このように、ロジック アプリは、そのアクションを各配列項目に対して実行します。

   電子メール アクションは次の例のようになる場合があります。

   ![VM が更新されたときに電子メールで送信するために選択された出力が表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   完了すると、ロジック アプリは次の例のようになります。

   ![作成されたロジック アプリとトリガーとアクションの詳細が表示されている、Logic Apps デザイナーのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. ロジック アプリを保存します。 ロジック アプリで各アクションの詳細を折りたたんで非表示にするには、アクションのタイトル バーを選択します。

   ロジック アプリはアクティブですが、仮想マシンが変更されるまで何も行いません。 ロジック アプリをテストするには、次のセクションに進みます。

## <a name="test-your-logic-app-workflow"></a>ロジック アプリ ワークフローをテストする

1. 指定したイベントをロジック アプリが取得していることを確認するには、仮想マシンを更新します。

   たとえば、Azure Portal で仮想マシンのサイズを変更するか、[Azure PowerShell で VM のサイズを変更](../virtual-machines/windows/resize-vm.md)できます。

   しばらくすると、電子メールが届きます。 次に例を示します。

   ![VM の更新に関する詳細が表示されている、Outlook メールの例のスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. ロジック アプリの実行とトリガーの履歴を確認するには、ロジック アプリのメニューで **[概要]** を選択します。 実行の詳細を表示するには、その実行の行を選択します。

   ![成功した実行が選択されている、ロジック アプリの [概要] ページのスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. 各ステップの入力と出力を表示するには、確認するステップを展開します。 この情報は、ロジック アプリの問題を診断してデバッグするのに役立ちます。

   ![各実行の詳細が表示されている、ロジック アプリの実行履歴のスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

これで、イベント グリッドでリソース イベントを監視し、そのイベントが発生したときに電子メールを送信するロジック アプリが作成および実行されました。 また、プロセスを自動化するワークフローを簡単に作成し、システムとクラウド サービスを統合できる方法を学習しました。

イベント グリッドとロジック アプリを使用して、次のような他の構成の変更を監視できます。

* 仮想マシンによる Azure のロールベースのアクセス制御 (Azure RBAC) 権限の取得。
* ネットワーク インターフェイス (NIC) のネットワーク セキュリティ グループ (NSG) への変更。
* 仮想マシンのディスクの追加または削除。
* 仮想マシン NIC へのパブリック IP アドレスの割り当て。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルではリソースを使用して、Azure サブスクリプションで料金を発生させるアクションを実行します。 このチュートリアルとテストが完了したら、料金を発生させたくないリソースは必ず無効にするか、削除してください。

* 作業内容を削除することなくロジック アプリの実行を停止するには、アプリを無効にします。 ロジック アプリのメニューで、 **[概要]** を選択します。 ツールバーで、 **[Disable]\(無効化\)** を選択します。

  ![ロジック アプリを無効にするために [無効] ボタンが選択されている、ロジック アプリの [概要] のスクリーンショット。](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > ロジック アプリのメニューが表示されない場合は、Azure ダッシュボードに戻り、ロジック アプリを開き直してください。

* ロジック アプリを完全に削除するには、ロジック アプリのメニューで **[概要]** を選択します。 ツールバーの **[削除]** を選択します。 ロジック アプリを削除することに同意し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

* [Event Grid を使ったカスタム イベントの作成とルーティング](../event-grid/custom-event-quickstart.md)
