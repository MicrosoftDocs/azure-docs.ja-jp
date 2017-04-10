---
title: "Visual Studio を使用した Azure への ASP.NET アプリのデプロイ | Microsoft Docs"
description: "Visual Studio を使用して ASP.NET Web プロジェクトを Azure App Service の新しい Web アプリにデプロイする方法について説明します。"
services: app-service\web
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: 69759e3c-384c-4afb-9278-db6724f6cb74
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 93107d19e1814b30d69fc12c92b544d9760bda6a
ms.lasthandoff: 04/04/2017


---
# <a name="deploy-an-aspnet-web-app-to-azure-app-service-using-visual-studio"></a>Visual Studio を使用した Azure App Service への ASP.NET Web アプリのデプロイ
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Visual Studio 2015 を使用して ASP.NET Web アプリケーションを [Azure App Service の Web アプリ](app-service-web-overview.md) にデプロイする方法を紹介します。

このチュートリアルは、Azure を使用した経験がない ASP.NET 開発者を対象に作成されています。 完了すると、単純な Web アプリケーションがクラウドで稼働している状態になります。

学習内容:

* Visual Studio で新しい Web プロジェクトを作成するときに新しい App Service Web アプリを作成する方法。
* Visual Studio を使用して、App Service Web アプリに WEB プロジェクトをデプロイする方法。

次の図は、チュートリアルでの作業内容を示しています。

![Visual Studio での作成とデプロイの図](./media/web-sites-dotnet-get-started/Create_App.png)

このチュートリアルの最後の「[トラブルシューティング](#troubleshooting)」セクションでは、うまくいかない場合の対処方法を説明します。また、「[次のステップ](#next-steps)」セクションには、Azure App Service の使い方をさらに学習できる他のチュートリアルへのリンクがあります。

これは入門チュートリアルであるため、デプロイ方法が示されている Web プロジェクトは単純なもので、データベースを使用せず、認証や承認を行いません。 より高度なデプロイのトピックへのリンクについては、 [Azure Web アプリのデプロイ方法](web-sites-deploy.md)に関するページを参照してください。

Azure SDK for .NET のインストールに必要な時間は別にして、このチュートリアルを完了するには、10 ～ 15 分ほどかかります。

## <a name="prerequisites"></a>前提条件
* このチュートリアルは、ASP.NET MVC と Visual Studio での作業経験があることを前提としています。 概要説明が必要な場合は、「 [Getting Started with ASP.NET MVC 5 (ASP.NET MVC 5 での作業の開始)](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started)」を参照してください。
* Azure アカウントが必要です。 [無料の Azure アカウントを作成する](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)か、[Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)ことができます。 
  
    Azure アカウントにサインアップする前に Azure App Service を開始する場合は、[App Service の試用](https://azure.microsoft.com/try/app-service/)に関するページにアクセスしてください。 有効期間が短いスターター アプリを App Service で作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## <a name="setupdevenv"></a>開発環境の設定
このチュートリアルは、Visual Studio 2015 と [Azure SDK for .NET](../dotnet-sdk.md) 2.9 以降向けに書かれています。 

* [最新の Azure SDK for Visual Studio 2015 はここからダウンロードしてください](http://go.microsoft.com/fwlink/?linkid=518003)。 Visual Studio 2015 をまだインストールしていない場合は、SDK によってインストールされます。
  
  > [!NOTE]
  > マシンに既にある SDK の依存関係の数に応じて、SDK のインストールには長時間 (数分から 30 分以上) かかる場合があります。
  > 
  > 

Visual Studio 2013 を持っていて、それを使用する場合は、 [最新の Azure SDK for Visual Studio 2013 をダウンロード](http://go.microsoft.com/fwlink/?LinkID=324322)することができます。 一部の画面は画像と異なる場合があります。

## <a name="create-a-web-application"></a>Web アプリケーションの作成
次の手順では、Visual Studio で Web アプリケーション プロジェクトを作成し、Azure App Service で Web アプリを作成します。 チュートリアルのこのセクションでは、新しい Web プロジェクトを構成します。 

1. Visual Studio 2015 を開きます。
2. **[ファイル]、[新規作成]、[プロジェクト]** の順にクリックします。
3. **[新しいプロジェクト]** ダイアログ ボックスで、**[Visual C#]、[Web]、[ASP.NET Web アプリケーション]** の順にクリックします。
4. ターゲット フレームワークとして **[.NET Framework 4.5.2]** が選択されていることを確認します。
5. [Azure Application Insights](../application-insights/app-insights-overview.md) は、Web アプリの可用性、パフォーマンス、利用状況を監視します。 **[Application Insights をプロジェクトに追加する]** チェック ボックスは、Visual Studio のインストール後に初めて Web プロジェクトを作成するときは、既定でオンになっています。 このチェック ボックスがオンになっているが Application Insights を使う予定がない場合は、オフにしてください。
6. アプリケーションに「**MyExample**」という名前を付けて、**[OK]** をクリックします。
   
    ![New Project dialog box](./media/web-sites-dotnet-get-started/GS13newprojdb.png)
7. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、**[MVC]** テンプレートを選択し、**[認証の変更]** をクリックします。
   
    このチュートリアルでは、ASP.NET MVC Web プロジェクトをデプロイします。 ASP.NET Web API プロジェクトのデプロイ方法については、「 [次のステップ](#next-steps) 」セクションを参照してください。 
   
    ![[新しい ASP.NET プロジェクト] ダイアログ ボックス](./media/web-sites-dotnet-get-started/GS13changeauth.png)
8. **[認証の変更]** ダイアログ ボックスで、**[認証なし]** をクリックし、**[OK]** をクリックします。
   
    ![[認証なし]](./media/web-sites-dotnet-get-started/GS13noauth.png)
   
    この入門チュートリアルでは、ユーザー ログインを行わない、単純なアプリをデプロイします。
9. **[New ASP.NET Project (新しい ASP.NET プロジェクト)]** ダイアログ ボックスの **[Microsoft Azure]** セクションで、**[クラウドにホストする]** がオンになっていることと、ドロップダウン リストで **[App Service]** が選択されていることを確認します。
   
    ![[新しい ASP.NET プロジェクト] ダイアログ ボックス](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)
   
    このように設定すると、Visual Studio によってこの Web プロジェクトの Azure Web アプリが自動的に作成されます。
10. **[OK]**

## <a name="create-the-azure-resources"></a>Azure リソースの作成
ここでは、作成する Azure リソースについて、Visual Studio に指示します。

1. **[App Service の作成]** ダイアログで **[アカウントの追加]** をクリックし、Azure サブスクリプションの管理に使用するアカウントの ID とパスワードで Azure にサインインします。
   
    ![Azure へのサインイン](./media/web-sites-dotnet-get-started/configuresitesettings.png)
   
    同じコンピューターに既にサインインしている場合は、 **[アカウントの追加]** ボタンが表示されないことがあります。 その場合は、この手順を省略できることもありますが、資格情報の再入力が必要なこともあります。
2. **azurewebsites.net** ドメインに固有の *Web アプリの名前* を入力します。 たとえば、MyExample という名前の右側に番号を付加して一意にすることができます (例: MyExample810)。 既定の Web 名が作成される場合は、それが一意になるため、そのまま使用することができます。
   
    入力した名前が既に使用されている場合は、右側に緑色のチェック マークではなく赤色の感嘆符が表示されます。この場合、別の名前を入力する必要があります。
   
    アプリケーションの URL は、この名前に *.azurewebsites.net*を追加したものです。 たとえば、名前が `MyExample810` である場合、URL は `myexample810.azurewebsites.net` になります。
   
    また、Azure Web アプリでカスタム ドメインを使用することもできます。 詳細については、「 [Azure App Service のカスタム ドメイン名の構成](web-sites-custom-domain-name.md)」を参照してください。
3. **[リソース グループ]** ボックスの横にある **[新規]** をクリックし、「MyExample」(またはお好きな名前) を入力します。 
   
    ![[App Service の作成] ダイアログ](./media/web-sites-dotnet-get-started/rgcreate.png)
   
    リソース グループは、Web アプリ、データベース、VM などの Azure リソースをひとまとめにしたものです。 チュートリアルを行うときは、新しいリソース グループを作成することをお勧めします。このようにすると、チュートリアルのために作成した Azure リソースをまとめて簡単に削除できるためです。 詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。
4. **[App Service プラン]** ドロップダウンの隣にある **[新規]** ボタンをクリックします。
   
    ![[App Service の作成] ダイアログ](./media/web-sites-dotnet-get-started/createasplan.png)
   
    **[App Service プランの構成]** ダイアログが表示されます。
   
    ![[App Service の構成] ダイアログ](./media/web-sites-dotnet-get-started/configasp.png)
   
    以降の手順では、新しいリソース グループの App Service プランを構成します。 App Service プランでは、Web アプリの実行環境となるコンピューティング リソースを指定します。 たとえば、Free レベルを選択した場合、API アプリは共有 VM 上で実行され、一部の有料レベルを選択した場合は専用 VM で実行されます。 詳細については、「 [Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。
5. **[App Service プランの構成]** ダイアログに「MyExamplePlan」(またはお好きな名前) を入力します。
6. **[場所]** ドロップダウン リストで、現在の所在地に最も近い場所を選択します。
   
    この設定によって、アプリが実行される Azure データ センターが指定されます。 このチュートリアルでは、任意のリージョンを選択することができます。任意のリージョンを選択しても、大きな違いはありません。 ただし運用アプリでは、[待機時間](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)を最小限に抑えるために、アクセスするクライアントに最も近いサーバーを選択することをお勧めします。
7. **[サイズ]** ドロップダウンで、**[Free]** をクリックします。
   
    このチュートリアルでは、無料の価格レベルで十分に良好な性能が提供されます。
8. **[App Service プランの構成]** ダイアログで、**[OK]** をクリックします。
9. **[App Service の作成]** ダイアログ ボックスで、**[作成]** をクリックします。

## <a name="inspect-the-azure-resources-in-visual-studio"></a>Visual Studio での Azure リソースの確認
ごく短時間で (通常は 1 分未満)、Visual Studio によって Web プロジェクトと Web アプリが作成されます。  

**[ソリューション エクスプローラー]** ウィンドウには、新しいプロジェクトのファイルとフォルダーが表示されます

![[ソリューション エクスプローラー]](./media/web-sites-dotnet-get-started/solutionexplorer.png)

**[Azure App Service のアクティビティ]** ウィンドウでは、Azure に App Service リソースが作成されたことが示されます。 ここでリンクをクリックすると、新しいプロジェクトの発行をすぐに開始できます。 ただし後ほど、このチュートリアルでは、いつでもファイルを発行できる方法を説明します。

![[Azure App Service アクティビティ] ウィンドウで作成された Web アプリ](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

**[Cloud Explorer]** ウィンドウでは、作成したばかりの新しい Web アプリも含めて、Azure のリソースを表示したり管理したりできます。

![Cloud Explorer で作成された Web アプリ](./media/web-sites-dotnet-get-started/siteinse.png)

## <a name="deploy-the-web-project-to-azure"></a>Azure への Web プロジェクトのデプロイ
このセクションでは、Azure App Service で作成した Web アプリのリソースに Web プロジェクトをデプロイします。

1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[発行]** を選択します。
   
    ![Visual Studio メニューの [発行場所の選択]](./media/web-sites-dotnet-get-started/choosepublish.png)
   
    数秒で、 **Web の発行** ウィザードが表示されます。 このウィザードは、新しい Web アプリに Web プロジェクトをデプロイするための設定が含まれる *発行プロファイル* に対して開かれます。
   
    > [!TIP] 
    > 発行プロファイルにはデプロイ用のユーザー名とパスワードが含まれています。  これらの資格情報は自動的に生成されているので、入力の必要はありません。 パスワードは、 `Properties\PublishProfiles` フォルダー内のユーザー固有の隠しファイルで暗号化されています。
    >
    >
2. **Web の発行**ウィザードの **[接続]** タブで、**[次へ]** をクリックします。
   
    ![Web の発行ウィザードの [接続] タブで [次へ] をクリック](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)
   
    次は **[設定]** タブです。 ここで、ビルド構成を変更して、デバッグ ビルドをデプロイするように設定すると、 [リモート デバッグ](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)ができるようになります。 このタブには他にもいくつかの [ファイル発行オプション](https://msdn.microsoft.com/library/dd465337.aspx#Anchor_2)があります。
3. **[設定]** タブの **[次へ]** をクリックします。
   
   ![Web の発行ウィザードの [設定] タブ](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)
   
   次は **[プレビュー]** タブです。 ここで、どのファイルがプロジェクトから API アプリにコピーされるかを確認できます。 既にデプロイ済みの API アプリにプロジェクトをデプロイした場合は、変更されたファイルだけがコピーされます。 コピーされるファイルの一覧を確認するには、 **[プレビューの開始]** ボタンをクリックしてください。
4. **[プレビュー]** タブで、**[発行]** をクリックします。
   
   ![Web の発行ウィザードの [プレビュー] タブ](./media/web-sites-dotnet-get-started/GS13previewoutput.png)
   
   **[発行]**をクリックすると、Visual Studio で Azure サーバーにファイルをコピーする処理が開始されます。 これには 1 ～ 2 分ほどかかることがあります。
   
   **[出力]** ウィンドウと **[Azure App Service アクティビティ]** ウィンドウでは、実行されたデプロイ操作が表示され、デプロイが問題なく完了したことが報告されます。
   
   ![デプロイが正常に完了したことを示す Visual Studio の出力ウィンドウ](./media/web-sites-dotnet-get-started/PublishOutput.png)
   
   デプロイが成功すると、自動的に既定のブラウザーが開き、デプロイ先の Web アプリの URL にアクセスします。これで、作成したアプリケーションはクラウドで実行されています。 ブラウザー アドレス バーの URL は、インターネットから読み込まれた Web アプリを示します。
   
   ![Azure で実行されている Web アプリ](./media/web-sites-dotnet-get-started/GS13deployedsite.png)
   
   > [!TIP]
   > **[Web の 1 クリック発行]** ツール バーを有効にすると、簡単にデプロイできるようになります。 **[表示]、[ツール バー]** の順にクリックし、**[Web の 1 クリック発行]** を選択します。 このツール バーを使用すると、プロファイルを選択できるほか、ボタンをクリックして発行したり、ボタンをクリックして **Web の発行** ウィザードを開いたりできます。
   > ![[Web の 1 クリック発行] ツール バー](./media/web-sites-dotnet-get-started/weboneclickpublish.png)
   > 
   > 

## <a name="troubleshooting"></a>トラブルシューティング
このチュートリアルの手順を行う際に問題が発生した場合は、必ず最新バージョンの Azure SDK for .NET を使用するようにしてください。 そのための最も簡単な方法は、 [Azure SDK for Visual Studio 2015 をダウンロード](http://go.microsoft.com/fwlink/?linkid=518003)することです。 最新バージョンをインストール済みの場合は、Web Platform Installer によってインストールが不要であることが示されます。

ASP.NET Web アプリが Azure App Service で動作するようになったら、トラブルシューティングを容易にする Visual Studio の機能についてさらに学習できます。 ログ記録、リモート デバッグなどの詳細については、[Visual Studio での Azure Web アプリのトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure の Web アプリに単純な Web アプリケーションを作成してデプロイする方法について説明しました。 次に示すのは、Azure App Service についてさらに学習するための関連トピックとリソースです。

* [Azure ポータル](https://portal.azure.com/)で Web アプリを監視および管理する 
  
    詳細については、[Azure Portal の概要](/services/management-portal/)に関するページと、「[Azure App Service での Web アプリの構成](web-sites-configure.md)」を参照してください。
* Visual Studio を使用して、既存の Web プロジェクトを新しい Web アプリにデプロイする
  
    **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[発行]** をクリックします。 発行先として **[Microsoft Azure App Service]** を選択し、**[新規]** をクリックします。 各ダイアログ ボックスは、このチュートリアルで表示されたものと同じです。
* ソース管理から Web プロジェクトをデプロイする
  
    [ソース管理システム](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)からの[デプロイの自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)の詳細については、[Azure App Service での Web アプリの使用](app-service-web-get-started.md)に関するページと [Azure Web アプリのデプロイ方法](web-sites-deploy.md)に関するページを参照してください。
* Azure App Service の API アプリに ASP.NET Web API をデプロイする
  
    ここでは、主に Web サイトのホストを目的とする Azure App Service のインスタンスを作成する方法を学習しました。 App Service では、クライアント コード生成のための CORS サポートや API メタデータ サポートなど、Web API をホストするための機能も提供されます。 API 機能は Web アプリで使用できますが、主に App Service のインスタンスで API をホストする場合は、 **API アプリ** の方が適しています。 詳細については、「 [Azure App Service で API Apps と ASP.NET を使用する](../app-service-api/app-service-api-dotnet-get-started.md)」を参照してください。 
* カスタム ドメイン名と SSL を追加する
  
    SSL および独自のドメイン (contoso.azurewebsites.net の代わりに www.contoso.com など) の使用方法については、次のリソースを参照してください。
  
  * [Azure App Service のカスタム ドメイン名の構成](web-sites-custom-domain-name.md)
  * [Azure の Web サイトでの HTTPS の有効化](web-sites-configure-ssl-certificate.md)
* Web アプリを含むリソース グループと、関連するすべての Azure リソースは、作業が完了したら削除します。
  
    Azure ポータルでリソース グループを操作する方法については、「 [Resource Manager テンプレートと Azure ポータルを使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-portal.md)」を参照してください。   
* App Service で ASP.NET Web アプリを作成するその他の例については、[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect の[デモ](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)の「[Create and deploy an ASP.NET web app in Azure App Service (Azure App Service で ASP.NET Web アプリを作成およびデプロイする)](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service)」と「[Create and deploy a mobile app in Azure App Service (Azure App Service でモバイル アプリを作成してデプロイする)](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-a-mobile-app-in-Azure-App-Service)」を参照してください。 HealthClinic.biz のデモに関連する他のクイック スタートについては、「 [Azure Developer Tools Quickstarts (Azure 開発者ツールのクイック スタート)](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)」を参照してください。


