---
title: "Azure .NET SDK とは"
description: "Azure .NET SDK に含まれるものを紹介します。"
documentationcenter: .net
author: chrissfanos
editor: mollybos
services: 
ms.assetid: f70279ac-7c56-4d6a-9882-352a8b644a4b
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/30/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 94c4929c84a671eca210a0e6b53eda5019cc0951
ms.lasthandoff: 03/25/2017


---
# <a name="what-is-the-azure-sdk-for-net"></a>Azure SDK for .NET とは
## <a name="overview"></a>概要
Azure SDK for .NET は、Visual Studio のツール、コマンドライン ツール、ランタイム バイナリ、およびクライアント ライブラリを 1 つにまとめたもので、Azure で実行するアプリケーションの開発、テスト、デプロイに役立ちます。 この記事では、SDK のインストールにより、どのようなことができるかについて説明します。 SDK は [Azure のダウンロード ページ](https://azure.microsoft.com/downloads/)でダウンロードできます。

Azure SDK for .NET には、 [Azure のサービスを利用するために必要なクライアント ライブラリ](http://go.microsoft.com/fwlink/?LinkId=510472)も含まれています。 これらのライブラリは NuGet で個々にインストールできます。

## <a id="included"></a>Azure SDK for .NET のインストールに含まれるもの
Azure SDK for .NET には次の製品が含まれており、インストールされます。

* [Visual Studio Community エディション 2015](#vwd)
* [Microsoft Azure ストレージ エミュレーター](#stgemulator)
* [Microsoft Azure Storage Tools](#stgtools)
* [Microsoft Azure Authoring Tools](#auth)
* [Microsoft Azure エミュレーター](#emulator)
* [HDInsight Tools for Visual Studio および Microsoft Hive ODBC ドライバー](#hdinsight)
* [Microsoft Azure Libraries for .NET](#libraries)
* [Microsoft Azure Mobile App SDK](#mobile)
* [Microsoft Azure PowerShell](#ps)
* [Microsoft Azure Tools for Microsoft Visual Studio](#tools)
* [Microsoft ASP.NET and Web Tools for Visual Studio](#wte)
* [Microsoft Azure Data Lake Tools for Visual Studio](#datalake)

### <a id="vwd"></a>Visual Studio Community エディション 2015
コンピューターに Visual Studio がインストールされていない場合は、SDK によって [Visual Studio Community エディション 2015](https://www.visualstudio.com/products/visual-studio-community-vs)がインストールされます。

### <a id="stgemulator"></a>Microsoft Azure ストレージ エミュレーター
[Azure ストレージ エミュレーター](http://msdn.microsoft.com/library/hh403989.aspx)は、SQL サーバーのインスタンスとローカル ファイル システムを使用して Azure のストレージ (キュー、テーブル、BLOB) をシミュレートします。これにより、ローカルのコンピューターでテストできます。

### <a id="stgtools"></a>Microsoft Azure Storage Tools
この製品は、コマンド ライン ツールの [AzCopy](http://aka.ms/AzCopy) をインストールします。このツールを使用すると、Azure のストレージ アカウントにデータを転送したり、このアカウントからデータを転送したりすることができます。

### <a id="auth"></a>Microsoft Azure Authoring Tools
この製品には次のツールが含まれています。

* [CSPack コマンド ライン ツール。](http://msdn.microsoft.com/library/gg432988.aspx) デプロイメント パッケージを作成する場合に使用します。
* [CSEncrypt コマンド ライン ツール。](http://msdn.microsoft.com/library/hh404001.aspx) リモート デスクトップ接続を介してクラウド サービスのロール インスタンスにアクセスする際のパスワードを暗号化するために使用します。
* クラウド サービス プロジェクトがランタイム環境と通信し、診断する場合に必要な各種のランタイム バイナリ。 これらのバイナリは、NuGet パッケージでは利用できません。

### <a id="emulator"></a>Microsoft Azure エミュレーター
[Azure エミュレーター](http://msdn.microsoft.com/library/dn339018.aspx)は、クラウド サービス環境をシミュレートします。これにより、クラウド サービス プロジェクトを Azure にデプロイする前に、ローカルのコンピューターでテストできます。

### <a id="hdinsight"></a>HDInsight Tools for Visual Studio および Microsoft Hive ODBC ドライバー
サーバー エクスプローラーの HDInsight ツールを使用すると、Hive データベースと HDInsight クラスターにリンクされたストレージ アカウントを移動したり、テーブルを作成したり、Hive クエリを送信できます。 詳細については、「 [HDInsight Hadoop Tools for Visual Studio の使用開始](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md)」をご覧ください。

### <a id="libraries"></a>Microsoft Azure Libraries for .NET
この製品には次のツールが含まれています。

* Azure のストレージ、サービス バス、キャッシュ用の NuGet パッケージ。このパッケージは、ユーザーのコンピューターに格納されるため、Visual Studio による新規のクラウド サービス プロジェクトの作成がオフラインでできるようになります。
* Visual Studio プラグイン。Visual Studio で [In-Role Cache](http://msdn.microsoft.com/library/dn386103.aspx) プロジェクトの実行がローカルでできるようになります。

### <a id="mobile"></a>Microsoft Azure Mobile App SDK
[Azure App Service の Mobile Apps](app-service-mobile/app-service-mobile-value-prop.md)を使用するためのツールです。

### <a id="ps"></a>Microsoft Azure PowerShell
Azure PowerShell を使用すると、 [Azure 環境の作成とデプロイを自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)できます。

### <a id="tools"></a>Microsoft Azure Tools for Microsoft Visual Studio
この製品を利用すると、Azure のリソース (主に Cloud Services および仮想マシン) で次のことができるようになります。

* [クラウド サービス プロジェクトを作成、公開、発行する](cloud-services/cloud-services-dotnet-get-started.md)
* [クラウド サービス プロジェクトのデプロイメント パッケージを作成する](http://msdn.microsoft.com/library/ff683672.aspx)
* [Azure の仮想マシンを作成して、新規の Web プロジェクトを作成する](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [PowerShell デプロイメント スクリプトを作成して、新規の仮想マシンを作成する](http://msdn.microsoft.com/library/dn642480.aspx)
* [Visual Studio のプロジェクト プロパティ ウィンドウでクラウド サービス プロジェクトの設定を表示し、管理する](http://msdn.microsoft.com/library/ee405486.aspx)
* サーバー エクスプローラーで[クラウド サービス](http://msdn.microsoft.com/library/ff683675.aspx)、[仮想マシン](http://msdn.microsoft.com/library/jj131259.aspx)、および[サービス バス](http://msdn.microsoft.com/library/jj149828.aspx)を表示し、管理する
* [クラウド サービスおよび仮想マシンに対してリモート デバッグを実行する](http://msdn.microsoft.com/library/ff683670.aspx)
* [Azure リソース グループ デプロイ プロジェクトを使用したリソースのプロビジョニングの自動化](https://msdn.microsoft.com/library/dn872471.aspx)

### <a id="wte"></a>Microsoft App Service Tools for Visual Studio
この製品をインストールすると、Azure の Web サイトで次のことができるようになります。

* [Azure の Web サイトに Web プロジェクトを発行する](app-service-web/web-sites-dotnet-get-started.md)
* [Azure の Web ジョブにコンソール アプリケーション プロジェクトを発行する](app-service-web/websites-dotnet-deploy-webjobs.md)
* [Azure の Web サイトおよび SQL データベース リソースを作成して、新しい Web プロジェクトを作成または発行する](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)
* [PowerShell デプロイメント スクリプトを作成して、新規の Web サイトを作成する](http://msdn.microsoft.com/library/dn642480.aspx)
* [サーバー エクスプローラーで Azure Web サイトの管理とトラブルシューティングを行う](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement)
* [Web サイトおよび Web ジョブに対してリモート デバッグを実行する](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)

> [!NOTE]
> これらの機能は Visual Studio の更新版にも含まれているため、Azure SDK for .NET をインストールしなくても利用できます。
> 
> 

## <a id="datalake"></a>Microsoft Azure Data Lake Tools for Visual Studio
詳細については、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。

## <a id="notincluded"></a>Azure SDK for .NET のインストールに含まれないもの
SDK をインストールしたときに、Azure 向けアプリケーションの開発に必要なものが含まれない場合もあります。 主なものは次のとおりです。

* [クライアント ライブラリ](http://go.microsoft.com/fwlink/?LinkId=510472)
  
    Azure SDK には、Azure のサービスを使用するために必要なクライアント ライブラリが含まれています。ただし、SDK をインストールしたときに、そのすべてがインストールされるわけではありません。 アプリケーションに必要なクライアント ライブラリが SDK によってインストールされない場合には、[NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472) で必要なライブラリを入手できます。 クライアント ライブラリが SDK によってインストールされる場合でも、NuGet.org で現在のバージョンに更新することをお勧めいたします。
  
      **Local copies of client libraries.** The Azure SDK for .NET copies to your computer the NuGet packages for some Azure client libraries, such as Storage, Service Bus, and Caching. These client libraries are automatically included in new cloud service projects, so the local NuGet packages enable Visual Studio to create projects even if you're not connected to the Internet. Client libraries are generally updated more frequently than new SDK versions are released, so the client libraries at NuGet.org are often more current than what you get with the SDK.
  
    **クライアント ライブラリが含まれるプロジェクト テンプレート。** [Azure クラウド サービス](cloud-services/cloud-services-dotnet-get-started.md)および Azure App Service の [Mobile Apps](app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) プロジェクト テンプレートのみに、いくつかのクライアント ライブラリが自動的に含まれます。 これらのテンプレートに他のライブラリを含める場合や、他のテンプレートを使用する場合は、必要な [クライアント ライブラリの NuGet パッケージ](http://go.microsoft.com/fwlink/?LinkId=510472) をインストールしてください。
* [Mobile Apps プロジェクト テンプレート](app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。
  
    Mobile Apps のテンプレートは、Visual Studio 2013 Update 2 以降でのみ利用できます。 Azure SDK for .NET をインストールしても、Visual Studio 2012 以前および Visual Studio 2013 Update 1 以前のバージョンでは利用できません。

## <a id="faq"></a>よく寄せられる質問
* [Visual Studio に Azure の機能の多くが含まれているのにもかかわらず、Azure SDK for .NET をインストールする必要がありますか。](#azinvs)
* [クライアント ライブラリを入手したいのですが、Azure SDK for .NET をインストールしないと入手できませんか。](#clientlib)
* [以前のバージョンの Azure SDK for .NET はどこにありますか。](#olderversions)
* [Azure SDK for .NET のバージョンのライフサイクル ポリシーを教えてください。](#lifecycle)
* [Azure SDK for .NET と互換性があるゲスト OS バージョンを教えてください。](#guestos)
* [Azure SDK for .NET はどのようにアンインストールできますか。](#uninstall)

### <a id="azinvs"></a>Visual Studio に Azure の機能の多くが含まれているのにもかかわらず、 Azure SDK for .NET をインストールする必要がありますか。
最新のツールを使用して Azure 向けの開発をする場合は、SDK をインストールすることをお勧めします。 できれば SDK をインストールしたくない場合、次の条件を満たしていれば、インストールする必要はありません。

* 既に最新の [Visual Studio Update](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5)をインストールしている。
* 開発の対象が Azure Websites または Mobile Services 向けに限られており、Cloud Services または Virtual Machines 向けの開発ではない。
* アプリケーションがストレージを使用しない、または使用するとしてもストレージ エミュレーターも AzCopy ツールも必要ではない。

### <a id="clientlib"></a>クライアント ライブラリを入手したいのですが、 Azure SDK for .NET をインストールしないと入手できませんか。
SDK でクライアント ライブラリがインストールされるのは、インターネットに接続していなくてもクラウド サービス プロジェクトを作成できるようにする場合だけです。 現行バージョンのクライアント ライブラリは、 [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472)で入手できる NuGet パッケージに用意されています。 詳細については、この記事の初めの方に記載されている [Azure SDK for .NET のインストールに含まれないもの](#notincluded) を参照してください。

### <a id="olderversions"></a>以前のバージョンの Azure SDK for .NET はどこにありますか。
以前のバージョンは、ダウンロード ページの「 [Azure SDK for .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) 」で確認してください。

### <a id="lifecycle"></a>Azure SDK for .NET のバージョンのライフサイクル ポリシーを教えてください。
「 [Microsoft Azure サポート ライフサイクル ポリシー](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq)」を参照してください。

### <a id="guestos"></a>Azure SDK for .NET と互換性があるゲスト OS バージョンを教えてください。
「 [Azure ゲスト OS リリースと SDK の互換性対応表](http://msdn.microsoft.com/library/ee924680.aspx)」を参照してください。

### <a id="uninstall"></a>Azure SDK for .NET はどのようにアンインストールできますか。
この記事の「 [Azure SDK for .NET のインストールに含まれるもの](#included) 」の下に記載されている各項目は、Windows の [コントロール パネル] &gt; **[プログラムと機能]**のインストール済みプログラム一覧には個々のプログラムとして示されています。  これらを、グループとしてアンインストールする方法はなく、各プログラムは個別にアンインストールする必要があります。

Azure SDK for .NET が既にインストールされている場合に新しいバージョンをインストールしたときは、通常は古いバージョンをアンインストールする必要はありません。 SDK のインストールでは、多くの場合、新しいものを追加し古いものをそのままにするのではなく、既存のプログラムを更新します。

ただし、以前のバージョンの不要な残留物を削除する場合は、同じプログラムの新しいバージョンが存在する場合のみ、以前のバージョン番号と明記されているプログラムのみをアンインストールします。 たとえば、Microsoft Azure Tools for Microsoft Visual Studio 2013 をバージョン 2.5 から 2.6 に更新した場合、2.5 と 2.6 の両方がある場合に、バージョン 2.5 をアンインストールします。 Microsoft Azure Authoring Tools のバージョン 2.5 のみが表示されている場合は、アンインストールするのは安全ではない場合があります。

**[プログラムと機能]** に示されているプログラムのバージョン番号は、誤解を与える場合があります。  たとえば、SDK バージョン 2.6 は、Visual Studio 2013 用の SDK をインストールした場合は、Microsoft Azure Mobile App SDK V1.0 を含み、Visual Studio 2015 用をインストールした場合は、Microsoft Azure Mobile App SDK V2.0 を含みます。この場合のバージョンは、SDK のバージョンではなくプログラムの Visual Studio のバージョンを示しています。

この記事には、Azure SDK の以前のバージョンに含まれていたすべてのプログラムは記載していません。以前の SDK バージョンには、以降のバージョンでは削除されたプログラムを含んでいる場合もあり、以前の SDK バージョンには他にもアンインストールできるプログラムがある場合があります。 たとえば、バージョン 2.5 では Azure Resource Manager Tools for Visual Studio がインストールされますが、**[プログラムと機能]** では個別のプログラムとして表示されなくなったので、この記事では列挙していません。  この記事では、Azure SDK for .NET バージョン 2.6 に含まれているプログラムのみを一覧表示しています。

> **注:** SDK に含まれる一部のプログラムは、別のコンテキストで別にインストールされる場合があり、SDK をすべて必要としない場合でも必要である場合があります。 これは、以前のバージョンの SDK によってインストールされた、以降のバージョンの SDK では削除されたプログラムにも該当します。 プログラムをアンインストールする場合には、コンピューターで引き続き必要なものは削除しないように注意する必要があります。
> 
> 

## <a id="versions"></a>バージョン
現在のバージョンを確認したり、以前のバージョンをダウンロードしたりするには、「 [Azure SDK for .NET のバージョン履歴](https://azure.microsoft.com/downloads/archive-net-downloads/) 」ページを参照してください。

## <a id="resources"></a>リソース
現在の Azure SDK for .NET またはクライアント ライブラリをダウンロードするには、 [Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)を参照してください。

Azure SDK for .NET (クライアント ライブラリを含む) のソース コードについては、 [GitHub.com/Azure の Web サイト](https://github.com/azure/)を参照してください。

Azure のクライアント ライブラリのリファレンス ドキュメントについては、 [Azure .NET のリファレンス ページ](https://azure.microsoft.com/documentation/api/)を参照してください。


