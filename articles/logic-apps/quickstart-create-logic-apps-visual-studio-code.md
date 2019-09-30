---
title: Visual Studio Code を使用した自動ワークフローの作成と管理 - Azure Logic Apps
description: Visual Studio Code (VS Code) での JSON を使用したロジック アプリの作成および管理のクイック スタート
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/05/2018
ms.openlocfilehash: b9f9a402ecde09dd00c2b070f784858a141e556b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308997"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>クイック スタート:自動化されたロジック アプリ ワークフローの作成と管理 - Visual Studio Code

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と Visual Studio Code を使用して、ロジック アプリを作成し、管理できます。ロジック アプリを使用すると、組織や企業間でアプリ、データ、システム、サービスを統合するためのタスク、ワークフロー、プロセスを自動化できます。 この記事では、コード ベースのエクスペリエンスで JavaScript Object Notation (JSON) のワークフロー定義スキーマを使用することによって、ロジック アプリのワークフロー定義を作成および編集する方法について説明します。 Azure に既にデプロイされている既存のロジック アプリを使用することもできます。 

これらのタスクは Azure portal (https://portal.azure.com) と Visual Studio でも実行できますが、既にロジック アプリ定義を使い慣れていて、コードで直接作業する場合は、Visual Studio Code の方が迅速に作業を開始できます。 たとえば、既に作成されているロジック アプリを無効化、有効化、削除、更新することができます。 また、Visual Studio Code が実行されている開発プラットフォーム (Linux、Windows、Mac など) からロジック アプリと統合アカウントを操作することもできます。

この記事では、基本的な概念に重点を置いた、[Azure portal でのロジック アプリの作成に関するクイック スタート](../logic-apps/quickstart-create-first-logic-app-workflow.md)と同じロジック アプリを作成できます。 Visual Studio Code では、ロジック アプリは次の例のようになります。

![完成したロジック アプリ](./media/create-logic-apps-visual-studio-code/overview.png)

作業を開始するには、以下が必要です。

* Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [ロジック アプリのワークフロー定義](../logic-apps/logic-apps-workflow-definition-language.md)とその構造についての基礎知識 (JavaScript Object Notation (JSON) を使用) 

  Azure Logic Apps を使用するのが初めての場合、基本的な概念に重点を置いた、[Azure portal での最初のロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)を説明するクイック スタートをお試しください。 

* Azure と Azure サブスクリプションにサインインするための Web へのアクセス

* まだお持ちでない場合は、以下のツールをダウンロードしてインストールしてください。 

  * [Visual Studio Code バージョン 1.25.1 以降](https://code.visualstudio.com/) (無料)

  * Azure Logic Apps 用 Visual Studio Code 拡張機能

    この拡張機能は、[Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) からダウンロードしてインストールすることも、Visual Studio Code 内から直接インストールすることもできます。 
    インストール後、Visual Studio Code を再度読み込む必要があります。 

    !["Azure Logic Apps 用 Visual Studio Code 拡張機能" を見つける](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    拡張機能が正しくインストールされると、Visual Studio Code ツール バーに Azure アイコンが表示されます。 

    ![インストールされた拡張機能](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    詳細については、「[Extension Marketplace (拡張機能 Marketplace)](https://code.visualstudio.com/docs/editor/extension-gallery)」をご覧ください。 [GitHub の Visual Studio Code 用 Azure Logic Apps 拡張機能](https://github.com/Microsoft/vscode-azurelogicapps)を参照して、この拡張機能のオープン ソース バージョンを表示し、コントリビューションを送信することもできます。 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Azure へのサインイン

1. Visual Studio Code を開きます。 Visual Studio Code ツール バーで、Azure アイコンを選択します。 

   ![Azure アイコンを選択する](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. Azure ウィンドウで、 **[Logic Apps]** の **[Sign in to Azure]\(Azure にサインインする\)** を選択します。 

   ![[Sign in to Azure]\(Azure にサインインする\) を選択する](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   提供された認証コードを使用してサインインするよう求められます。 

1. 認証コードをコピーし、 **[Copy & Open]\(コピーして開く\)** を選択すると、新しいブラウザー ウィンドウが開きます。

   ![サインイン プロンプト](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. 認証コードを入力します。 メッセージが表示されたら、 **[続行]** を選択します。

   ![コードを入力する](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Azure アカウントを選択します。 サインインしたら、ブラウザーを閉じて Visual Studio Code に戻ることができます。

   Azure ウィンドウの [Logic Apps] ウィンドウと [統合アカウント] ウィンドウに、アカウントの Azure サブスクリプションが表示されます。 

   ![サブスクリプションを選択します。](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   必要なサブスクリプションが表示されていない場合は、 **[Logic Apps]** ラベルの横の **[サブスクリプションの選択]** (フィルター アイコン) をクリックします。 目的のサブスクリプションを探して選択します。

1. Azure サブスクリプション内の既存のロジック アプリまたは統合アカウントを表示するには、サブスクリプションを展開します。

   ![ロジック アプリと統合アカウントを表示する](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>ロジック アプリを作成する

1. Visual Studio Code 内から Azure サブスクリプションにサインインしていない場合は、この記事の手順に従って[今すぐサインイン](#sign-in-azure)します。

1. サブスクリプションのコンテキスト メニューで、 **[作成]** を選択します。

   ![[作成] を選択する](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. サブスクリプション内の Azure リソース グループを示す一覧で、既存のリソース グループを選択するか、 **[Create a new resource group]\(新しいリソース グループの作成\)** を選択します。 

   この例では、新しいリソース グループを作成します。

   ![新しいリソース グループの作成](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Azure リソース グループの名前を指定し、Enter キーを押します。

   ![新しいリソース グループの名前を指定する](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. ロジック アプリのメタデータを保存するデータ センターの場所を選択します。

   ![場所を選択する](./media/create-logic-apps-visual-studio-code/select-location.png)

1. ロジック アプリの名前を指定し、Enter キーを押します。

   ![ロジック アプリの名前を指定する](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Azure ウィンドウの Azure サブスクリプションの下に新しいロジック アプリが表示されます。 これで、ロジック アプリのワークフロー定義の作成を開始できます。

1. ロジック アプリのショートカット メニューで、 **[Open in Editor]\(エディターで開く\)** を選択します。 

   ![エディターでロジック アプリを開く](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   ロジック アプリのワークフローの作成を開始できるように、Visual Studio Code にロジック アプリ ワークフロー定義テンプレート (.logicapp.json ファイル) が開きます。

   ![ロジック アプリの新しいワークフロー定義](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. ロジック アプリ ワークフロー定義テンプレート ファイルで、ロジック アプリのワークフロー定義の作成を開始します。 テクニカル リファレンスについては、[Azure Logic Apps のワークフロー定義言語スキーマ](../logic-apps/logic-apps-workflow-definition-language.md)に関する記事をご覧ください。

   ロジックの定義の例を次に示します。 通常、JSON 要素は各セクション内でアルファベット順に表示されますが、このサンプルでは、ロジック アプリのステップがデザイナーに表示される順序でこれらの要素を大まかに示しています。

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. 完了したら、ロジック アプリ定義ファイルを保存します。 Azure サブスクリプションへのロジック アプリ定義のアップロードを確認するメッセージが表示されたら、 **[アップロード]** を選択します。

   ![新しいロジック アプリをアップロードする](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   ロジック アプリが Azure に発行されたら、アプリが稼働していることを Azure portal で確認できます。 

   ![Azure portal の発行済みのロジック アプリ](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>ロジック アプリを編集する

Azure に既にデプロイされている既存のロジック アプリを操作するには、Visual Studio Code でそのアプリのワークフロー定義ファイルを開きます。

1. Visual Studio Code 内から Azure サブスクリプションにサインインしていない場合は、この記事の手順に従って[今すぐサインイン](#sign-in-azure)します。

1. Azure ウィンドウの **[Logic Apps]** で、Azure サブスクリプションを展開し、目的のロジック アプリを選択します。 

1. ロジック アプリのメニューで、 **[Open in Editor]\(エディターで開く\)** を選択します。 または、ロジック アプリ名の横の編集アイコンをクリックします。

   ![既存のロジック アプリのエディターを開く](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   ロジック アプリのワークフロー定義の .logicapp.json ファイルが開きます。

   ![開かれたロジック アプリのワークフロー定義](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. ロジック アプリの定義を変更します。

1. 完了したら、変更を保存します。

1. Azure サブスクリプション内のロジック アプリ定義を更新するよう求められたら、 **[アップロード]** を選択します。 

   ![編集内容をアップロードする](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> * [Visual Studio でロジック アプリを作成する](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)