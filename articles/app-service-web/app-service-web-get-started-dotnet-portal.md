---
title: "5 分間で Azure Portal で Umbraco Web アプリをデプロイする | Microsoft Docs"
description: "サンプル ASP.NET アプリをデプロイして、App Service での Web アプリの実行がいかに簡単であるかを説明します。 結果はすぐにわかります。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: fa3f31cdd708729071876ffad707bea70567da83
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>5 分間で Azure Portal で Umbraco Web アプリをデプロイする

このチュートリアルでは、数分で [Umbraco](https://our.umbraco.org/) Web アプリを [Azure App Service](../app-service/app-service-value-prop-what-is.md) にデプロイします。

![Umbraco アプリ](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>前提条件
Microsoft Azure のアカウントが必要です。 アカウントを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)するか [Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。

> [!NOTE]
> Azure アカウントがなくても、[App Service を試用](https://azure.microsoft.com/try/app-service/)できます。 スターター アプリを作成し、最大&1; 時間使用できます。クレジット カードも契約も不要です。
> 
> 

## <a name="deploy-the-aspnet-app"></a>ASP.NET アプリをデプロイする
1. [Azure Portal](https://portal.azure.com) にサインインします。

2. [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS) を開きます。

    このリンクは、Azure Portal で新しい Umbraco アプリを即座に構成するためのショートカットです。

3. **[アプリ名]** に Web アプリ名を入力します。 この名前が `azurewebsites.net` ドメイン内で一意の場合は、ボックスに緑色のチェックマークが表示されます。
   
5. **[リソース グループ]** で **[新規作成]** をクリックして新しい[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成し、名前を指定します。

7. **[App Service プラン/場所]** > **[新規作成]** の順にクリックします。 [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を次のように構成します。

    - **[App Service プラン]** に希望する名前を入力します。
    - **[場所]** で、プランをホストする場所を選択します。
    - **[価格レベル]** をクリックし、**[F1 Free]** または別の適切なレベルを選択してから、**[選択]** をクリックします。
    - **[OK]**をクリックします。

    Umbraco CMS の構成は、次のスクリーンショットのようになります。

    ![構成の実行中 - Azure App Service での初めての Umbraco](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. **[SQL Database]** > **[新しいデータベースの作成]** の順にクリックします。 SQL Database を次のように構成します。

    - **[名前]** に **myDB** などの名前を入力します。
    - **[価格レベル]** をクリックし、**[F Free]** または別の適切なレベルを選択してから、**[選択]** をクリックします。
    - **[ターゲット サーバー]** > **[新しいサーバーの作成]** の順にクリックします。 データベース サーバーを次のように構成します。

        - **[サーバー名]** にサーバー名を入力します。 この名前が `.database.windows.net` ドメイン内で一意の場合は、ボックスに緑色のチェック マークが表示されます。
        - **[サーバー管理者のログイン]** に希望する管理者ユーザー名を入力します。
        - **[パスワード]** と **[パスワードの確認]** に希望するパスワードを入力します。
        - [場所] で、Web アプリで使用するのと同じ場所を選択します。
        - **[Azure サービスにサーバーへのアクセスを許可する]** が選択されていることを確認します。
        - **[選択]**をクリックします。
    
    - **[選択]**をクリックします。

13. **[Web アプリの設定]** をクリックして、データベース ユーザー名とパスワードを指定し、**[OK]** をクリックします。

    Umbraco CMS の構成は、次のスクリーンショットのようになります。

    ![構成完了 - Azure App Service での初めての Umbraco](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. **[作成]**をクリックします。
    
    ここで、構成に基づいて Azure で Umbraco アプリが作成されます。 "**デプロイが開始されました...**" という通知が表示されます。

    ![デプロイの成功 - Azure App Service での初めての Umbraco](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>Umbraco Web アプリを起動して管理する

Azure でアプリのデプロイが完了すると、別の通知が表示されます。

![デプロイの成功 - Azure App Service での初めての Umbraco](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. 通知をクリックします。 見逃した場合は、通知ベル (![通知ベル - Azure App Service での初めての Umbraco](./media/app-service-web-get-started-dotnet-portal/notification.png)) をクリックするといつでもアクセスできます。

    Web アプリの管理[ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources) ("*ブレード*": 水平方向に表示されるポータル ページが) が表示されます。

3. [概要] ページの上部にある **[参照]** をクリックします。
   
    ![参照 - Azure App Service での初めての Umbraco](./media/app-service-web-get-started-dotnet-portal/browse.png)

    Umbraco の**ようこそ**ページが表示されます。 Umbraco のインストールを構成して使ってみましょう。

    ![Umbraco の構成 - Azure App Service での初めての Umbraco](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>次のステップ
* [Visual Studio を使用して Azure App Service に ASP.NET Web アプリをデプロイする](web-sites-dotnet-get-started.md) - 含まれるアプリケーション テンプレートのいずれかを使用して、Visual Studio から新しい Azure Web アプリを作成する方法について学習します。
* [Azure App Service にコードをデプロイする](web-sites-deploy.md) - FTP やソース管理リポジトリからデプロイする方法について学習します。
* [初めての Web アプリに機能を追加する](app-service-web-get-started-2.md) - Azure アプリを次のレベルに進めます。 ユーザーを認証します。 必要に応じてスケールを変更したり、 パフォーマンスのアラートを設定したりできます。 いずれも、数回のクリックで実現できます。

