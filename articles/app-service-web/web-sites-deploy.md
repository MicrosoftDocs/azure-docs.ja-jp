---
title: "Azure App Service へのアプリのデプロイ | Microsoft Docs"
description: "Azure App Service にアプリをデプロイする方法について説明します。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: f1464f71-2624-400e-86a2-e687e385804f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin;dariac
translationtype: Human Translation
ms.sourcegitcommit: 283b1cfda82b4f96ad5148c522a4c9833cb4c381
ms.openlocfilehash: 4b3b96e9c5d7a4ff99c803aa356dcb5ad6997978


---
# <a name="deploy-your-app-to-azure-app-service"></a>Azure App Service へのアプリのデプロイ
この記事は、Web アプリ、モバイル アプリ バックエンド、または API アプリのファイルを [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする際の最適なオプションを特定するのに役立ちます。また、選択したオプションに固有の手順が記載された適切なリソースも紹介しています。

## <a name="a-nameoverviewaazure-app-service-deployment-overview"></a><a name="overview"></a>Azure App Service のデプロイの概要
Azure App Service では、アプリケーション フレームワーク (ASP.NET、PHP、Node.js など) を自動的に保持します。 既定で有効化されるフレームワークもあれば、有効にするために簡単なチェックマークによる構成が必要なフレームワーク (Java や Python など) もあります。 また、PHP のバージョンやランタイムのビット数など、アプリケーション フレームワークをカスタマイズすることもできます。 詳細については、「 [Azure App Service での Web アプリの構成](web-sites-configure.md)」をご覧ください。

Web サーバーやアプリケーション フレームワークを気にする必要がないため、アプリを App Service にデプロイするときは、コード、バイナリ、コンテンツ ファイル、それぞれのディレクトリ構造を、Azure の [**/site/wwwroot** ディレクトリ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (WebJobs の場合は **/site/wwwroot/App_Data/Jobs/** ディレクトリ) にデプロイすることが重要になります。 App Service では、3 つの異なるデプロイ プロセスをサポートしています。 この記事のデプロイ方法はすべて、次のいずれかのプロセスを使用します。 

* [FTP または FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): [FileZilla](https://filezilla-project.org) から [NetBeans](https://netbeans.org) などのフル機能を備えた IDE まで、好みの FTP または FTPS 対応ツールを使用して、ファイルを Azure に移動します。 これは、厳密なファイル アップロード プロセスです。 App Service からバージョン管理やファイル構造管理などの追加サービスは提供されません。 
* [Kudu (Git/Mercurial または OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment): Kudu は App Service の[デプロイ エンジン](https://github.com/projectkudu/kudu/wiki)です。 任意のリポジトリから Kudu にコードを直接プッシュします。 Kudu では、コードをプッシュしたときに、継続的なデプロイと他の自動タスクのための追加サービス (バージョン管理、パッケージの復元、MSBuild、 [Web フック](https://github.com/projectkudu/kudu/wiki/Web-hooks) など) も提供されます。 Kudu デプロイ エンジンでは、次の 3 つの異なる種類のデプロイ ソースをサポートしています。   
  
  * OneDrive および Dropbox からのコンテンツの同期   
  * GitHub、Bitbucket、および Visual Studio Team Services からの自動同期によるリポジトリ ベースの継続的なデプロイ  
  * ローカル Git からの手動での同期によるリポジトリ ベースのデプロイ  
* [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): Visual Studio など、好きな Microsoft ツールから、IIS サーバーへのデプロイを自動化するのと同じツールを使用して、直接 App Service にコードをデプロイします。 このツールは、差分のみのデプロイ、データベースの作成、接続文字列の変換などをサポートします。Web Deploy は、Azure にデプロイする前にアプリケーション バイナリがビルドされる点で Kudu とは異なります。 FTP と同様に、App Service から追加サービスは提供されません。

一般的な Web 開発ツールでは、これらのデプロイメント プロセスを&1; つ以上サポートします。 選択したツールによって利用できるデプロイメント プロセスが決まりますが、自由に使える実際の DevOps 機能は、デプロイメント プロセスと選択したツールの組み合わせによって異なります。 たとえば、 [Visual Studio と Azure SDK](#vspros)から Web Deploy を実行する場合、Kudu の自動化機能を利用できなくても、Visual Studio でパッケージの復元と MSBuild による自動化が提供されます。 

> [!NOTE]
> これらのデプロイ プロセスでは、実際には、アプリで必要な [Azure リソースがプロビジョニング](../azure-resource-manager/resource-group-template-deploy-portal.md) されるわけではありません。 ただし、リンクされている手順に関する記事のほとんどは、アプリをプロビジョニングし、コードをエンド ツー エンドでデプロイする方法を示しています。 また、「 [コマンド ライン ツールを使用したデプロイの自動化](#automate) 」では、Azure リソースをプロビジョニングするための追加オプションを紹介しています。
> 
> 

## <a name="a-nameftpadeploy-manually-by-uploading-files-with-ftp"></a><a name="ftp"></a>FTP を使用したファイルのアップロードによる手動デプロイ
Web コンテンツを Web サーバーに手動でコピーすることに慣れている場合は、エクスプローラーや [FileZilla](https://filezilla-project.org/) などの [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) ユーティリティを使用してファイルをコピーできます。

ファイルの手動コピーの利点は次のとおりです。

* FTP ツールを使い慣れていて、複雑さが最小限で済みます。 
* ファイルのコピー先が正確にわかります。
* FTPS を使用するとセキュリティが強化されます。

ファイルの手動コピーの欠点は次のとおりです。

* App Service の適切なディレクトリにファイルをデプロイする方法を把握する必要があります。 
* エラーが発生したときにロールバックするためのバージョン管理が行われません。
* デプロイに関する問題のトラブルシューティングに利用できる組み込みのデプロイ履歴がありません。
* 多くの FTP ツールでは、差分のみのコピーは行われず、すべてのファイルがコピーされるため、デプロイ時間が長くなる可能性があります。  

### <a name="a-namehowtoftpahow-to-upload-files-with-ftp"></a><a name="howtoftp"></a>FTP を使用してファイルをアップロードする方法
[Azure Portal](https://portal.azure.com) では、FTP または FTPS を使用してアプリのディレクトリに接続するために必要な情報がすべて提供されます。

* [FTP を使用した Azure App Service へのアプリのデプロイ](app-service-deploy-ftp.md)

## <a name="a-namedropboxadeploy-by-syncing-with-a-cloud-folder"></a><a name="dropbox"></a>クラウド フォルダーとの同期によるデプロイ
[ファイルの手動コピー](#ftp) に代わる優れた方法として、OneDrive や Dropbox などのクラウド ストレージ サービスから App Service にファイルとフォルダーを同期する方法があります。 クラウド フォルダーとの同期では、デプロイに Kudu プロセスを利用します (「 [デプロイメント プロセスの概要](#overview)」を参照してください)。

クラウド フォルダーとの同期の利点は次のとおりです。

* デプロイのシンプルさ。 OneDrive や Dropbox などのサービスは、デスクトップ同期クライアントを提供するので、ローカルの作業ディレクトリもデプロイメント ディレクトリになります。
* ワンクリックでデプロイできます。
* Kudu デプロイ エンジンのすべての機能 (パッケージの復元、自動化など) を利用できます。

クラウド フォルダーとの同期の欠点は次のとおりです。

* エラーが発生したときにロールバックするためのバージョン管理が行われません。
* デプロイは自動化されず、手動で同期する必要があります。

### <a name="a-namehowtodropboxahow-to-deploy-by-syncing-with-a-cloud-folder"></a><a name="howtodropbox"></a>クラウド フォルダーと同期してデプロイする方法
[Azure ポータル](https://portal.azure.com)では、コンテンツを同期する OneDrive または Dropbox クラウド ストレージのフォルダーを指定し、そのフォルダー内でアプリのコードとコンテンツの作業を行い、ボタンをクリックするだけで App Service に同期できます。

* [クラウド フォルダーから Azure App Service へのコンテンツの同期](app-service-deploy-content-sync.md)。 

## <a name="a-namecontinuousdeploymentadeploy-continuously-from-a-cloud-based-source-control-service"></a><a name="continuousdeployment"></a>クラウド ベースのソース管理サービスからの継続的なデプロイ
開発チームで [Visual Studio Team Services](http://www.visualstudio.com/)、[GitHub](https://www.github.com)、[BitBucket](https://bitbucket.org/) などのクラウド ベースのソース コード管理 (SCM) サービスを使用している場合、リポジトリと統合し、継続的にデプロイするように App Service を構成できます。 

クラウド ベースのソース管理サービスからのデプロイの利点は次のとおりです。

* バージョン管理により、ロールバックが有効になります。
* Git (および該当する場合は、Mercurial) リポジトリの継続的なデプロイを構成できます。 
* 分岐固有のデプロイでは、個々の [スロット](web-sites-staged-publishing.md)にそれぞれ異なる分岐をデプロイできます。
* Kudu デプロイ エンジンのすべての機能 (デプロイのバージョン管理、ロールバック、パッケージの復元、自動化など) を利用できます。

クラウド ベースのソース管理サービスからのデプロイの欠点は次のとおりです。

* 各 SCM サービスの知識が必要です。

### <a name="a-namevstsahow-to-deploy-continuously-from-a-cloud-based-source-control-service"></a><a name="vsts"></a>クラウド ベースのソース管理サービスから継続的にデプロイする方法
[Azure ポータル](https://portal.azure.com)では、GitHub、Bitbucket、および Visual Studio Team Services からの継続的なデプロイを構成できます。

* [Azure App Service への継続的なデプロイ](app-service-continuous-deployment.md)。 

Azure Portal で一覧表示されていないクラウド リポジトリ ([GitLab](https://gitlab.com/) など) から継続的なデプロイを手動で構成する方法については、「[Setting up continuous deployment using manual steps (手動による手順を使用した継続的デプロイの設定)](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)」を参照してください。

## <a name="a-namelocalgitdeploymentadeploy-from-local-git"></a><a name="localgitdeployment"></a>ローカル Git からのデプロイ
開発チームで Git に基づくオンプレミスのローカル ソース コード管理 (SCM) サービスを使用している場合は、App Service へのデプロイ ソースとしてこれを構成できます。 

ローカル Git からのデプロイの利点は次のとおりです。

* バージョン管理により、ロールバックが有効になります。
* 分岐固有のデプロイでは、個々の [スロット](web-sites-staged-publishing.md)にそれぞれ異なる分岐をデプロイできます。
* Kudu デプロイ エンジンのすべての機能 (デプロイのバージョン管理、ロールバック、パッケージの復元、自動化など) を利用できます。

ローカル Git からのデプロイの欠点は次のとおりです。

* 各 SCM システムの知識が必要です。
* 継続的なデプロイのためのターンキー ソリューションがありません。 

### <a name="a-namevstsahow-to-deploy-from-local-git"></a><a name="vsts"></a>ローカル Git からデプロイする方法
[Azure ポータル](https://portal.azure.com)で、ローカル Git デプロイを構成できます。

* [Azure App Service へのローカル Git デプロイ](app-service-deploy-local-git.md)。 
* [任意の git/hg リポジトリから Web アプリへの発行](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。  

## <a name="deploy-using-an-ide"></a>IDE を使用したデプロイ
[Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) と [Azure SDK](https://azure.microsoft.com/downloads/)、[Xcode](https://developer.apple.com/xcode/)、[Eclipse](https://www.eclipse.org)、[IntelliJ IDEA](https://www.jetbrains.com/idea/) などの IDE スイートを既に使用している場合は、IDE 内から直接 Azure にデプロイできます。 このオプションは個人開発者に最適です。

Visual Studio は&3; つのデプロイ プロセス (FTP、Git、Web Deploy) をすべてサポートしていますが、FTP または Git と統合されていれば、他の IDE でも App Service にデプロイできます (「 [デプロイメント プロセスの概要](#overview)」を参照してください)。

IDE を使用したデプロイの利点は次のとおりです。

* エンド ツー エンドのアプリケーション ライフ サイクルのために、ツールが最小限に抑えられる可能性があります。 IDE の外部に移動せずに、アプリの開発、デバッグ、追跡、Azure へのデプロイをすべて行うことができます。 

IDE を使用したデプロイの欠点は次のとおりです。

* ツールの複雑さが増します。
* チーム プロジェクトの場合、ソース管理システムが引き続き必要になります。

<a name="vspros"></a> Visual Studio と Azure SDK を使用したデプロイのその他の利点は次のとおりです。

* Azure SDK により、Visual Studio で Azure リソースが主要リソースになります。 アプリの作成、削除、編集、起動、および終了、バックエンドの SQL データベースのクエリ、Azure アプリのライブ デバッグなどが可能です。 
* Azure でのコード ファイルのライブ編集。
* Azure でのアプリのライブ デバッグ。
* Azure エクスプローラーの統合。
* 差分のみのデプロイ。 

### <a name="a-namevsahow-to-deploy-from-visual-studio-directly"></a><a name="vs"></a>Visual Studio から直接デプロイする方法
* [Azure と ASP.NET を使ってみる](web-sites-dotnet-get-started.md) Visual Studio と Web Deploy を使用して簡単な ASP.NET MVC Web プロジェクトを作成し、デプロイする方法。
* [Visual Studio を使用した Azure WebJobs のデプロイ方法](websites-dotnet-deploy-webjobs.md)。 コンソール アプリケーション プロジェクトを Web ジョブとしてデプロイするための構成方法。  
* [メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションの Web アプリへのデプロイ](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。 Visual Studio、Web Deploy、Entity Framework、Code First Migrations を使用して、ASP.NET MVC Web プロジェクトを SQL データベースと共に配置する方法。
* [Visual Studio を使用した ASP.NET Web デプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)。 12 部構成のチュートリアル シリーズであり、このリスト内に掲載されている他のリソースより詳細にデプロイ タスクの範囲を網羅しています。 チュートリアルを作成した後で、不足している事項を説明するためにメモが追加されたため、一部の Azure デプロイ機能が追加されました。
* [Git リポジトリから ASP.NET Web サイトを Visual Studio 2012 内にある Azure へ直接のデプロイ](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。 Visual Studio 内で ASP.NET Web プロジェクトをデプロイし、Git プラグインを使用してコードを Git にコミットして、Azure を Git リポジトリに接続する方法について説明します。 Visual Studio 2013 以降、Git のサポートが組み込まれており、プラグインをインストールする必要はありません。

### <a name="a-nameaztkahow-to-deploy-using-the-azure-toolkits-for-eclipse-and-intellij-idea"></a><a name="aztk"></a>Azure Toolkit for Eclipse や Azure Toolkit for IntelliJ IDEA を使用してデプロイする方法
Azure には、[Azure Toolkit for Eclipse](../azure-toolkit-for-eclipse.md) や [Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij.md) を使用することで、Eclipse や IntelliJ から直接 Web Apps をデプロイすることができます。 以下のチュートリアルでは、いずれかの IDE を使用して単純な "Hello" world Web App を Azure にデプロイする手順を説明しています。

* [Eclipse で Azure 用の Hello World Web アプリを作成する](app-service-web-eclipse-create-hello-world-web-app.md) このチュートリアルでは、Azure Toolkit for Eclipse を使用して、Azure 用の Hello World Web アプリを作成、デプロイする方法について説明します。
* [IntelliJ で Azure 用の Hello World Web アプリを作成する](app-service-web-intellij-create-hello-world-web-app.md) このチュートリアルでは、Azure Toolkit for IntelliJ を使用して、Azure 用の Hello World Web アプリを作成、デプロイする方法について説明します。

## <a name="a-nameautomateaautomate-deployment-by-using-command-line-tools"></a><a name="automate"></a>コマンド ライン ツールを使用したデプロイの自動化
コマンド ライン ターミナルを開発環境として使用する場合は、コマンド ライン ツールを使用して App Service アプリのデプロイ タスクをスクリプト化できます。 

コマンド ライン ツールを使用したデプロイの利点は次のとおりです。

* スクリプト化されたデプロイ シナリオが可能になります。
* Azure リソースのプロビジョニングとコードのデプロイを統合できます。
* Azure のデプロイを既存の継続的な統合スクリプトに統合できます。

コマンド ライン ツールを使用したデプロイの欠点は次のとおりです。

* GUI を好む開発者には適していません。

### <a name="a-nameautomatehowahow-to-automate-deployment-with-command-line-tools"></a><a name="automatehow"></a>コマンド ライン ツールを使用してデプロイを自動化する方法

コマンド ライン ツールの一覧とチュートリアルへのリンクについては、「[Automate deployment of your Azure app with command-line tools](app-service-deploy-command-line.md)」(コマンド ライン ツールを使用した Azure アプリのデプロイの自動化) をご覧ください。 

## <a name="a-namenextstepsanext-steps"></a><a name="nextsteps"></a>次のステップ
シナリオによっては、アプリをステージングと運用バージョンの間で簡単に切り替えることができる環境が必要です。 詳細については、 [Web アプリのステージングされたデプロイ](web-sites-staged-publishing.md)に関するページをご覧ください。

バックアップと復元計画を策定することは、デプロイ ワークフローの重要な部分です。 App Service のバックアップと復元の機能については、 [Web Apps のバックアップ](web-sites-backup.md)に関するページを参照してください。  

Azure のロールベースのアクセス許可を使用して、App Service のデプロイへのアクセスを管理する方法については、 [RBAC と Web Apps の公開](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/)に関するブログを参照してください。




<!--HONumber=Jan17_HO1-->


