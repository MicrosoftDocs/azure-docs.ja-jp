---
title: 仮想マシンの変更の監視 - Azure Event Grid と Logic Apps | Microsoft Doc
description: Azure Event Grid と Logic Apps を使用して、仮想マシン (VM) の構成の変更を確認します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 01/12/2019
ms.openlocfilehash: e735c9773971a4c594c32e9ae29eeb295c32810c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473709"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>チュートリアル: Azure Event Grid と Logic Apps で仮想マシンの変更を監視する

特定のイベントが Azure リソースまたはサード パーティのリソースで発生したときに、自動[ロジック アプリ ワークフロー](../logic-apps/logic-apps-overview.md)を開始できます。 こうしたリソースは、そのイベントを [Azure Event Grid](../event-grid/overview.md) に発行できます。 また、そのイベントは、イベント グリッドによって、エンドポイントとしてキュー、webhook、または [Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) を持つサブスクライバーにプッシュされます。 ロジック アプリはサブスクライバーとして、イベント グリッドからのイベントを待機してから、自動ワークフローを実行してタスクを実行できます。コードを記述する必要はありません。

たとえば、Azure Event Grid サービスを介して発行元がサブスクライバーに送信できるイベントをいくつか次に示します。

* リソースの作成、読み取り、更新、または削除。 たとえば、Azure サブスクリプションで料金を発生させ、課金内容に影響する可能性がある変更を監視できます。 
* Azure サブスクリプションのユーザーの追加または削除。
* アプリによる特定のアクションの実行。
* キューでの新着メッセージ。

このチュートリアルでは、仮想マシンの変更を監視し、それらの変更に関する電子メールを送信するロジック アプリを作成します。 Azure リソースのイベント サブスクリプションでロジック アプリを作成する場合、イベントはそのリソースから、イベント グリッドを介してロジック アプリに渡されます。 このチュートリアルでは、このロジック アプリを構築する方法を説明します。

![概要 - イベント グリッドとロジック アプリによる仮想マシンの監視](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * イベント グリッドからイベントを監視するロジック アプリを作成する。
> * 仮想マシンの変更を主に確認する条件を追加する。
> * 仮想マシンの変更時に電子メールを送信する。

## <a name="prerequisites"></a>前提条件

* [Azure Logic Apps がサポートする電子メール プロバイダー](../connectors/apis-list.md) (Office 365 Outlook、Outlook.com、Gmail など) からの通知の送信先となる電子メール アカウント。 このチュートリアルでは、Office 365 Outlook を使用します。

* [仮想マシン](https://azure.microsoft.com/services/virtual-machines)。 仮想マシンを作成していない場合は、[VM を作成するチュートリアル](https://docs.microsoft.com/azure/virtual-machines/)に記載された手順に従い作成してください。 仮想マシンでイベントを発行する場合は、[他の操作を行う必要はありません](../event-grid/overview.md)。

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>イベント グリッドからイベントを監視するロジック アプリを作成する

最初にロジック アプリを作成し、仮想マシンのリソース グループを監視する Event Grid トリガーを追加します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 

2. Azure のメイン メニューの左上隅から、**[リソースの作成]** > **[エンタープライズ統合]** > **[ロジック アプリ]** を選択します。

   ![ロジック アプリを作成する](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. 次の表で指定した設定で、ロジック アプリを作成します。

   ![ロジック アプリの詳細を指定する](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | 設定 | 推奨値 | Description | 
   | ------- | --------------- | ----------- | 
   | **Name** | *{ロジック アプリ名}* | 一意のロジック アプリ名を指定します。 | 
   | **サブスクリプション** | *{Azure サブスクリプション}* | このチュートリアルでは、すべてのサービスで同じ Azure サブスクリプションを選択します。 | 
   | **リソース グループ** | *{Azure リソース グループ}* | このチュートリアルでは、すべてのサービスで同じ Azure リソース グループを選択します。 | 
   | **場所** | *{Azure のリージョン}* | このチュートリアルでは、すべてのサービスで同じ リージョンを選択します。 | 
   | | | 

4. 準備ができたら、**[ダッシュボードにピン留めする]**、**[作成]** の順に選択します。

   これで、ロジック アプリの Azure リソースが作成されました。 
   Azure によってロジック アプリがデプロイされたら、すばやく開始できるように Logic Apps デザイナーによって一般的なパターンのテンプレートが表示されます。

   > [!NOTE] 
   > **[ダッシュボードにピン留めする]** を選択すると、ロジック アプリが Logic Apps デザイナーで自動的に開きます。 開かない場合は、手動でロジック アプリを見つけて開くことができます。

5. ここで、ロジック アプリ テンプレートを選択します。 **[テンプレート]** の **[空のロジック アプリ]** を選択すると、ロジック アプリを最初から作成できます。

   ![ロジック アプリ テンプレートを選択する](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Logic Apps デザイナーには現在、ロジック アプリの開始に使用できる[*コネクタ*](../connectors/apis-list.md)や[*トリガー*](../logic-apps/logic-apps-overview.md#logic-app-concepts)のほか、タスクを実行するためにトリガーの後に追加できるアクションも表示されます。 トリガーは、ロジック アプリ インスタンスを作成し、ロジック アプリ ワークフローを開始するイベントです。 
   ロジック アプリには、最初の項目としてトリガーが必要です。

6. 検索ボックスに、フィルターとして「event grid」と入力します。 トリガーとして、**[Azure Event Grid - On a resource event]\(Azure Event Grid - リソース イベントの発生時\)** を選択します

   ![トリガーとして、[Azure Event Grid - On a resource event]\(Azure Event Grid - リソース イベントの発生時\) を選択する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. メッセージが表示されたら、Azure の資格情報で Azure Event Grid にサインインします。

   ![Azure の資格情報でサインイン](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > @outlook.com または @hotmail.com などの個人用の Microsoft アカウントでサインインした場合、Event Grid トリガーが正しく表示されないことがあります。 この問題を回避するには、[[サービス プリンシパルを使用して接続する]](../active-directory/develop/howto-create-service-principal-portal.md) を選択するか、*ユーザー名*@emailoutlook.onmicrosoft.com などの Azure サブスクリプションに関連する Azure Active Directory のメンバーとして認証します。

8. 次に、ロジック アプリを発行元イベントに登録します。 次の表で指定したように、イベント サブスクリプションの詳細を指定します。

   ![イベント サブスクリプションの詳細を指定する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Setting | 推奨値 | 説明 | 
   | ------- | --------------- | ----------- | 
   | **サブスクリプション** | *{仮想マシンの Azure サブスクリプション}* | イベント発行元の Azure サブスクリプションを選択します。 このチュートリアルでは、ご利用の仮想マシンの Azure サブスクリプションを選択します。 | 
   | **リソースの種類** | Microsoft.Resources.resourceGroups | イベント発行元のリソースの種類を選択します。 このチュートリアルでは、指定した値を選択するため、ロジック アプリはリソース グループだけを監視します。 | 
   | **リソース名** | *{仮想マシンのリソース グループ名}* | 発行元のリソース名を選択します。 このチュートリアルでは、仮想マシンのリソース グループの名前を選択します。 | 
   | オプションの設定を指定する場合は、**[詳細オプションを表示する]** を選択します。 | *{説明を確認する}* | * **プレフィックス フィルター**:このチュートリアルでは、この設定は空のままにします。 既定の動作はすべての値と一致します。 ただし、フィルターとしてプレフィックス文字列を指定できます。たとえば、特定のリソースのパスとパラメーターを指定できます。 <p>* **サフィックス フィルター**:このチュートリアルでは、この設定は空のままにします。 既定の動作はすべての値と一致します。 ただし、フィルターとしてサフィックス文字列を指定できます。たとえば、特定の種類のファイルが必要なときは、ファイル名拡張子を指定できます。<p>* **サブスクリプション名**:イベント サブスクリプションに一意の名前を指定します。 |
   | | | 

   完了すると、イベント グリッド トリガーが次の例のようになることがあります。
   
   ![イベント グリッド トリガーの詳細の例](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. ロジック アプリを保存し、 デザイナーのツール バーで、**[保存]** を選択します。 ロジック アプリでアクションの詳細を折りたたんで非表示にするには、アクションのタイトル バーを選択します。

   ![ロジック アプリを保存する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   イベント グリッド トリガーを含むロジック アプリを保存すると、Azure は選択したリソースのロジック アプリのイベント サブスクリプションを自動的に作成します。 このため、リソースがイベントをイベント グリッドに発行したとき、イベント グリッドは、そのイベントをロジック アプリに自動的にプッシュします。 このイベントはロジック アプリをトリガーし、次の手順で定義するワークフローのインスタンスを作成して実行します。

ロジック アプリは現在アクティブで、イベント グリッドからイベントをリッスンしますが、アクションをワークフローに追加するまでは何も行いません。 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>仮想マシンの変更を確認する条件を追加する

特定のイベントが発生した場合にのみロジック アプリのワークフローが実行されるようにするには、仮想マシンの "書き込み" 操作を確認する条件を追加します。 この条件が true の場合、ロジック アプリによって、更新された仮想マシンに関する詳細が電子メールで送信されます。

1. ロジック アプリ デザイナーのイベント グリッド トリガーで、**[新しいステップ]** > **[条件の追加]** の順に選択します。

   ![ロジック アプリに条件を追加する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   ロジック アプリ デザイナーにより、空の条件 (true か false かに応じて従うアクション パスなど) がワークフローに追加されます。

   ![空の条件](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. **[条件]** ボックスで、**[詳細設定モードで編集]** を選択します。
次の式を入力します。

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   条件は次の例のようになります。

   ![空の条件](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   この式は、`body` イベントで、`operationName` プロパティが `Microsoft.Compute/virtualMachines/write` 操作の `data` オブジェクトをチェックします。 
   詳細については、「[Event Grid event schema (Event Grid イベント スキーマ)](../event-grid/event-schema.md)」を参照してください。

3. 条件の説明を入力するには、条件シェイプの**省略記号** (**...**) ボタンを選択し、**[名前の変更]** を選択します。

   > [!NOTE] 
   > このチュートリアルの後半で説明する例では、ロジック アプリ ワークフローの手順も説明します。

4. 次に示すように式で自動的に解決されるように、**[基本モードで編集]** を選択します。

   ![ロジックのアプリの条件](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. ロジック アプリを保存し、

## <a name="send-email-when-your-virtual-machine-changes"></a>仮想マシンの変更時に電子メールを送信する

次は、指定した条件が true の場合に電子メールを受信するという "[*アクション*](../logic-apps/logic-apps-overview.md#logic-app-concepts)" を追加します。

1. その条件の **[true の場合]** ボックスで、**[アクションの追加]** を選択します。

   ![条件が true の場合のアクションを追加する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. 検索ボックスに、フィルターとして「email」と入力します。 電子メール プロバイダーに基づいて、一致するコネクタを検索して選択します。 次に、コネクタに対して "電子メールの送信" アクションを選択します。 例:  

   * たとえば、Azure 職場または学校アカウントの場合は、Office 365 Outlook コネクタを選択します。 
   * 個人用 Microsoft アカウントの場合は、Outlook.com コネクタを選択します。 
   * Gmail アカウントの場合は、Gmail コネクタを選択します。 

   ここでは Office 365 Outlook コネクタを使用して続行します。 
   別のプロバイダーを使用する場合、手順は変わりませんが、UI の表示が異なることがあります。 

   !["電子メールの送信" アクション](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. 電子メール プロバイダーに接続していない場合は、認証を求められた時点で、電子メール アカウントにサインインします。

4. 次の表で指定しているとおりに、電子メールの詳細を指定します。

   ![空の電子メール アクション](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > ワークフローで使用できるフィールドから選択するには、編集ボックスで **[動的なコンテンツ]** をクリックして一覧を開くか、**[動的なコンテンツの追加]** を選択します。 他のフィールドについては、一覧の各セクションの **[もっと見る]** を選択します。 **[動的なコンテンツ]** 一覧を閉じるには、**[動的なコンテンツの追加]** を選択します。

   | Setting | 推奨値 | 説明 | 
   | ------- | --------------- | ----------- | 
   | **To** | *{受信者の電子メール アドレス}* |受信者の電子メール アドレスを入力します。 テスト目的で自分の電子メール アドレスを使用できます。 | 
   | **[件名]** | 更新リソース:**[件名]**| 電子メールの件名の内容を入力します。 このチュートリアルでは、推奨テキストを入力し、イベントの **[件名]** フィールドを選択します。 ここでは、電子メールの件名には更新リソース (仮想マシン) の名前が含まれています。 | 
   | **本文** | リソース グループ:**トピック** <p>イベントの種類:**イベントの種類**<p>イベント ID:**ID**<p>時間:**イベント時間** | 電子メールの本文の内容を入力します。 このチュートリアルでは、推奨テキストを入力し、その更新に関するリソース グループ名、イベントの種類、イベント タイムスタンプ、イベント ID が電子メールに含まれるように、**[トピック]**、**[イベントの種類]**、**[ID]**、**[イベント時間]** フィールドを選択します。 <p>コンテンツに空白行を追加するには、Shift + Enter キーを押します。 | 
   | | | 

   > [!NOTE] 
   > 配列を表すフィールドを選択すると、その配列を参照するアクションに **[For each]** ループが自動的に追加されます。 このように、ロジック アプリは、そのアクションを各配列項目に対して実行します。

   電子メール アクションは次の例のようになる場合があります。

   ![電子メールに含める出力を選択する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   完了すると、ロジック アプリは次の例のようになります。

   ![完成したロジック アプリ](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. ロジック アプリを保存し、 ロジック アプリで各アクションの詳細を折りたたんで非表示にするには、アクションのタイトル バーを選択します。

   ![ロジック アプリを保存する](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   ロジック アプリはアクティブですが、仮想マシンが変更されるまで何も行いません。 
   ロジック アプリをテストするには、次のセクションに進みます。

## <a name="test-your-logic-app-workflow"></a>ロジック アプリ ワークフローをテストする

1. 指定したイベントをロジック アプリが取得していることを確認するには、仮想マシンを更新します。 

   たとえば、Azure Portal で仮想マシンのサイズを変更するか、[Azure PowerShell で VM のサイズを変更](../virtual-machines/windows/resize-vm.md)できます。 

   しばらくすると、電子メールが届きます。 例: 

   ![仮想マシンの更新に関する電子メール](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. ロジック アプリの実行およびトリガー履歴を確認するには、ロジック アプリのメニューの **[概要]** を選択します。 実行の詳細を表示するには、その実行の行を選択します。

   ![ロジック アプリの実行の履歴](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. 各ステップの入力と出力を表示するには、確認するステップを展開します。 この情報は、ロジック アプリの問題を診断してデバッグするのに役立ちます。
 
   ![ロジック アプリの実行の履歴の詳細](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

これで、イベント グリッドでリソース イベントを監視し、そのイベントが発生したときに電子メールを送信するロジック アプリが作成および実行されました。 また、プロセスを自動化するワークフローを簡単に作成し、システムとクラウド サービスを統合できる方法を学習しました。

イベント グリッドとロジック アプリを使用して、次のような他の構成の変更を監視できます。

* 仮想マシンによるロールベースのアクセス制御 (RBAC) 権限の取得。
* ネットワーク インターフェイス (NIC) のネットワーク セキュリティ グループ (NSG) への変更。
* 仮想マシンのディスクの追加または削除。
* 仮想マシン NIC へのパブリック IP アドレスの割り当て。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルではリソースを使用して、Azure サブスクリプションで料金を発生させるアクションを実行します。 このチュートリアルとテストが完了したら、料金を発生させたくないリソースは必ず無効にするか、削除してください。

* 作業内容を削除することなくロジック アプリの実行を停止するには、アプリを無効にします。 ロジック アプリのメニューで、**[概要]** を選択します。 ツール バーで、**[無効にする]** を選択します。

  ![ロジック アプリをオフにする](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > ロジック アプリのメニューが表示されない場合は、Azure ダッシュボードに戻り、ロジック アプリを開き直してください。

* ロジック アプリを完全に削除するには、ロジック アプリのメニューから **[概要]** を選択します。 ツール バーで、**[削除]** を選択します。 ロジック アプリを削除することに同意し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

* [Event Grid を使ったカスタム イベントの作成とルーティング](../event-grid/custom-event-quickstart.md)
