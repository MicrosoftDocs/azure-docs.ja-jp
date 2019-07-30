---
title: Visual Studio を使用して自動化されたワークフローを作成する - Azure Logic Apps
description: Azure Logic Apps と Visual Studio を使用して、エンタープライズ統合のためのタスク、ビジネス プロセス、およびワークフローを自動化します
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: quickstart
ms.custom: mvc
ms.reviewer: klam, LADocs
ms.suite: integration
ms.date: 04/25/2019
ms.openlocfilehash: 51d1af24a22f7ed86b01b1c7c79b204cd552a7ea
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385425"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>クイック スタート:Azure Logic Apps を使用して自動化されたタスク、プロセス、およびワークフローを作成する - Visual Studio

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と Visual Studio を使用して、企業および組織の間でアプリ、データ、システム、サービスを統合するタスクとプロセスを自動化するワークフローを作成できます。 このクイック スタートでは、Visual Studio でロジック アプリを作成し、そのアプリを Azure にデプロイすることによってこれらのワークフローを設計および構築する方法を示します。 これらのタスクは Azure portal でも実行できます。しかし、Visual Studio を使用すれば、ロジック アプリをソース管理に追加したり、さまざまなバージョンを発行したり、異なるデプロイ環境用の Azure Resource Manager テンプレートを作成したりできます。

Azure Logic Apps が初めてであり、その基本的な概念だけを必要としている場合は、[Azure Portal でのロジック アプリの作成に関するクイック スタート](../logic-apps/quickstart-create-first-logic-app-workflow.md)をお試しください。 ロジック アプリ デザイナーは、Azure Portal と Visual Studio の両方で同じように動作します。

このクイック スタートでは、Visual Studio で Azure Portal クイック スタートと同じロジック アプリを作成します。 このロジック アプリは Web サイトの RSS フィードを監視し、そのフィード内の新しい項目ごとに電子メールを送信します。 完成したロジック アプリは、次の高レベルのワークフローのようになります。

![完成したロジック アプリ](./media/quickstart-create-logic-apps-with-visual-studio/overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* まだお持ちでない場合は、以下のツールをダウンロードしてインストールしてください。

  * [Visual Studio 2019、2017、または 2015 - Community Edition 以降](https://aka.ms/download-visual-studio)。 
  このクイック スタートでは、Visual Studio Community 2017 を使用します。

    > [!IMPORTANT]
    > Visual Studio 2019 または 2017 をインストールする場合は、 **[Azure の開発]** ワークロードを選択してください。

  * [Microsoft Azure SDK for .NET (2.9.1 以降)](https://azure.microsoft.com/downloads/)。 
  [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet) の詳細を参照してください。

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * 必要な Visual Studio バージョン用の Azure Logic Apps Tools:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Tools は、Visual Studio Marketplace から直接ダウンロードしてインストールできます。または、[この拡張機能を Visual Studio 内からインストールする方法](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)を確認できます。 
    インストールが完了したら、必ず Visual Studio を再起動してください。

* 組み込みのロジック アプリ デザイナーを使用する際の Web へのアクセス

  デザイナーが Azure でリソースを作成し、ロジック アプリでコネクタからプロパティやデータを読み取るには、インターネット接続が必要です。 
  たとえば、Dynamics CRM Online の接続の場合、デザイナーは CRM インスタンスをチェックして、既定のプロパティやカスタム プロパティを確認します。

* Logic Apps でサポートされるメール アカウント (Office 365 Outlook、Outlook.com、Gmail など)。 その他のプロバイダーについては、[こちらのコネクタ一覧を参照](https://docs.microsoft.com/connectors/)してください。 この例では Office 365 Outlook を使います。 別のプロバイダーを使用する場合も、全体的な手順は同じです。ただし、UI がやや異なる場合があります。

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Azure リソース グループ プロジェクトを作成する

最初に、[Azure リソース グループ プロジェクト](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)を作成します。 Azure リソース グループとリソースについて詳しくは、[こちら](../azure-resource-manager/resource-group-overview.md)を参照してください。

1. Visual Studio を起動します。 Azure のアカウントを使用してサインインします。

1. **[ファイル]** メニューで、 **[新規作成]**  >  **[プロジェクト]** の順に選択します (Ctrl + Shift + N キー)。

   ![[ファイル] メニューで [新規作成]、[プロジェクト] の順に選択する](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. **[インストール済み]** で、 **[Visual C#]** または **[Visual Basic]** を選択します。 **[クラウド]**  >  **[Azure リソース グループ]** の順に選択します。 プロジェクトに名前を付けます。例:

   ![Azure リソース グループ プロジェクトを作成する](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > **[クラウド]** または **[Azure リソース グループ]** が表示されない場合は、Azure SDK for Visual Studio がインストールされていることを確認してください。

   Visual Studio 2019 を使用している場合は、以下の手順に従ってください。

   1. **[新しいプロジェクトの作成]** ボックスで、Visual C# または Visual Basic の **[Azure リソース グループ]** プロジェクトを選択します。 **[次へ]** を選択します。

   1. 使用する Azure リソース グループの名前やその他のプロジェクト情報を指定します。 **[作成]** を選択します。

1. テンプレートの一覧で、 **[ロジック アプリ]** テンプレートを選択します。 **[OK]** を選択します。

   ![ロジック アプリ テンプレートを選ぶ](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   プロジェクトが作成された後、ソリューション エクスプローラーが開かれ、ソリューションが表示されます。 
   ソリューションで、**LogicApp.json** ファイルはロジック アプリの定義を格納するだけでなく、デプロイのために使用できる Azure Resource Manager テンプレートにもなっています。

   ![ソリューション エクスプローラーに新しいロジック アプリ ソリューションとデプロイ ファイルが表示される](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>空のロジック アプリを作成する

Azure リソース グループ プロジェクトが作成されたら、 **[空のロジック アプリ]** テンプレートを使用してロジック アプリを作成します。

1. ソリューション エクスプローラーで、**LogicApp.json** ファイルのショートカット メニューを開きます。 **[Open With Logic App Designer]\(ロジック アプリ デザイナーで開く\)** を選択します (Ctrl + L キー)。

   ![ロジック アプリ デザイナーでロジック アプリの .json ファイルを開く](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > このコマンドが Visual Studio 2019 にない場合は、Visual Studio の最新の更新プログラムが適用されていることを確認してください。

   Visual Studio では、ロジック アプリや接続のリソースを作成してデプロイするために Azure サブスクリプションと Azure リソース グループを指定するよう求められます。

1. **[サブスクリプション]** で、Azure サブスクリプションを選択します。 **[リソース グループ]** で、 **[新規作成]** を選択して新しい Azure リソース グループを作成します。

   ![Azure サブスクリプション、リソース グループ、リソースの場所を選択する](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Setting | 値の例 | 説明 |
   | ------- | ------------- | ----------- |
   | ユーザー プロファイルの一覧 | Contoso <br> jamalhartnett@contoso.com | 既定ではサインインに使用したアカウント |
   | **サブスクリプション** | 従量課金制 <br> (jamalhartnett@contoso.com) | Azure サブスクリプションの名前および関連付けられたアカウント |
   | **リソース グループ** | MyLogicApp-RG <br> (米国西部) | ロジック アプリのリソースを格納およびデプロイするための Azure リソース グループと場所 |
   | **Location** | MyLogicApp-RG2 <br> (米国西部) | リソース グループの場所を使用しない場合の別の場所 |
   ||||

1. Logic Apps デザイナーが開き、紹介ビデオやよく使用されるトリガーが含まれたページが表示されます。 ビデオやトリガーの後の **[テンプレート]** まで下へスクロールし、 **[空のロジック アプリ]** を選択します。

   ![[空のロジック アプリ] を選択する](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>ロジック アプリ ワークフローを構築する

次に、新しいフィード項目が現れると起動される RSS [トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 ロジック アプリはすべて、特定の条件が満たされると起動されるトリガーで開始されます。 トリガーが起動されるたびに、ワークフローを実行するロジック アプリ インスタンスが Logic Apps エンジンによって作成されます。

1. ロジック アプリ デザイナーの検索ボックスの下で、 **[すべて]** を選択します。
検索ボックスに「rss」と入力します。 トリガーの一覧から、 **[フィード項目が発行される場合 - RSS]** トリガーを選択します

   ![トリガーとアクションを追加してロジック アプリを構築する](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. デザイナーにトリガーが表示されたら、[Azure Portal クイック スタート](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)にあるワークフロー手順に従ってロジック アプリの構築を完了してから、この記事に戻ります。 完了すると、ロジック アプリは次の例のようになります。

   ![完成したロジック アプリ](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app.png)

1. Visual Studio ソリューションを保存します。 (Ctrl + S キー)。

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>ロジック アプリを Azure にデプロイする

ロジック アプリを実行してテストする前に、Visual Studio からそのアプリを Azure にデプロイします。

1. ソリューション エクスプローラーのプロジェクトのショートカット メニューで、 **[デプロイ]**  >  **[新規作成]** の順に選択します。 メッセージに従って Azure アカウントでサインインします。

   ![ロジック アプリ デプロイを作成する](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. このデプロイでは、既定の Azure サブスクリプション、リソース グループ、およびその他の設定を保持します。 **[配置]** を選択します。

   ![ロジック アプリを Azure リソース グループにデプロイする](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. **[パラメーターの編集]** ボックスが表示された場合は、ロジック アプリのリソース名を指定します。 設定を保存します。

   ![ロジック アプリのデプロイ名を入力する](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   デプロイが開始されると、Visual Studio の **[出力]** ウィンドウにアプリのデプロイ状態が表示されます。 状態が表示されない場合、 **[Show output from]\(出力元の表示\)** の一覧を開いて、Azure リソース グループを選択します。

   ![デプロイの状態の出力](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   選択したコネクタにユーザーからの入力が必要な場合は、バックグラウンドで PowerShell ウィンドウが開き、必要なパスワードまたはシークレット キーの入力を求められます。 その情報を入力すると、デプロイが続行されます。

   ![PowerShell ウィンドウ](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   デプロイが完了すると、ロジック アプリは Azure Portal で有効になり、指定されたスケジュールで (1 分ごとに) 実行されます。 新しいフィード項目が検出されるとトリガーが起動され、それにより、ロジック アプリのアクションを実行するワークフロー インスタンスが作成されます。 ロジック アプリは、新しい項目ごとに電子メールを送信します。 新しい項目が検出されない場合、トリガーは起動されず、ワークフローのインスタンス化を "スキップ" します。 ロジック アプリは、次の間隔まで待ってからチェックします。

   このロジック アプリが送信するサンプルの電子メールを次に示します。 
   電子メールが届かない場合は、迷惑メール フォルダーを確認してください。

   ![Outlook が新しい RSS 項目ごとにメールを送信する](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

これで、Visual Studio でロジック アプリが正常に構築およびデプロイされました。 ロジック アプリを管理して実行履歴を確認するには、「[Manage logic apps with Visual Studio (Visual Studio でロジック アプリを管理する)](../logic-apps/manage-logic-apps-with-visual-studio.md)」を参照してください。

## <a name="add-new-logic-app"></a>新しいロジック アプリを追加する

既存の Azure リソース グループ プロジェクトがある場合は、[JSON アウトライン] ウィンドウを使用してそのプロジェクトに新しい空のロジック アプリを追加できます。

1. ソリューション エクスプローラーで、`<logic-app-name>.json` ファイルを開きます。

1. **[表示]** メニューの **[その他のウィンドウ]**  >  **[JSON アウトライン]** を選択します。

1. テンプレート ファイルにリソースを追加するには、[JSON アウトライン] ウィンドウの上部にある **[リソースの追加]** を選択します。 または、[JSON アウトライン] ウィンドウで、 **[リソース]** を右クリックして、 **[新しいリソースの追加]** を選びます。

   ![[JSON アウトライン] ウィンドウ](./media/quickstart-create-logic-apps-with-visual-studio/jsonoutline.png)

1. **[リソースの追加]** ダイアログ ボックスで、 **[ロジック アプリ]** を探して選びます。 ロジック アプリの名前を指定し、 **[追加]** を選びます。

   ![リソースの追加](./media/quickstart-create-logic-apps-with-visual-studio/addresource.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ロジック アプリの使用を完了したら、ロジック アプリと関連リソースが含まれているリソース グループを削除します。

1. ロジック アプリの作成に使用したのと同じアカウントで、[Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメイン メニューで **[リソース グループ]** を選択します。
ロジック アプリのリソース グループを選択し、 **[概要]** を選択します。

1. **[概要]** ページで **[リソース グループの削除]** を選択します。 確認のためにリソース グループ名を入力し、 **[削除]** を選択します。

   ![[リソース グループ] > [概要] > [リソース グループの削除]](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

1. ローカル コンピューターで Visual Studio ソリューションを削除します。

## <a name="next-steps"></a>次の手順

この記事では、Visual Studio を使用してロジック アプリの構築、デプロイ、実行を行いました。 Visual Studio でロジック アプリの高度なデプロイを管理および実行する方法の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> * [Visual Studio でロジック アプリを管理する](../logic-apps/manage-logic-apps-with-visual-studio.md)