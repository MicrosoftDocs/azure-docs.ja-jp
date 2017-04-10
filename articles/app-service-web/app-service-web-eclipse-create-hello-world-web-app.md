---
title: "Eclipse を使用した基本的な Azure Web アプリの作成 | Microsoft Docs"
description: "このチュートリアルでは、Azure Toolkit for Eclipse を使用して、Azure 用の Hello World Web アプリを作成する方法について説明します。"
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 9bb0b5fa32cd6dabd6c41106db38a587b0a55eed
ms.openlocfilehash: 18299966173f030f615049eaf11a55a1f71305a0


---
# <a name="create-a-basic-azure-web-app-using-eclipse"></a>Eclipse を使用した基本的な Azure Web アプリの作成
このチュートリアルでは、 [Azure Toolkit for Eclipse]を使用して、Web アプリとして基本的な Hello World アプリケーションを作成し、Azure にデプロイする方法について説明します。 簡潔にするために JSP の例を紹介していますが、Azure のデプロイに関する限り、基本的な Java サーブレットの場合も手順はほぼ同じです。

このチュートリアルを完了し、作成したアプリケーションを Web ブラウザーで開くと、次の図のようになります。

![Hello World アプリのプレビュー][01]

## <a name="prerequisites"></a>前提条件
* Java Developer Kit (JDK) v 1.8 以降。
* Eclipse IDE for Java EE Developers Luna 以降。 <http://www.eclipse.org/downloads/> からダウンロードできます。
* Java ベースの Web サーバーまたはアプリケーション サーバーのディストリビューション ([Apache Tomcat]、[Jetty] など)。
* Azure サブスクリプション。<https://azure.microsoft.com/free/> または <http://azure.microsoft.com/pricing/purchase-options/> から入手できます。
* [Azure Toolkit for Eclipse]。 Azure Toolkit のインストールの詳細については、「[Azure Toolkit for Eclipse のインストール]」を参照してください。

## <a name="to-create-a-hello-world-application"></a>Hello World アプリケーションを作成するには
最初に、Java プロジェクトを作成します。

1. Eclipse を起動し、メニューから **[ファイル]**、**[新規]**、**[Dynamic Web Project (動的 Web プロジェクト)]** の順にクリックします  (**[ファイル]** と **[新規]** のクリック後、使用可能なプロジェクトとして **[Dynamic Web Project (動的 Web プロジェクト)]** が表示されない場合は、**[ファイル]**、**[新規]**、**[プロジェクト]** の順にクリックし、**[Web]** を展開して、**[Dynamic Web Project (動的 Web プロジェクト)]**、**[次へ]** の順にクリックします)。
2. このチュートリアルでは、プロジェクトに **MyWebApp**という名前を付けます。 画面は次のようになります。
   
    ![新しい動的 Web プロジェクトの作成][02]
3. **[完了]**をクリックします。
4. Eclipse の Project Explorer ビューで、 **[MyWebApp]**を展開します。 **WebContent** を右クリックし、**[新規]**、**[JSP ファイル]** の順にクリックします。
5. **[New JSP File (新しい JSP ファイル)]** ダイアログ ボックスで **index.jsp** ファイルに名前を付け、親フォルダーは **MyWebApp/WebContent** のままにして **[次へ]** をクリックします。
6. **[Select JSP Template (JSP テンプレートの選択)]** ダイアログ ボックスで、このチュートリアルのために **[New JSP File (html) (新しい JSP ファイル (html))]** を選択し、**[完了]** をクリックします。
7. index.jsp ファイルが Eclipse で開いたら、"**Hello World!**" を動的に表示するためのテキストを 既存の `<body>` 要素に追加します。 更新された `<body>` コンテンツは、次のようになります。
   
    `<body><b><% out.println("Hello World!"); %></b></body>` 
8. index.jsp を保存します。

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>アプリケーションを Azure Web アプリ コンテナーにデプロイするには
Java Web アプリケーションを Azure にデプロイする方法はいくつかあります。 このチュートリアルでは、アプリケーションを Azure Web アプリ コンテナーにデプロイするという最も簡単な方法について説明します。特殊なプロジェクトの種類や追加のツールは必要ありません。 JDK と Web コンテナー ソフトウェアは Azure から提供されるので、自分でアップロードする必要はありません。必要なものは Java Web アプリのみです。 結果として、アプリケーションの発行プロセスにかかる時間は分単位ではなく、秒単位になります。

1. Eclipse の Project Explorer で **[MyWebApp]**を右クリックします。
2. コンテキスト メニューの **[Azure]**、**[Publish as Azure Web App (Azure Web アプリとして発行)]** の順に選択します。
   
    ![[Publish as Azure Web App (Azure Web アプリとして発行)]][03]
   
    または、Project Explorer で Web アプリケーション プロジェクトが選択されている状態で、ツール バーの **[発行]** ドロップダウン ボタンをクリックし、そこから **[Publish as Azure Web App (Azure Web アプリとして発行)]** を選択します。
   
    ![[Publish as Azure Web App (Azure Web アプリとして発行)]][14]
3. まだ Eclipse から Azure にサインインしていない場合、Azure アカウントにサインインするように求められます。
   
    ![Azure サインイン ダイアログ ボックス][04]
   
    複数の Azure アカウントがある場合、サインイン プロセス中に同じようなプロンプトが何度も表示されることがあります。 このような状況の場合、次のサインイン手順を続行します。
4. Azure アカウントに正常にサインインすると、 **[サブスクリプションの管理]** ダイアログ ボックスに、資格情報に関連付けられたサブスクリプションの一覧が表示されます。 複数のサブスクリプションが表示された場合、その一部のみを使用するには、使用しないサブスクリプションのチェックボックスを必要に応じてオフにします。 サブスクリプションを選択したら、 **[閉じる]**をクリックします。
   
    ![[サブスクリプションの管理] ダイアログ ボックス][05]
5. **[Deploy to Azure Web App Container (Azure Web アプリ コンテナーにデプロイ)]** ダイアログ ボックスを開くと、以前に作成した Web アプリ コンテナーがすべて表示されます。コンテナーを作成していない場合、一覧は空欄です。
   
    ![[Deploy to Azure Web App Container (Azure Web アプリ コンテナーにデプロイ)] ダイアログ ボックス][06]
6. 以前に Azure Web アプリ コンテナーを作成していない場合、またはアプリケーションを新しいコンテナーに発行する場合は、次の手順を実行します。 作成済みの場合は、既存の Web アプリ コンテナーを選択し、以下の手順 7 に進みます。
   
   1. **[New (新規)]**
      
       ![[Deploy to Azure Web App Container (Azure Web アプリ コンテナーにデプロイ)] ダイアログ ボックス][15]
   2. **[New Web App Container (新しい Web アプリ コンテナー)]** ダイアログ ボックスが表示されます。
      
       ![[New Web App Container (新しい Web アプリ コンテナー)] ダイアログ ボックス][07a]
   3. Web アプリ コンテナーの **[DNS Label (DNS ラベル)]** を入力します。これで、Azure の Web アプリケーションについて、ホスト URL のリーフ DNS ラベルが構成されます。 (この名前は使用可能であり、Azure Web アプリの名前付け要件に準拠している必要があります。)
   4. **[Web Container (Web コンテナー)]** ドロップダウン メニューで、アプリケーションに適したソフトウェアを選択します。
      
       現在、Tomcat 8、Tomcat 7 または Jetty 9 から選択することができます。 選択したソフトウェアの最新の配信が Azure で提供されます。また、これは Oracle によって作成され、Azure で提供された JDK 8 の最新の配信で実行されます。
   5. **[サブスクリプション]** ドロップダウン メニューで、このデプロイに使用するサブスクリプションを選択します。
   6. **[Resource Group (リソース グループ)]** ドロップダウン メニューで、Web アプリに関連付けるリソース グループを選択します。 (Azure リソース グループを使用すると、関連リソースをグループ化できるため、たとえば、リソースをまとめて削除できます。)
      
       (所有している場合は) 既存のリソース グループを選択して、下記のステップ g にスキップするか、以下のステップに従って、新しいリソース グループを作成します。
      
      * **[New (新規)]**
      * **[New Resource Group (新しいリソース グループ)]** ダイアログ ボックスが表示されます。
        
          ![[New Resource Group (新しいリソース グループ)] ダイアログ ボックス][08]
      * **[Name (名前)]** テキスト ボックスに、新しいリソース グループの名前を入力します。
      * **[Region (リージョン)]** ドロップダウン メニューで、リソース グループに適した Azure データ センターの場所を選択します。
      * オプション: 既定では、Java 8 の最新の配布は、Azure によって Web アプリ コンテナーに JVM として自動的にデプロイされます。 ただし、Web アプリで必要な場合は、JVM の別のバージョンと配布を指定できます。 Web アプリの JDK を指定するには、 **[JDK]** タブをクリックし、次のオプションのいずれかを選択します。
        
        * **[Deploy the default JDK offered by Azure Web Apps service (Azure Web Apps サービスによって提供される既定の JDK をデプロイする)]**: Java 8 の最新の配布がデプロイされます。
        * **[Deploy a 3rd party JDK available on Azure (Azure で利用できるサード パーティの JDK をデプロイする)]**: Microsoft Azure によって提供される JDK のリストから選択できます。
        * **[Deploy my own JDK from this download location (このダウンロード場所から独自の JDK をデプロイする)]**: 独自の JDK 配布を指定できます。JDK 配布は、ZIP ファイルとしてパッケージ化し、公開されているダウンロード場所またはアクセスできる Azure Storage アカウントにアップロードする必要があります。
          
          ![[New Web App Container (新しい Web アプリ コンテナー)] ダイアログ ボックス][07b]
   7. **[OK]**をクリックします。
   8. **[App Service Plan (App Service プラン)]** ドロップダウン メニューには、選択したリソース グループに関連付けられた App Service プランが表示されます。 (App Service プランでは、Web アプリの場所、価格レベル、コンピューティング インスタンス サイズなどの情報を指定します。 単一の App Service プランを複数の Web Apps に使用できます。そのため、App Service プランは、特定の Web アプリのデプロイとは別に保持されます。)
      
       (所有している場合は) 既存の App Service プランを選択して、下記のステップ h にスキップするか、以下のステップに従って、新しい App Service プランを作成します。
      
      * **[New (新規)]**
      * **[New App Service Plan (新しい App Service プラン)]** ダイアログ ボックスが表示されます。
        
          ![[新しい App Service プラン] ダイアログ ボックス][09]
      * **[Name (名前)]** ボックスに、新しい App Service プランの名前を入力します。
      * **[Location (場所)]** ドロップダウン メニューで、プランに適した Azure データ センターの場所を選択します。
      * **[Pricing Tier (価格レベル)]** ドロップダウン メニューで、プランに適した価格を選択します。 テスト目的の場合は、 **[Free]**を選択できます。
      * **[Instance Size (インスタンス サイズ)]** ドロップダウン メニューで、プランに適したインスタンス サイズを選択します。 テスト目的の場合は、 **[Small]**を選択できます。
   9. これらの手順をすべて完了すると、[New Web App Container] \(新しい Web アプリ コンテナー) ダイアログ ボックスは次の図のようになります。
      
       ![[New Web App Container (新しい Web アプリ コンテナー)] ダイアログ ボックス][10]
   10. **[OK]** をクリックすると、新しい Web アプリ コンテナーの作成が完了します。
       
        数秒待つと Web アプリ コンテナーの一覧が更新されます。一覧で新しく作成した Web アプリ コンテナーが選択されています。
7. 以上で、初めて Web アプリを Azure にデプロイする処理を完了できます。
   
    ![[Deploy to Azure Web App Container (Azure Web アプリ コンテナーにデプロイ)] ダイアログ ボックス][11]
   
    **[OK]** をクリックして、Java アプリケーションを選択した Web アプリ コンテナーにデプロイします。
   
    既定では、アプリケーションはアプリケーション サーバーのサブディレクトリとしてデプロイされます。 ルート アプリケーションとしてデプロイする場合、**[Deploy to root (ルートにデプロイ)]** チェック ボックスをオンにして **[OK]** をクリックします。
8. **[Azure Activity Log (Azure アクティビティ ログ)]** ビューが開き、Web アプリのデプロイの状態が表示されます。
   
    ![[Azure Activity Log (Azure アクティビティ ログ)]][12]
   
    Web アプリを Azure にデプロイするプロセスは、わずか数秒で完了します。 アプリケーションの準備ができると、 **[Published (発行済み)]** in the **[Published (発行済み)]** というリンクが表示されます。 リンクをクリックすると、デプロイした Web アプリのホーム ページが表示されます。

## <a name="updating-your-web-app"></a>Web アプリの更新
既存の実行中の Azure Web アプリを更新するプロセスは短時間で簡単です。更新には&2; つのオプションがあります。

* 既存の Java Web アプリのデプロイを更新できます。
* 同じ Web アプリ コンテナーに追加の Java アプリケーションを発行できます。

いずれの場合でもプロセスは同じで、かかる時間は数秒です。

1. Eclipse の Project Explorer で、更新する Java アプリケーションを右クリックするか、既存の Web アプリ コンテナーに追加します。
2. コンテキスト メニューが表示されたら、**[Azure]**、**[Publish as Azure Web App (Azure Web アプリとして発行)]** の順に選択します。
3. 既にログインしているので、既存の Web アプリ コンテナーの一覧が表示されます。 Java アプリケーションを発行または再発行するコンテナーを選択し、 **[OK]**をクリックします。

数秒後、**[Azure Activity Log (Azure アクティビティ ログ)]** ビューに更新されたデプロイが **[Published (発行済み)]** と表示され、Web ブラウザーで更新されたアプリケーションを確認できます。

## <a name="starting-stopping-or-restarting-an-existing-web-app"></a>既存の Web アプリの起動、停止、再起動
既存の Azure Web アプリ コンテナー (コンテナー内にデプロイされているすべての Java アプリケーションを含む) を起動または停止するには、 **[Azure Explorer]** (Azure Explorer) ビューを使用できます。

**Azure 用エクスプローラー** ビューがまだ開いていない場合、Eclipse の **[ウィンドウ]** メニュー、**[Show View (ビューの表示)]**、**[Other (その他)]**、**[Azure]**、**[Azure Explorer (Azure 用エクスプローラー)]** の順にクリックして開きます。 まだログインしていない場合は、ログインするように求められます。

**[Azure Explorer]** (Azure Explorer) ビューが表示されたら、次の手順に従って Web アプリを起動また停止します。 

1. **[Azure]** ノードを展開します。
2. **[Web Apps (Web アプリ)]** ノードを展開します。 
3. 目的の Web App を右クリックします。
4. コンテキスト メニューが表示されたら、**[Start (起動)]****[Stop (停止)]**、または **[Restart (再起動)]** をクリックします。 メニュー項目はコンテキストに依存します。つまり、Web アプリが実行しているときは停止操作のみ、Web アプリが現在実行されていないときは起動操作のみを行うことができます。
   
    ![既存の Web アプリを停止する][13]

## <a name="next-steps"></a>次のステップ
Azure Toolkits for Java IDE の詳細については、次のリンクをご覧ください。

* [Azure Toolkit for Eclipse]
  * [Azure Toolkit for Eclipse のインストール]
  * *Eclipse で Azure 用の Hello World Web アプリを作成する (この記事)*
  * [Azure Toolkit for Eclipse の新機能]
* [Azure Toolkit for IntelliJ]
  * [Azure Toolkit for IntelliJ のインストール]
  * [IntelliJ で Azure 用の Hello World Web アプリを作成する]
  * [Azure Toolkit for IntelliJ の新機能]

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

Azure Web Apps の作成の詳細については、「 [Web Apps の概要]」を参照してください。

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web-intellij-create-hello-world-web-app.md
[Azure Toolkit for Eclipse のインストール]: ../azure-toolkit-for-eclipse-installation.md
[Azure Toolkit for IntelliJ のインストール]: ../azure-toolkit-for-intellij-installation.md
[Azure Toolkit for Eclipse の新機能]: ../azure-toolkit-for-eclipse-whats-new.md
[Azure Toolkit for IntelliJ の新機能]: ../azure-toolkit-for-intellij-whats-new.md

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Web Apps の概要]: ./app-service-web-overview.md
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/

<!-- IMG List -->

[01]: ./media/app-service-web-eclipse-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-eclipse-create-hello-world-web-app/02-Dynamic-Web-Project.png
[03]: ./media/app-service-web-eclipse-create-hello-world-web-app/03-Context-Menu.png
[04]: ./media/app-service-web-eclipse-create-hello-world-web-app/04-Log-In-Dialog.png
[05]: ./media/app-service-web-eclipse-create-hello-world-web-app/05-Manage-Subscriptions-Dialog.png
[06]: ./media/app-service-web-eclipse-create-hello-world-web-app/06-Deploy-To-Azure-Web-Container.png
[07a]: ./media/app-service-web-eclipse-create-hello-world-web-app/07a-New-Web-App-Container-Dialog.png
[07b]: ./media/app-service-web-eclipse-create-hello-world-web-app/07b-New-Web-App-Container-Dialog.png
[08]: ./media/app-service-web-eclipse-create-hello-world-web-app/08-New-Resource-Group-Dialog.png
[09]: ./media/app-service-web-eclipse-create-hello-world-web-app/09-New-Service-Plan-Dialog.png
[10]: ./media/app-service-web-eclipse-create-hello-world-web-app/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/app-service-web-eclipse-create-hello-world-web-app/11-Completed-Deploy-Dialog.png
[12]: ./media/app-service-web-eclipse-create-hello-world-web-app/12-Activity-Log-View.png
[13]: ./media/app-service-web-eclipse-create-hello-world-web-app/13-Azure-Explorer-Web-App.png
[14]: ./media/app-service-web-eclipse-create-hello-world-web-app/14-publishDropdownButton.png
[15]: ./media/app-service-web-eclipse-create-hello-world-web-app/15-New-Azure-Web-Container.png



<!--HONumber=Jan17_HO4-->


