---
title: C# ASP.NET Framework Web アプリを作成する - Azure App Service | Microsoft Docs
description: 既定の C# ASP.NET Web アプリをデプロイして、Azure App Service で Web アプリを実行する方法を確認します。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4f479ad60d74f1c7381b5fb776c5508aaa0785f1
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242256"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Azure に ASP.NET Framework Web アプリを作成する

[Azure App Service](overview.md) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。

このクイック スタートでは、Azure App Service に初めての ASP.NET Web アプリをデプロイする方法を示します。 完了すると、App Service プランが作成されます。 また、App Service アプリが作成され、Web アプリケーションがデプロイされます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、**ASP.NET および Web 開発**のワークロードと共に、<a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> をインストールします。

Visual Studio 2019 を既にインストールしている場合:

- **[ヘルプ]**  >  **[更新プログラムの確認]** の順に選択して、Visual Studio に最新の更新プログラムをインストールします。
- **[ツール]**  >  **[ツールと機能を取得]** の順に選択し、ワークロードを追加します。

## ASP.NET Web アプリを作成する<a name="create-and-publish-the-web-app"></a>

次の手順に従って ASP.NET Web アプリを作成します。

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、C# の **[ASP.NET Web アプリケーション (.NET Framework)]** を選択し、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、アプリケーションに _myFirstAzureWebApp_ という名前を付け、 **[作成]** を選択します。

   ![Web アプリ プロジェクトを構成する](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

1. 任意の種類の ASP.NET Web アプリを Azure にデプロイできます。 このクイックスタートでは、 **[MVC]** テンプレートを選択します。 

1. 認証が **[認証なし]** に設定されていることを確認します **作成** を選択します。

   ![ASP.NET Web アプリケーションを作成する](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

1. Visual Studio のメニューから **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、Web アプリをローカルで実行します。

   ![アプリをローカルで実行する](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## Web アプリを発行する<a name="launch-the-publish-wizard"></a>

1. **ソリューション エクスプローラー**で **myFirstAzureWebApp** プロジェクトを右クリックし、 **[発行]** を選択します。

1. **App Service** を選択し、 **[発行]** を選択します。

   ![プロジェクトの概要ページから発行する](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. **App Service の [新規作成]** に表示されるオプションは、既に Azure にサインインしているかどうかや、Visual Studio アカウントが Azure アカウントとリンクされているかどうかによって異なります。 **[アカウントの追加]** または **[サインイン]** を選択して Azure サブスクリプションにサインインします。 既にサインインしている場合は、目的のアカウントを選択します。

   > [!NOTE]
   > 既にサインインしている場合は、まだ **[作成]** を選択しないでください。
   >
   >

   ![Azure へのサインイン](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. **[リソース グループ]** で、 **[新規]** を選択します。

1. **[新しいリソース グループ名]** に「*myResourceGroup*」と入力し、 **[OK]** を選択します。

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. **[ホスティング プラン]** で **[新規]** を選択します。

1. **[ホスティング プランの構成]** ダイアログで、次の表の値を入力し、 **[OK]** を選択します。

   | Setting | 推奨値 | 説明 |
   |-|-|-|
   |App Service プラン| myAppServicePlan | App Service プランの名前です。 |
   | Location | 西ヨーロッパ | Web アプリがホストされているデータ センターです。 |
   | Size | 無料 | [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)によって、ホスティング機能が決まります。 |

   ![Create App Service plan](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. **[名前]** に、有効な文字 (`a-z`、`A-Z`、`0-9`、`-`) のみから成る一意のアプリ名を入力します。 自動的に生成される一意の名前をそのまま使用してもかまいません。 Web アプリの URL は `http://<app_name>.azurewebsites.net` です。`<app_name>` には自分のアプリの名前を指定します。

   ![アプリ名を構成する](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

1. **[作成]** をクリックして、Azure リソースの作成を開始します。

ウィザードの完了後に、Azure に ASP.NET Web アプリを発行してから、既定のブラウザーでアプリを起動します。

![Azure で発行された ASP.NET Web アプリ](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

**App Service の [新規作成]** ページで指定したアプリ名が、`http://<app_name>.azurewebsites.net` 形式の URL プレフィックスとして使用されます。

**お疲れさまでした。** ASP.NET Web アプリを Azure App Service でライブ実行することができました。

## <a name="update-the-app-and-redeploy"></a>アプリを更新して再デプロイする

1. **ソリューション エクスプローラー**で、対象プロジェクトの **[表示]**  >  **[ホーム]**  >  **[Index.cshtml]** を順に開きます。

1. 上部の `<div class="jumbotron">` HTML タグを検索し、要素全体を次のコードに置き換えます。

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure に再デプロイするには、**ソリューション エクスプローラー**で **myFirstAzureWebApp** プロジェクトを右クリックし、 **[発行]** を選択します。

1. **発行**の概要ページで **[発行]** を選択します。

   ![Visual Studio の発行の概要ページ](./media/app-service-web-get-started-dotnet-framework/publish-summary-page-framework-vs2019.png)

発行が完了すると、Visual Studio で Web アプリの URL のブラウザーが起動されます。

![Azure で更新された ASP.NET Web アプリ](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Azure アプリの管理

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> に移動して、Web アプリを管理します。

1. 左側のメニューで、 **[App Services]** を選択し、お客様の Azure アプリの名前を選択します。

   ![Azure アプリへのポータル ナビゲーション](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Web アプリの [概要] ページを確認します。 ここでは、参照、停止、開始、再開、削除のような基本的な管理を行うことができます。

   ![Azure portal の App Service の概要](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   左側のメニューは、アプリを構成するためのさまざまなページを示しています。

## <a name="video"></a>ビデオ

このクイックスタートの手順が実行されているところを次の動画で確認したうえで、実際に手順に従って初めての .NET アプリを Azure に発行してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ASP.NET と SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
