---
title: クイック スタート:C# ASP.NET アプリを作成する
description: Visual Studio から既定の C# ASP.NET Web アプリ テンプレートをデプロイすることによって、Azure App Service で Web アプリを実行する方法について説明します。
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 08/06/2020
ms.custom: devx-track-csharp, mvc, devcenter, seodec18
ms.openlocfilehash: 5bf774f2b5cf4a6275dc04248aadfd9a94f5122d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212557"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Azure に ASP.NET Framework Web アプリを作成する

[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。

このクイック スタートでは、Azure App Service に初めての ASP.NET Web アプリをデプロイする方法を示します。 完了すると、App Service プランが作成されます。 また、App Service アプリが作成され、Web アプリケーションがデプロイされます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、**ASP.NET および Web 開発**のワークロードと共に、<a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> をインストールします。

Visual Studio 2019 を既にインストールしている場合:

- **[ヘルプ]**  >  **[更新プログラムの確認]** の順に選択して、Visual Studio に最新の更新プログラムをインストールします。
- **[ツール]**  >  **[ツールと機能を取得]** の順に選択し、ワークロードを追加します。

## <a name="create-an-aspnet-web-app"></a>ASP.NET Web アプリを作成する<a name="create-and-publish-the-web-app"></a>

次の手順に従って ASP.NET Web アプリを作成します。

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。

2. **[新しいプロジェクトの作成]** で、 **[ASP.NET Web アプリケーション (.NET Framework)]** を選択し、 **[次へ]** を選択します。

3. **[新しいプロジェクトの構成]** で、アプリケーションに _myFirstAzureWebApp_ という名前を付け、 **[作成]** を選択します。

   ![Web アプリ プロジェクトを構成する](./media/quickstart-dotnet-framework/configure-web-app-project-framework.png)

4. 任意の種類の ASP.NET Web アプリを Azure にデプロイできます。 このクイックスタートでは、 **[MVC]** テンプレートを選択します。

5. 認証が **[認証なし]** に設定されていることを確認します **［作成］** を選択します

   ![ASP.NET Web アプリケーションを作成する](./media/quickstart-dotnet-framework/select-mvc-template-vs2019.png)

6. Visual Studio のメニューから **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、Web アプリをローカルで実行します。

   ![アプリをローカルで実行する](./media/quickstart-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Web アプリを発行する<a name="launch-the-publish-wizard"></a>

1. **ソリューション エクスプローラー**で **myFirstAzureWebApp** プロジェクトを右クリックし、 **[発行]** を選択します。

1. **[App Service]** を選択し、 **[プロファイルの作成]** を選択します。

   ![プロジェクトの概要ページから発行する](./media/quickstart-dotnet-framework/publish-app-framework-vs2019.png)

1. **App Service の [新規作成]** に表示されるオプションは、既に Azure にサインインしているかどうかや、Visual Studio アカウントが Azure アカウントとリンクされているかどうかによって異なります。 **[アカウントの追加]** または **[サインイン]** を選択して Azure サブスクリプションにサインインします。 既にサインインしている場合は、目的のアカウントを選択します。

   > [!NOTE]
   > 既にサインインしている場合は、まだ **[作成]** を選択しないでください。
   >
   >

   ![Azure へのサインイン](./media/quickstart-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. **[リソース グループ]** で、 **[新規]** を選択します。

1. **[新しいリソース グループ名]** に「*myResourceGroup*」と入力し、 **[OK]** を選択します。

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. **[ホスティング プラン]** で **[新規]** を選択します。

1. **[ホスティング プランの構成]** ダイアログで、次の表の値を入力し、 **[OK]** を選択します。

   | 設定 | 推奨値 | 説明 |
   |-|-|-|
   | ホスティング プラン| myAppServicePlan | App Service プランの名前です。 |
   | 場所 | 西ヨーロッパ | Web アプリがホストされているデータ センターです。 |
   | サイズ | Free | [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)によって、ホスティング機能が決まります。 |

   ![Create App Service plan](./media/quickstart-dotnet-framework/app-service-plan-framework-vs2019.png)

1. **[名前]** に、有効な文字 (`a-z`、`A-Z`、`0-9`、`-`) のみから成る一意のアプリ名を入力します。 自動的に生成される一意の名前をそのまま使用してもかまいません。 Web アプリの URL は `http://<app-name>.azurewebsites.net` です。`<app-name>` には自分のアプリの名前を指定します。

2. **[作成]** をクリックして、Azure リソースの作成を開始します。

   ![アプリ名を構成する](./media/quickstart-dotnet-framework/web-app-name-framework-vs2019.png)

    ウィザードが完了すると、Azure リソースが作成され、発行する準備が整います。

3. **[発行]** ページで、 **[発行]** をクリックします。 Visual Studio によってアプリのビルド、パッケージ化、および Azure への発行が行われた後、既定のブラウザーでアプリが起動されます。

    ![Azure で発行された ASP.NET Web アプリ](./media/quickstart-dotnet-framework/published-azure-web-app.png)

**App Service の [新規作成]** ページで指定したアプリ名が、`http://<app-name>.azurewebsites.net` 形式の URL プレフィックスとして使用されます。

**お疲れさまでした。** ASP.NET Web アプリを Azure App Service でライブ実行することができました。

## <a name="update-the-app-and-redeploy"></a>アプリを更新して再デプロイする

1. **ソリューション エクスプローラー**で、対象プロジェクトの **[表示]**  >  **[ホーム]**  >  **[Index.cshtml]** を順に開きます。

1. 上部の `<div class="jumbotron">` HTML タグを検索し、要素全体を次のコードに置き換えます。

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure に再デプロイするには、**ソリューション エクスプローラー**で **myFirstAzureWebApp** プロジェクトを右クリックし、 **[発行]** を選択します。 続けて、 **[発行]** を選択します。

    発行が完了すると、Visual Studio で Web アプリの URL のブラウザーが起動されます。

    ![Azure で更新された ASP.NET Web アプリ](./media/quickstart-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Azure アプリの管理

1. Web アプリを管理するには、[Azure portal](https://portal.azure.com) にアクセスし、「**App Services**」を検索して選択します。

   ![App Services を選択する](./media/quickstart-dotnet-framework/app-services.png)

2. **[App Services]** ページで、Web アプリの名前を選択します。

   ![Azure アプリへのポータル ナビゲーション](./media/quickstart-dotnet-framework/access-portal-framework-vs2019.png)

   Web アプリの [概要] ページを確認します。 ここでは、参照、停止、開始、再開、削除のような基本的な管理を行うことができます。

   ![Azure portal の App Service の概要](./media/quickstart-dotnet-framework/web-app-general-framework-vs2019.png)

   左側のメニューは、アプリを構成するためのさまざまなページを示しています。

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ASP.NET と SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [ASP.NET アプリの構成](configure-language-dotnet-framework.md)
