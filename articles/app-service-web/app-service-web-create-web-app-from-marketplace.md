---
title: "Azure Marketplace から Web アプリを作成する | Microsoft Docs"
description: "Azure ポータルを使用して Azure Marketplace から新しい WordPress Web アプリを作成する方法について説明します。"
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 972a296d-f927-470b-8534-0f2cb9eac223
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: a04c7129cd2e16c129f3e4b8e8e40f76ff37114d
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-web-app-from-the-azure-marketplace"></a>Azure Marketplace から Web アプリを作成する
<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace には、Microsoft、サード パーティ企業、およびオープン ソース ソフトウェア活動によって開発された多種多様な人気の Web アプリが用意されています。 たとえば、WordPress、Umbraco CMS、Drupal などです。これらの Web アプリは、この WordPress の例で使用される [PHP] をはじめ、[.NET]、[Node.js]、[Java]、[Python] など、さまざまなよく知られたフレームワーク上に構築されています。 Azure Marketplace から Web アプリを作成するために必要なソフトウェアは、 [Azure ポータル]に使用するブラウザーだけです。

このチュートリアルで学習する内容は次のとおりです。

* Azure App Service で Azure Marketplace テンプレートに基づく Web アプリを検索および作成します。
* 新しい Web アプリの Azure App Service 設定を構成します。
* Web アプリを起動して管理します。

このチュートリアルでは、WordPress ブログ サイトを Azure Marketplace からデプロイします。 このチュートリアルの手順を完了すると、独自の WordPress サイトをクラウドで運用できるようになります。

![Example WordPress wep app dashboard][WordPressDashboard1]

このチュートリアルでデプロイする WordPress サイトは、データベースに MySQL を使用します。 代わりに SQL Database を使用する場合は、[Project Nami] を参照してください。これは Azure Marketplace からも入手できます。

> [!NOTE]
> このチュートリアルを完了するには、Microsoft Azure アカウントが必要です。 アカウントを持っていない場合は、[Visual Studio サブスクライバーの特典を有効にする][activate]か、[無料試用版にサインアップ][free trial]してください。
> 
> Azure アカウントにサインアップする前に Azure App Service を開始する場合は、[App Service の試用]に関するページにアクセスしてください。 有効期間が短いスターター Web アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードや契約は必要ありません。
> 
> 

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Azure App Service での Web アプリの検索と作成
1. [Azure ポータル]にログインします。
2. **[新規]**をクリックします。
   
    ![Create a new Azure resource][MarketplaceStart]
3. **WordPress** を検索し、**[WordPress]** をクリックします。 MySQL の代わりに SQL Database を使用する場合は、 **Project Nami**を検索してください。
   
    ![Search for WordPress in the Marketplace][MarketplaceSearch]
4. WordPress アプリの説明を読んだら、 **[作成]**をクリックします。
   
    ![Create WordPress web app][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>新しい Web アプリの Azure App Service 設定の構成
1. 新しい Web アプリを作成すると、WordPress の設定ブレードが表示されます。以降の手順は、このブレードを使用して実行します。
   
    ![Configure WordPress web app settings][ConfigStart]
2. **[Web アプリ]** ボックスに Web アプリの名前を入力します。
   
    Web アプリの URL は *{name}*.azurewebsites.net のようになるため、この名前は azurewebsites.net ドメイン内で一意である必要があります。 入力した名前が一意でない場合は、テキスト ボックスに赤色の感嘆符が表示されます。
   
    ![Configure the WordPress web app name][ConfigAppName]
3. サブスクリプションが複数ある場合には、使用するものを&1; つ選択します。
   
    ![Configure the subscription for the web app][ConfigSubscription]
4. **リソース グループ** を選択するか、新しく作成します。
   
    リソース グループの詳細については、[Azure Resource Manager の概要][ResourceGroups]に関するページをご覧ください。
   
    ![Configure the resource group for the web app][ConfigResourceGroup]
5. **App Service プラン/場所** を選択するか、新しく作成します。
   
    App Service プランの詳細については、[Azure App Service プランの概要][AzureAppServicePlans]に関するページをご覧ください。
   
    ![Configure the service plan for the web app][ConfigServicePlan]
6. **[データベース]** をクリックし、**[新しい MySQL データベース]** ブレードで、MySQL データベースを構成するために必要な値を指定します。
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 新しい名前を入力するか、既定の名前をそのまま使用します。
   
    b. **[データベースの種類]** は **[共有]** のままにします。
   
    c. Web アプリ用に選択したのと同じ場所を選択します。
   
    d. 価格レベルを選択します。 このチュートリアルでは、**Mercury** (無料で、最小限の接続とディスク領域を使用可能) で問題ありません。
   
    e. **[新しい MySQL データベース]** ブレードで、法律条項に同意し、**[OK]** をクリックします。
   
    ![Configure the database settings for the web app][ConfigDatabase]
7. **[WordPress]** ブレードで法律条項に同意し、**[作成]** をクリックします。
   
    ![Finish the web app settings and click OK][ConfigFinished]
   
    Azure App Service によって、通常は&1; 分以内に Web アプリが作成されます。 進捗状況を監視するには、ポータル ページの上部にあるベル アイコンをクリックします。
   
    ![進捗状況インジケーター][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>WordPress Web アプリの起動と管理
1. Web アプリの作成が完了したら、Azure ポータルで、アプリケーションを作成したリソース グループに移動し、Web アプリとデータベースを確認できます。
   
    電球のアイコンが表示された追加のリソースは [Application Insights][ApplicationInsights] であり、Web アプリの監視サービスを提供します。
2. **[リソース グループ]** ブレードで、Web アプリの行をクリックします。
   
    ![Select your WordPress web app][WordPressSelect]
3. Web アプリ ブレードで **[参照]**をクリックします。
   
    ![Browse to your WordPress web app][WordPressBrowse]
4. WordPress ブログ用の言語を選択するように求めるメッセージが表示されたら、使用する言語を選択し、 **[続行]**をクリックします。
   
    ![Configure the language for your WordPress web app][WordPressLanguage]
5. WordPress の **[ようこそ]** ページで、WordPress に必要な構成情報を入力し、**[WordPress をインストール]** をクリックします。
   
    ![Configure the settings your WordPress web app][WordPressConfigure]
6. **[ようこそ]** ページで作成した資格情報を使用して、ログインします。  
7. サイトのダッシュボード ページが開き、入力した情報が表示されます。    
   
    ![View your WordPress dashboard][WordPressDashboard2]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Marketplace から Web アプリの例を作成してデプロイする方法を説明しました。

App Service Web Apps の使用方法の詳細については、ページの左側 (ワイド ブラウザー ウィンドウの場合) またはページの上部 (幅の狭いブラウザー ウィンドウの場合) に表示されるリンクを参照してください。

Azure での WordPress Web アプリの開発の詳細については、「[Azure App Service での WordPress の開発][WordPressOnAzure]」をご覧ください。

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[App Service の試用]: https://azure.microsoft.com/try/app-service/
[ResourceGroups]: ../azure-resource-manager/resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Azure ポータル]: https://portal.azure.com/
[Project Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png

