---
title: Visual Studio で Azure Logic Apps と Azure Functions を使用して サーバーレス アプリを構築する
description: Visual Studio で Azure Logic Apps と Azure Functions を使用して初めてのサーバーレス アプリを構築、展開、管理します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 04/02/2019
ms.openlocfilehash: 39b44668a89ce0c77c09a7fa20dc4d95b2164bf4
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863000"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Azure Logic Apps と Azure Functions を使用して初めてのサーバーレス アプリを構築する - Visual Studio

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) や [Azure Functions](../azure-functions/functions-overview.md) などの Azure のサーバーレス ツールと機能を使用すると、クラウド アプリを短時間で開発して展開できます。 この記事では、Visual Studio で、Azure 関数を呼び出すロジック アプリを使用するサーバーレス アプリの構築を始める方法を示します。 Azure でのサーバーレス ソリューションについて詳しくは、「[Functions と Logic Apps を含む Azure Serverless の概要](../logic-apps/logic-apps-serverless-overview.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Visual Studio でサーバーレス アプリを構築するには、次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* まだお持ちでない場合は、以下のツールをダウンロードしてインストールしてください。

  * <a href="https://aka.ms/download-visual-studio" target="_blank">Visual Studio 2019、2017、または 2015 - Community Edition 以降</a>。 
  このクイックスタートでは、無料の Visual Studio Community 2017 を使用します。

    > [!IMPORTANT]
    > Visual Studio 2019 または 2017 をインストールする場合は、**[Azure の開発]** ワークロードを選択してください。
    > Visual Studio 2019 の場合、Cloud Explorer は Azure portal でロジック アプリ デザイナーを開くことはできますが、まだ埋め込まれている Logic App Designer を開くことはできません。

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Microsoft Azure SDK for .NET (2.9.1 以降)</a>。 <a href="https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet">Azure SDK for .NET</a> の詳細を参照してください。

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * 必要な Visual Studio バージョン用の Azure Logic Apps Tools:

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019" target="_blank">Visual Studio 2019</a>

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017" target="_blank">Visual Studio 2017</a>

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015" target="_blank">Visual Studio 2015</a>
  
    Azure Logic Apps Tools は、Visual Studio Marketplace から直接ダウンロードしてインストールできます。または、<a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">この拡張機能を Visual Studio 内からインストールする方法</a>を確認できます。 
    インストールが完了したら、必ず Visual Studio を再起動してください。

  * ローカルで関数をデバッグするための <a href="https://www.npmjs.com/package/azure-functions-core-tools" target="_blank">Azure Functions Core Tools</a>

* 組み込みのロジック アプリ デザイナーを使用する際の Web へのアクセス

  デザイナーが Azure でリソースを作成し、ロジック アプリでコネクタからプロパティとデータを読み取るためには、インターネット接続が必要です。 
  たとえば、Dynamics CRM Online コネクタを使用する場合、デザイナーは CRM インスタンスの使用可能な既定およびカスタムのプロパティをチェックします。

## <a name="create-resource-group-project"></a>リソース グループ プロジェクトを作成する

最初に、サーバーレス アプリ用の [Azure リソース グループ プロジェクト](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)を作成します。 Azure では、リソース グループ内にリソースを作成します。リソース グループは、アプリ全体に対するリソースを 1 つの資産として整理、管理、展開するために使用する論理コレクションです。 Azure のサーバーレス アプリの場合、リソース グループには Azure Logic Apps と Azure Functions の両方に対するリソースが含まれています。 Azure リソース グループとリソースについて詳しくは、[こちら](../azure-resource-manager/resource-group-overview.md)を参照してください。

1. Visual Studio を起動し、Azure アカウントでサインインします。

1. **[ファイル]** メニューで、**[新規作成]** > **[プロジェクト]** の順に選択します 

   ![Visual Studio で新しいプロジェクトを作成する](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. **[インストール済み]** で、**[Visual C#]** または **[Visual Basic]** を選択します。 **[クラウド]** > **[Azure リソース グループ]** の順に選択します。

   > [!NOTE]
   > **[クラウド]** カテゴリまたは **[Azure リソース グループ]** プロジェクトが存在しない場合は、Azure SDK for Visual Studio をインストールしたことを確認してください。

   Visual Studio 2019 を使用している場合は、以下の手順に従ってください。

   1. **[新しいプロジェクトの作成]** ボックスで、Visual C# または Visual Basic の **[Azure リソース グループ]** プロジェクト テンプレートを選択し、**[次へ]** を選択します。

   1. 使用する Azure リソース グループの名前と、その他のプロジェクト情報を指定します。 操作が完了したら、**[作成]** を選択します。

1. プロジェクトの名前と場所を指定して、**[OK]** を選択します。

   Visual Studio でテンプレートの一覧からテンプレートを選択するように求められます。 
   この例では、ロジック アプリと Azure 関数の呼び出しを含むサーバーレス アプリを構築できるように、Azure クイック スタート テンプレートを使用します。

   > [!TIP]
   > Azure リソース グループにソリューションを 事前デプロイしたくないシナリオでは、空のロジック アプリが作成されるだけの空の**ロジック アプリ** テンプレートを使用できます。

1. **[この場所からテンプレートを参照します]** の一覧から、**[Azure クイック スタート (github.com/Azure/azure-quickstart-templates)]** を選択します。

1. 検索ボックスに、フィルターとして「logic-app」と入力します。 結果から次のテンプレートを選択します。**101-logic-app-and-function-app**

   ![Azure クイックスタート テンプレートを選択する](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio で、リソース グループ プロジェクト用のソリューションが作成されて開かれます。 
   選択した Azure クイック スタート テンプレートにより、リソース グループ プロジェクト内に `azuredeploy.json` という名前の配置テンプレートが作成されます。 この配置テンプレートには、HTTP 要求でトリガーし、Azure 関数を呼び出し、HTTP 応答として結果を返す、簡単なロジック アプリの定義が含まれています。

   ![新しいサーバーレス ソリューション](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 次に、配置テンプレートを開いてサーバーレス アプリのリソースを確認するには、先にソリューションを Azure に展開する必要があります。

## <a name="deploy-your-solution"></a>ソリューションのデプロイ

Visual Studio でロジック アプリ デザイナーを使用してロジック アプリを開く前に、Azure リソース グループが Azure に既に展開されている必要があります。 これにより、デザイナーはロジック アプリ内のリソースやサービスへの接続を作成できます。 このタスクでは、Visual Studio から Azure portal にソリューションを展開します。

1. ソリューション エクスプローラーでリソース プロジェクトのショートカット メニューから、**[配置]** > **[新規]** の順に選択します。

   ![リソース グループの新しい展開を作成する](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. まだ選択していない場合は、展開する Azure サブスクリプションとリソース グループを選択します。 **[配置]** を選択します。

   ![[配置の設定]](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. **[パラメーターの編集]** ボックスが表示されたら、展開時にロジック アプリと Azure 関数に対して使用するリソース名を指定して、設定を保存します。 関数アプリに対してはグローバルに一意の名前を使用する必要があります。

   ![ロジック アプリと関数アプリの名前を指定する](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Visual Studio で指定したリソース グループへの展開が始まると、Visual Studio の **[出力]** ウィンドウにソリューションの展開の状態が表示されます。 
   展開が終了すると、ロジック アプリが Azure portal に存在するようになります。

## <a name="edit-logic-app-in-visual-studio"></a>Visual Studio でロジック アプリを編集する

ソリューションがリソース グループに配置されたので、ロジック アプリを編集して変更できるように、ロジック アプリ デザイナーでロジック アプリを開きます。

1. ソリューション エクスプローラーで `azuredeploy.json` ファイルのショートカット メニューから、**[Open With Logic App Designer]\(ロジック アプリ デザイナーで開く\)** を選択します。

   ![ロジック アプリ デザイナーで "azuredeploy.json" を開く](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. **[ロジック アプリのプロパティ]** ボックスが表示されたら、まだ選択されていない場合は、**[サブスクリプション]** で Azure サブスクリプションを選択します。 **[リソース グループ]** で、ソリューションを展開したリソース グループと場所を選択して、**[OK]** を選択します。

   ![ロジック アプリのプロパティ](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   ロジック アプリ デザイナーが開いたら、手順を追加したりワークフローを変更したりして、更新を保存します。

   ![ロジック アプリ デザイナーで開かれたロジック アプリ](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Azure Functions プロジェクトを作成する

JavaScript、Python、F#、PowerShell、Batch、または Bash を使用して Functions プロジェクトと関数を作成するには、記事「[Azure Functions Core Tools の操作](../azure-functions/functions-run-local.md)」の手順に従ってください。 ソリューション内で C# を使用して Azure 関数を開発するには、記事「[Publish a .NET class library as a Function App](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)」(関数アプリとして .NET クラス ライブラリを発行する) の手順に従って C# クラス ライブラリを使用できます。

## <a name="deploy-functions-from-visual-studio"></a>Visual Studio から関数を展開する

ここで使用している配置テンプレートでは、`azuredeploy.json` ファイルの変数で指定されている Git リポジトリから、ソリューション内に作成した Azure 関数が展開されます。 ソリューションで Functions プロジェクトを作成した場合、GitHub や Azure DevOps などの Git ソース管理にそのプロジェクトをチェックインしてから、テンプレートがその Azure 関数を展開するように `repo` 変数を更新します。

## <a name="manage-logic-apps-and-view-run-history"></a>ロジック アプリを管理して実行履歴を表示する

Azure に既に展開されているロジック アプリについても、Visual Studio から編集、管理、実行履歴の表示、無効化を行うことができます。

1. Visual Studio の **[表示]** メニューで、**[Cloud Explorer]** を選択します。

1. **[すべてのサブスクリプション]** で、管理するロジック アプリに関連付けられている Azure サブスクリプションを選択して、**[適用]** を選択します。

1. **[Logic Apps]** でロジック アプリを選択します。 アプリのショートカット メニューで、**[Logic App Editor で開く]** を選択します。

既に発行されているロジック アプリをリソース グループ プロジェクトにダウンロードできます。 したがって、Azure portal でロジック アプリの使用を始めた場合でも、Visual Studio でそのアプリをインポートして管理できます。 詳しくは、「[Visual Studio でロジック アプリを管理する](../logic-apps/manage-logic-apps-with-visual-studio.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [Visual Studio でロジック アプリを管理する](manage-logic-apps-with-visual-studio.md)
