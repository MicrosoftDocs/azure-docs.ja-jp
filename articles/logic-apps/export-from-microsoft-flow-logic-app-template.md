---
title: Power Automate からフローを Azure Logic Apps にエクスポートする
description: Azure Resource Manager テンプレートとしてエクスポートすることによって Power Automate から Azure Logic Apps にフローを移行する
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, sneshaf, pinath, logicappspm
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: f2b4e09ec9b50bb6993c89d90b0f33c0c905cbf0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699098"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Power Automate からフローをエクスポートして Azure Logic Apps にデプロイする

フローの機能を拡張するために、そのフローを [Power Automate](https://flow.microsoft.com) から [Azure Logic Apps](../logic-apps/logic-apps-overview.md) に移行することができます。 フローをロジック アプリの Azure Resource Manager テンプレートとしてエクスポートし、そのロジック アプリ テンプレートを Azure リソース グループにデプロイした後、Logic App Designer でそのロジック アプリを開くことができます。

> [!IMPORTANT]
> 2020 年 8 月以降に作成された Power Automate フローについては、Logic Apps へのエクスポートは利用できません。 2020 年 10 月に、Power Automate では [OpenAPI 2.0 プロトコル](https://swagger.io/specification/v2/)での新しいフロー作成が標準化されました。 このプロトコルに基づく新しいフローは Logic Apps ワークフローと互換性がないため、これらのフローを Logic Apps にエクスポートすることはできなくなりました。 代わりに、これらのフローに対応した[ロジック アプリ](quickstart-create-first-logic-app-workflow.md)を手動で作成する必要があります。

すべての Power Automate コネクタが Azure Logic Apps で使用できるわけではありません。 Azure Logic Apps に同等のコネクタを持つ Power Automate フローのみを移行できます。 たとえば、Button トリガー、Approval コネクタ、および Notification コネクタは Power Automate に固有です。 

* Logic Apps に同等のコネクタを持たない Power Automate コネクタを見つけるには、[Power Automate コネクタ](/connectors/connector-reference/connector-reference-powerautomate-connectors)に関する記事を参照してください。

* Power Automate に同等のコネクタを持たない Logic Apps コネクタを見つけるには、[Logic Apps コネクタ](/connectors/connector-reference/connector-reference-logicapps-connectors)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Power Automate からエクスポートするフロー

## <a name="export-your-flow"></a>フローをエクスポートする

1. [Power Automate](https://flow.microsoft.com) にサインインし、 **[マイ フロー]** を選択します。 フローを探して選択します。 ツール バーの省略記号ボタン ( **...** ) を選択し、 **[エクスポート]**  >  **[Logic Apps テンプレート (.json)]** の順に選択します。

   ![Power Automate からフローをエクスポートする](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. テンプレートの .json ファイルを目的の場所に保存します。

詳細については、[Azure Logic Apps への成長](https://flow.microsoft.com/blog/grow-up-to-logic-apps/)に関する記事を参照してください。

## <a name="deploy-template-by-using-the-azure-portal"></a>Azure portal を使用してテンプレートをデプロイする

1. Azure アカウントで [Azure portal](https://portal.azure.com) にサインインします。

1. Azure ホーム ページで、検索ボックスに「`custom template`」と入力します。 結果から、 **[カスタム テンプレートのデプロイ]**  >  **[作成]** の順に選択します。

   ![[テンプレートのデプロイ] を見つけて選択する](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. **[カスタム デプロイ]** で、 **[Build your own template in the editor]\(エディターで独自のテンプレートをビルド\)** を選択します。

   !["Build your own template in the editor" (エディターで独自のテンプレートをビルド) を選択](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. **[テンプレートの編集]** ツール バーで、 **[ファイルの読み込み]** を選択します。

   !["ファイルの読み込み" を選択](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Power Automate からエクスポートした JSON テンプレート ファイルを保存した場所を参照します。 テンプレート ファイル > **[開く]** の順に選択します。

1. エディターにテンプレートの JSON、パラメーター、およびリソースが表示されたら、 **[保存]** を選択します。

   ![テンプレートを保存する](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. 次に、ロジック アプリの詳細情報を指定します。

   1. テンプレートの入力パラメーター値を選択または指定します。

      | プロパティ | 説明 |
      |----------|-------------|
      | **サブスクリプション** | 課金に使用する Azure サブスクリプション |
      | **リソース グループ** | ロジック アプリに使用する Azure リソース グループ。 既存のグループを使用するか、または新しいグループを作成できます。 |
      | **場所** | 新しいリソース グループを作成する場合に使用する Azure リージョン |
      | **ロジック アプリ名** | ロジック アプリ リソースに使用する名前 |
      | **ロジック アプリの場所** | ロジック アプリ リソースを作成する Azure リージョン (Azure リソース グループとは異なる場合) |
      | <*connection-name*> | ロジック アプリで再利用できる、以前に作成されたすべての接続の 1 つまたは複数の名前 <p><p>**注**:このロジック アプリが最初のものである場合は、すべての接続が新しく作成されるため、既定の名前をそのまま使用できます。 それ以外の場合は、以前に作成した接続の名前を指定できます。これは、複数のロジック アプリで使用できます。 |
      |||

      次に例を示します。

      ![テンプレートの入力パラメーターを指定する](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. 完了したら、必要な Azure リソースの作成とそれに応じた Azure サブスクリプションへの課金に関する **使用条件** を確認します。

   1. 準備ができたら、 **[上記の使用条件に同意する]**  >  **[購入]** の順に選択します。

      Azure は、指定されたリソース グループに、テンプレートをロジック アプリとしてデプロイします。

1. Power Automate から移行するすべてのロジック アプリは、無効の状態でデプロイされます。 ロジック アプリを有効にする前に、次の手順に従ってすべての新しい接続を承認します。

   1. Azure portal で、作成したロジック アプリを開きます。 ロジック アプリのメニューで **[ロジック アプリ デザイナー]** を選択します。

      承認が必要な接続ごとに、警告アイコンが表示されます。

      ![警告アイコン](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. 承認された接続が必要な手順ごとに、その手順を展開して **[Add new]\(新規追加\)** を選択します。

      ![[新規追加] ボタンが選択された Outlook の [接続] ウィンドウを示すスクリーンショット。](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. 各サービスにサインインするか、接続を承認するために必要な資格情報を指定します。

   1. 接続を更新したら、デザイナーのツール バーの **[保存]** を選択します。

1. ロジック アプリをアクティブ化する準備ができたら、ロジック アプリのメニューで **[概要]** を選択し、 **[Enable]\(有効化\)** を選択します。

   ![ロジック アプリを有効にする](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. 重複したワークフローを実行しないようにするために、必ず、元のフローを非アクティブ化または削除してください。

## <a name="deploy-template-by-using-visual-studio"></a>Visual Studio を使用してテンプレートをデプロイする

ロジック アプリを作成するための[前提条件](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)を使用して Visual Studio を設定した場合は、Visual Studio を使用して、エクスポートされたテンプレートを Azure Logic Apps にデプロイできます。

1. Visual Studio で、Power Automate からエクスポートしたロジック アプリ テンプレートの .json ファイルを参照して開きます。

1. Visual Studio で、 **[ロジック アプリ]** テンプレートを使用する **[Azure リソース グループ]** プロジェクトを作成します。手順については、「[クイック スタート: Azure Logic Apps を使用して自動化されたタスク、プロセス、およびワークフローを作成する - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)」に従ってください。

   この例では、"ImportedLogicApp" という名前の Visual Studio ソリューションを作成します。

   ![Azure リソース グループ プロジェクトを作成する](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. ソリューションが作成されたら、ソリューション エクスプローラーで **LogicApp.json** ファイルを開きます (このファイルがまだ開いていない場合)。

1. エクスポートされたテンプレートから内容をコピーし、**LogicApp.json** ファイルの内容を上書きします。

1. ロジック アプリをデプロイする前に、次の手順に従って新しい接続を承認します。

   1. **LogicApp.json** のショートカット メニューを開き、 **[Open With Logic App Designer]\(Logic App Designer で開く\)** を選択します。

      ![Logic App Designer でテンプレートを開く](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. メッセージが表示されたら、ロジック アプリのデプロイに使用する Azure サブスクリプションとリソース グループを選択します。

      ![Azure サブスクリプションとリソース グループを選択する](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      ロジック アプリがデザイナーに表示された後、承認を必要とするすべての接続に警告アイコンが表示されます。

      ![警告アイコンがある接続](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. 承認された接続が必要な手順ごとに、その手順を展開して **[Add new]\(新規追加\)** を選択します。

      ![新しい接続を追加する](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. 各サービスにサインインするか、接続を承認するために必要な資格情報を指定します。

   1. ロジック アプリをデプロイする前に、ソリューションを保存します。

1. ソリューション エクスプローラーで、プロジェクトのショートカット メニューを開き、 **[デプロイ]**  >  **[新規作成]** の順に選択します。 メッセージに従って Azure アカウントでサインインします。

1. メッセージが表示されたら、Azure サブスクリプション、Azure リソース グループ、および、デプロイに使用するその他の設定 (テンプレートのパラメーター値を渡すために使用する [パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)など) を確認して、 **[デプロイ]** を選択します。

   ![デプロイの設定を確認する](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. **[パラメーターの編集]** ボックスが表示された場合は、Azure 内のロジック アプリ リソースの名前を指定して **[保存]** を選択します。  

   ![デプロイのパラメーターを編集する](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   デプロイが開始されると、Visual Studio の **[出力]** ウィンドウにアプリのデプロイ状態が表示されます。 状態が表示されない場合、 **[Show output from]\(出力元の表示\)** の一覧を開いて、Azure リソース グループを選択します。 次に例を示します。

   ![[出力] ウィンドウ](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   ロジック アプリでの接続にユーザーからの入力が必要な場合は、バックグラウンドで PowerShell ウィンドウが開き、必要なパスワードまたはシークレット キーの入力を求められます。 その情報を入力すると、デプロイが続行されます。

   ![接続の認証](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   デプロイが完了すると、Azure portal でロジック アプリが公開されますが、アクティブ化はされません。

1. Azure portal でロジック アプリをアクティブ化する準備ができたら、Logic App Designer でロジック アプリを探して開きます。 ロジック アプリのメニューで **[概要]** を選択し、 **[Enable]\(有効化\)** を選択します。

1. 重複したワークフローを実行しないようにするために、必ず、元のフローを非アクティブ化または削除してください。

これらのデプロイ手順の詳細については、次を参照してください: [クイック スタート:Azure Logic Apps を使用して自動化されたタスク、プロセス、およびワークフローを作成する - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps のコネクタ](../connectors/apis-list.md)の詳細について学習します。
* [Azure Logic Apps](../logic-apps/logic-apps-overview.md) について学習します
