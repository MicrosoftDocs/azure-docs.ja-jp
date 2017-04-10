---
title: "Azure App Service のハイブリッド接続を使用してオンプレミスのリソースにアクセスする"
description: "静的 TCP ポートを使用するオンプレミスのリソースと Azure App Service の Web アプリケーションの間の接続を作成します。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: a46ed26b-df8e-4fc3-8e05-2d002a6ee508
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: cb656dd952e76ee29e102fff531d34a45e4630e4
ms.lasthandoff: 02/16/2017


---
# <a name="access-on-premises-resources-using-hybrid-connections-in-azure-app-service"></a>Azure App Service のハイブリッド接続を使用してオンプレミスのリソースにアクセスする
SQL Server、MySQL、HTTP Web API、ほとんどのカスタム Web サービスなど、静的 TCP ポートを使用するすべてのオンプレミス リソースに、Azure App Service アプリを接続できます。 ここでは、App Service とオンプレミスの SQL Server データベース間のハイブリッド接続の作成方法を示します。

> [!NOTE]
> ハイブリッド接続機能の Web Apps 部分は、 [Azure ポータル](https://portal.azure.com)でのみ使用できます。 BizTalk Services で接続を作成するには、「 [Hybrid Connections (ハイブリッド接続)](http://go.microsoft.com/fwlink/p/?LinkID=397274)」を参照してください。 
> 
> このコンテンツは、Azure App Service の Mobile Apps にも適用されます。 
> 
> 

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 無料サブスクリプションについては、「 [1 か月間無料評価版](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。 
  
    Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
* ハイブリッド接続でオンプレミスの SQL Server または SQL Server Express のデータベースを使用するには、TCP/IP が静的ポートで有効になっている必要があります。 SQL Server は静的ポート 1433 を使用するため、SQL Server で既定のインスタンスを使用することをお勧めします。 ハイブリッド接続で使用するための SQL Server Express のインストールと構成については、「 [ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する](http://go.microsoft.com/fwlink/?LinkID=397979)」をご覧ください。
* 後でこの記事で説明するオンプレミスの Hybrid Connection Manager のエージェントをインストールするコンピューターの条件は次のとおりです。
  
  * ポート 5671 で Azure に接続できること
  * オンプレミスのリソースの *hostname*で、次の手順を実行します。*portnumber* に到達できること 

> [!NOTE]
> この記事の手順では、オンプレミスのハイブリッド接続のエージェントをホストするコンピューターからブラウザーを使用していると想定しています。
> 
> 

## <a name="create-a-web-app-in-the-azure-portal"></a>Azure ポータルで Web アプリを作成する
> [!NOTE]
> このチュートリアルで使用する Web アプリやモバイル アプリ バックエンドを Azure ポータルで既に作成している場合は、この手順をスキップし、「 [ハイブリッド接続および BizTalk サービスを作成する](#CreateHC) 」から開始してください。
> 
> 

1. [Azure Portal](https://portal.azure.com) の左上隅の **[新規]**  >  **[Web + モバイル]**  >  **[Web アプリ]** をクリックします。
   
    ![新しい Web アプリ][NewWebsite]
2. **[Web アプリ]** ブレードで、URL を入力し、**[作成]** をクリックします。 
   
    ![Web サイト名][WebsiteCreationBlade]
3. しばらくすると、Web アプリケーションが作成され、Web アプリケーションのブレードが表示されます。 ブレードは縦方向にスクロールできるダッシュボードで、サイトを管理することができます。
   
    ![Web サイト実行][WebSiteRunningBlade]
4. サイトがライブかどうかを確認するには、 **[参照]** アイコンをクリックして、既定のページを表示します。
   
    ![[参照] をクリックして Web アプリを確認する][Browse]
   
    ![既定の Web アプリ ページ][DefaultWebSitePage]

次に、Web アプリケーションに対するハイブリッド接続と BizTalk サービスを作成します。

<a name="CreateHC"></a>

## <a name="create-a-hybrid-connection-and-a-biztalk-service"></a>ハイブリッド接続および BizTalk サービスを作成する
1. Web アプリ ブレードで、**[すべての設定]**  >  **[ネットワーク]**  >  **[ハイブリッド接続エンドポイントを構成する]** をクリックします。
   
    ![Hybrid Connections (ハイブリッド接続)][CreateHCHCIcon]
2. ハイブリッド接続ブレードで、 **[追加]**をクリックします。
   
    <!-- ![Add a hybrid connnection][CreateHCAddHC]
   -->
3. **[ハイブリッド接続の追加]** ブレードが開きます。  これは最初のハイブリッド接続であるため、**[新しいハイブリッド接続]** があらかじめ選択され、**[ハイブリッド接続の作成]** ブレードが開きます。
   
    ![ハイブリッド接続の追加][TwinCreateHCBlades]
   
    **[ハイブリッド接続の作成] ブレード**で、次の手順を実行します。
   
   * **[名前]**に、接続の名前を入力します。
   * **[ホスト名]**に、リソースをホストする内部設置型のコンピューターの名前を入力します。
   * **[ポート]**には、内部設置型のリソースが使用するポート番号 (SQL Server の既定のインスタンスの場合は&1433;) を入力します。
   * **[BizTalk サービス]**
4. **[BizTalk サービスの作成]** ブレードが開きます。 BizTalk サービスの名前を入力し、 **[OK]**をクリックします。
   
    ![[BizTalk サービスの作成]][CreateHCCreateBTS]
   
    **[BizTalk サービスの作成]** ブレードが閉じ、**[ハイブリッド接続の作成]** ブレードに戻ります。
5. [ハイブリッド接続の作成] ブレードで、 **[OK]**をクリックします。 
   
    ![[OK] をクリック][CreateBTScomplete]
6. 処理が完了すると、ポータルの通知領域に接続の作成が完了したことが通知されます。
   
    <!--- TODO
   
    この手順ではすべてが失敗します。 I can't create a BizTalk service in the dogfood portal. クラシック ポータル (完全なポータル) に切り替えて BizTalk サービスを作成しましたが、接続できないようです。ハイブリッド接続の作成手順を完了すると、"ハイブリッド接続 RelecIoudHC を作成できませんでした。 名前空間 'Microsoft.BizTaIkServices for api version 2014-06-01' でこのリソースの種類が見つかりませんでした" というエラーが表示されます。
   
    The error indicates it couldn't find the type, not the instance.
    ![Success notification][CreateHCSuccessNotification]
    -->
7. Web アプリのブレードの **[ハイブリッド接続]** アイコンは、ハイブリッド接続が 1 つ作成されたことを示しています。
   
    ![1 つのハイブリッド接続が作成された][CreateHCOneConnectionCreated]

これで、クラウド ハイブリッド接続のインフラストラクチャの重要な部分が完了しました。 次に、対応するオンプレミスの部分を作成します。

<a name="InstallHCM"></a>

## <a name="install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>オンプレミス ハイブリッド接続マネージャーをインストールして接続を完了する
1. Web アプリのブレードで、**[すべての設定]**  >  **[ネットワーク]**  >  **[ハイブリッド接続エンドポイントを構成する]** をクリックします。 
   
    ![ハイブリッド接続アイコン][HCIcon]
2. **[ハイブリッド接続]** ブレードで、最近追加されたエンドポイントの **[状態]** 列に **[未接続]** と表示されています。 接続をクリックして構成します。
   
    ![[未接続]][NotConnected]
   
    ハイブリッド接続ブレードが開きます。
   
    ![NotConnectedBlade][NotConnectedBlade]
3. ブレードで、 **[リスナーのセットアップ]**をクリックします。
   
    ![[リスナーのセットアップ] をクリック][ClickListenerSetup]
4. **[ハイブリッド接続のプロパティ]** ブレードが開きます。 **オンプレミスの Hybrid Connection Manager** で、**[インストールするにはここをクリックします]** をクリックします。
   
    ![[インストールするにはここをクリックします]][ClickToInstallHCM]
5. [アプリケーションの実行 - セキュリティの警告] ダイアログで、 **[実行]** を選択します。
   
    ![[実行] を選択して続行][ApplicationRunWarning]
6. **[ユーザー アカウント制御]** ダイアログで、**[はい]** を選択します。
   
   ![[はい] を選択][UAC]
7. Hybrid Connection Manager がダウンロードされ、インストールされます。 
   
    ![インストール中][HCMInstalling]
8. インストールが完了したら、 **[閉じる]**をクリックします。
   
    ![[閉じる] をクリックしてください][HCMInstallComplete]
   
    **[ハイブリッド接続]** ブレードには、**[状態]** 列に **[接続済み]** と表示されています。 
   
    ![接続されている状態][HCStatusConnected]

これで、ハイブリッド接続のインフラストラクチャが完成しました。この接続を使用するハイブリッド アプリケーションを作成できます。 

> [!NOTE]
> 以下のセクションでは、Mobile Apps .NET バックエンド プロジェクトでハイブリッド接続を使用する方法を説明します。
> 
> 

## <a name="configure-the-mobile-app-net-backend-project-to-connect-to-the-sql-server-database"></a>SQL Server データベースに接続する Mobile Apps .NET バックエンド プロジェクトを構成する
App Service では、Mobile Apps .NET バックエンド プロジェクトは、追加の Mobile Apps SDK をインストールし初期化した ASP.NET Web アプリにすぎません。 Web アプリを Mobile Apps バックエンドとして使用するには、 [Mobile Apps .NET バックエンド SDK をダウンロードして初期化する](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#install-sdk)必要があります。  

Mobile Apps については、オンプレミスのデータベースの接続文字列を定義し、この接続を使用するようにバックエンドを変更する必要もあります。 

1. Visual Studio のソリューション エクスプローラーで、Mobile Apps .NET バックエンドの Web.config ファイルを開き、**connectionStrings** セクションを探し、次のような新しい SqlClient エントリを追加します。このエントリはオンプレミスの SQL Server データベースを指しています。
   
        <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />
   
    文字列内の `<**secure_password**>` は、 *HybridConnectionLogin* 用に作成したパスワードに置き換えます。
2. Visual Studio で **[保存]** をクリックして、Web.config ファイルを保存します。
   
   > [!NOTE]
   > この接続設定は、ローカル コンピューターで実行されるときに使用されます。 Azure で実行される場合、この設定は、ポータルで定義された接続設定によってオーバーライドされます。
   > 
   > 
3. **Models** フォルダーを展開し、 *Context.cs*で終わるデータ モデル ファイルを開きます。
4. **DbContext** を、値 `OnPremisesDBConnection` をベース **DbContext** コンストラクターに渡すように変更します。次のスニペットに似ています。
   
        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }
   
    これで、サービスは、SQL Server データベースへの新しい接続を使用するようになります。

## <a name="update-the-mobile-app-backend-to-use-the-on-premises-connection-string"></a>オンプレミスの接続文字列を使用するようにモバイル アプリ バックエンドを更新する
次に、この新しい接続文字列用のアプリ設定を追加して、Azure から使用できるようにする必要があります。  

1. [Azure Portal](https://portal.azure.com) に戻り、モバイル アプリ用の Web アプリ バックエンド コードで、**[すべての設定]**、**[アプリケーション設定]** の順にクリックします。
2. **[Web アプリの設定]** ブレードで、下にスクロールして **[接続文字列]** を表示し、`OnPremisesDBConnection` という名前の `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>` などの値が指定された新しい **SQL Server** 接続文字列を追加します。
   
    `<**secure_password**>` を、オンプレミスのデータベースのセキュリティ保護されたパスワードに置き換えます。
   
    ![オンプレミスのデータベースの接続文字列](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)
3. [ **保存** ] をクリックして、今作成したハイブリッド接続と接続文字列を保存します。

この時点で、サーバー プロジェクトを再発行し、既存の Mobile Apps クライアントで新しい接続をテストできます。 データは、ハイブリッド接続を使用して、オンプレミスのデータベースから読み込まれ、このデータベースに書き込まれます。

<a name="NextSteps"></a>

## <a name="next-steps"></a>次のステップ
* ハイブリッド接続を使用する ASP.NET Web アプリケーションの作成の詳細については、「 [Connect to an on-premises SQL Server from an Azure web site using Hybrid Connections (ハイブリッド接続を使用して Azure の Web サイトからオンプレミスの SQL Server に接続する)](http://go.microsoft.com/fwlink/?LinkID=397979)」を参照してください。 

### <a name="additional-resources"></a>その他のリソース
[ハイブリッド接続の概要](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist introduces hybrid connections (Josh Twist によるハイブリッド接続の紹介) (Channel 9 のビデオ)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[ハイブリッド接続の Web サイト](https://azure.microsoft.com/services/biztalk-services/)

[BizTalk サービス: [ダッシュボード]、[監視]、[スケール]、[構成]、および [ハイブリッド接続] タブ](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Building a Real-World Hybrid Cloud with Seamless Application Portability (シームレスなアプリケーションの移植性を使用して実際のハイブリッド クラウドをビルドする) (Channel 9 のビデオ)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (ハイブリッド接続を使用して Azure Mobile Services から内部設置型の SQL Server に接続する) (Channel 9 のビデオ)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

