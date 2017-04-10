---
title: "5 分間で Azure Portal で WordPress アプリをデプロイする | Microsoft Docs"
description: "WordPress アプリをデプロイして、App Service での Web アプリの実行がいかに簡単であるかを説明します。 結果はすぐにわかります。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/13/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7ef5802866bf96859d3f44cdb58cbb412b08a700
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-a-wordpress-app-in-the-azure-portal-in-five-minutes"></a>5 分間で Azure Portal で WordPress アプリをデプロイする

このチュートリアルでは、初めての [WordPress](https://wordpress.org/) Web アプリを [Azure App Service](../app-service/app-service-value-prop-what-is.md) に数分間でデプロイする方法を示します。

![WordPress サイト](./media/app-service-web-get-started-php-portal/wpdashboard.png)

## <a name="prerequisites"></a>前提条件
Microsoft Azure のアカウントが必要です。 アカウントを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)するか [Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。

> [!NOTE]
> Azure アカウントがなくても、[App Service を試用](https://azure.microsoft.com/try/app-service/)できます。 スターター アプリを作成し、最大&1; 時間使用できます。クレジット カードも契約も不要です。
> 
> 

## <a name="deploy-the-wordpress-app"></a>WordPress アプリをデプロイする
1. [Azure Portal](https://portal.azure.com) にサインインします。

2. [https://portal.azure.com/#create/WordPress.WordPress](https://portal.azure.com/#create/WordPress.WordPress) を開きます。

    このリンクは、Azure Portal で新しい WordPress アプリをすぐに構成するためのショートカットです。

3. **[アプリ名]** に Web アプリ名を入力します。 この名前が `azurewebsites.net` ドメイン内で一意の場合は、ボックスに緑色のチェックマークが表示されます。
   
5. **[リソース グループ]** で **[新規作成]** をクリックして新しい[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成し、名前を指定します。

6. **[データベース プロバイダー]** で **[CleaDB]** を選択します。

7. **[App Service プラン/場所]** > **[新規作成]** の順にクリックします。 [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を次のように構成します。

    - **[App Service プラン]** に希望する名前を入力します。
    - **[場所]** で、プランをホストする場所を選択します。
    - **[価格レベル]** をクリックし、**[F1 Free]** または別の適切なレベルを選択してから、**[選択]** をクリックします。
    - **[OK]**をクリックします。

8. **[データベース]** > **[新規作成]** の順にクリックします。 SQL Database を次のように構成します。

    - **[データベース名]** にデータベース名を入力します。 
    - **[場所]** で、App Service プランと同じ場所を選択します。
    - **[価格レベル]** をクリックし、**[Mercury]** または別の適切なレベルを選択してから、**[選択]** をクリックします。
    - **[法律条項]** をクリックし、**[購入]** をクリックします。
    - **[OK]**をクリックします。

9. **[作成]**をクリックします。

    ここで、構成に基づいて Azure で WordPress アプリが作成されます。 "**デプロイが開始されました...**" という通知が表示されます。

    ![デプロイの開始 - Azure App Service での初めての WordPress](./media/app-service-web-get-started-php-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-wordpress-web-app"></a>WordPress Web アプリの起動と管理

Azure でアプリのデプロイが完了すると、別の通知が表示されます。

![デプロイの成功 - Azure App Service での初めての WordPress](./media/app-service-web-get-started-php-portal/deployment-succeeded.png)

1. 通知をクリックします。 見逃した場合は、通知ベル (![通知ベル - Azure App Service での初めての WordPress](./media/app-service-web-get-started-dotnet-portal/notification.png)) をクリックするといつでもアクセスできます。

    Web アプリの管理[ブレード](../azure-resource-manager/resource-group-portal.md#manage-resources) ("*ブレード*": 水平方向に表示されるポータル ページが) が表示されます。

3. [概要] ページの上部にある **[参照]** をクリックします。
   
    ![参照 - Azure App Service での初めての WordPress](./media/app-service-web-get-started-php-portal/browse.png)

    WordPress の**ようこそ**ページが表示されます。 WordPress のインストールを構成して使ってみましょう。

    ![WordPress の構成 - Azure App Service での初めての WordPress](./media/app-service-web-get-started-php-portal/wordpress-config.png)
    
## <a name="next-steps"></a>次のステップ
* [Laravel Web アプリの作成および構成して Azure にデプロイする](app-service-web-php-get-started.md) - Azure で PHP Web アプリを実行するために必要な、以下のような基本的なスキルについて学習します。

    * PowerShell/Bash から Azure でアプリを作成および構成する。
    * PHP バージョンを設定する。
    * ルート アプリケーション ディレクトリに存在しないスタート ファイルを使用する。
    * Composer オートメーションを有効にする。
    * 環境固有の変数にアクセスする。
    * 一般的なエラーのトラブルシューティング。

* [Azure App Service にコードをデプロイする](web-sites-deploy.md) - FTP やソース管理リポジトリからデプロイする方法について学習します。
* [初めての Web アプリに機能を追加する](app-service-web-get-started-2.md) - Azure アプリを次のレベルに進めます。 ユーザーを認証します。 必要に応じてスケールを変更したり、 パフォーマンスのアラートを設定したりできます。 いずれも、数回のクリックで実現できます。

