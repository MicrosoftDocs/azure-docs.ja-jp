---
title: C# ASP.NET Core Web アプリを作成する - Azure App Service | Microsoft Docs
description: 既定の C# ASP.NET Core Web アプリをデプロイして、Azure App Service で Web アプリを実行する方法を確認します。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2762a81c9f366ad4a5d0d6d87cf04490fc10b23c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630586"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Azure に ASP.NET Core Web アプリを作成する

> [!NOTE]
> この記事では、Windows 上の App Service にアプリをデプロイします。 _Linux_ 上の App Service にデプロイするには、「[App Service on Linux での .NET Core Web アプリの作成](./containers/quickstart-dotnetcore.md)」をご覧ください。 
>

[Azure App Service](overview.md) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。  このクイック スタートでは、Azure App Service に初めての ASP.NET Core Web アプリをデプロイする方法について説明します。 完了すると、デプロイされた Web アプリケーションを含む App Service アプリと App Service プランで構成されるリソース グループが完成します。

![](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、**ASP.NET および Web 開発**のワークロードと共に、<a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> をインストールします。

Visual Studio 2017 を既にインストールしている場合:

- **[ヘルプ]**、**[更新プログラムの確認]** の順にクリックし、Visual Studio に最新の更新プログラムをインストールします。
- **[ツール]**、**[ツールと機能を取得]** の順にクリックし、ワークロードを追加します。

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

Visual Studio で、**[ファイル]、[新規作成]、[プロジェクト]** の順にクリックして、プロジェクトを作成します。 

**[新しいプロジェクト]** ダイアログ ボックスで、**[Visual C#]、[Web]、[ASP.NET Core Web アプリケーション]** の順にクリックします。

アプリケーションに _myFirstAzureWebApp_ という名前を付けて、**[OK]** をクリックします。
   
![New Project dialog box](./media/app-service-web-get-started-dotnet/new-project.png)

任意の種類の ASP.NET Core Web アプリを Azure にデプロイできます。 このクイックスタートでは、**[Web アプリケーション]** テンプレートを選択し、認証が **[認証なし]** に設定されていること、他のオプションは選択されていないことを確認してください。
      
**[OK]** を選択します。

![[新しい ASP.NET プロジェクト] ダイアログ ボックス](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

メニューから、**[デバッグ]、[デバッグなしで開始]** の順にクリックし、ローカルで Web アプリを実行します。

![アプリをローカルで実行する](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="launch-the-publish-wizard"></a>発行ウィザードを起動する

**ソリューション エクスプローラー**で **myFirstAzureWebApp** プロジェクトを右クリックし、**[発行]** を選択します。

![ソリューション エクスプローラーから発行する](./media/app-service-web-get-started-dotnet/right-click-publish.png)

発行ウィザードが自動的に起動します。 **[App Service]**、**[発行]** の順に選択し、**[App Service の作成]** ダイアログを開きます。

![プロジェクトの概要ページから発行する](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

## <a name="sign-in-to-azure"></a>Azure へのサインイン

**[App Service の作成]** ダイアログで、**[アカウントの追加]** をクリックし、Azure サブスクリプションにサインインします。 既にサインインしている場合、ドロップダウンからアカウントを選択します。

> [!NOTE]
> 既にサインインしている場合は、まだ **[作成]** を選択しないでください。
>
   
![Azure へのサインイン](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

## <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

**[リソース グループ]** の横にある **[新規]** をクリックします。

リソース グループに **myResourceGroup** という名前を付けて、**[OK]** をクリックします。

## <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

**[ホスティング プラン]** の隣にある **[新規]** を選択します。 

**[ホスティング プランの構成]** ダイアログ ボックスで、スクリーン ショットの次の表に示した設定を使用します。

![Create App Service plan](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Setting | 推奨値 | 説明 |
|-|-|-|
|App Service プラン| myAppServicePlan | App Service プランの名前です。 |
| 場所 | 西ヨーロッパ | Web アプリがホストされているデータ センターです。 |
| サイズ | 無料 | [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)によって、ホスティング機能が決まります。 |

**[OK]** を選択します。

## <a name="create-and-publish-the-web-app"></a>Web アプリを作成して発行する

**[アプリ名]** に一意のアプリ名 (有効な文字は `a-z`、`0-9`、`-`) を入力するか、自動的に生成された一意の名前をそのまま使用します。 Web アプリの URL は `http://<app_name>.azurewebsites.net` です。`<app_name>` には自分のアプリの名前を指定します。

**[作成]** をクリックして、Azure リソースの作成を開始します。

![アプリ名を構成する](./media/app-service-web-get-started-dotnet/web-app-name.png)

ウィザードの完了後に、Azure に ASP.NET Core Web アプリを発行してから、既定のブラウザーでアプリを起動します。

![Azure で発行された ASP.NET Web アプリ](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

アプリを[作成して発行する手順](#create-and-publish-the-web-app)で指定した名前が、`http://<app_name>.azurewebsites.net` 形式の URL プレフィックスとして使用されます。

ASP.NET Core Web アプリを Azure App Service でライブ実行することができました。

## <a name="update-the-app-and-redeploy"></a>アプリを更新して再デプロイする

**ソリューション エクスプローラー**で、_Pages\Index.cshtml_ を開きます。

2 つの `<div>` タグを次のコードに置き換えます。

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Azure に再デプロイするには、**ソリューション エクスプローラー**で **myFirstAzureWebApp** プロジェクトを右クリックし、**[発行]** を選択します。

発行の概要ページで **[発行]** を選択します。
![Visual Studio の発行の概要ページ](./media/app-service-web-get-started-dotnet/publish-summary-page.png)

発行が完了すると、Visual Studio で Web アプリの URL のブラウザーが起動されます。

![Azure で更新された ASP.NET Web アプリ](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Azure アプリの管理

<a href="https://portal.azure.com" target="_blank">Azure Portal</a> に移動して、Web アプリを管理します。

左側のメニューで、**[App Services]** を選択し、お客様の Azure アプリの名前を選択します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-get-started-dotnet/access-portal.png)

Web アプリの [概要] ページを確認します。 ここでは、参照、停止、開始、再開、削除のような基本的な管理タスクを行うことができます。 

![Azure Portal の App Service ブレード](./media/app-service-web-get-started-dotnet/web-app-blade.png)

左側のメニューは、アプリを構成するためのさまざまなページを示しています。 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ASP.NET Core と SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
