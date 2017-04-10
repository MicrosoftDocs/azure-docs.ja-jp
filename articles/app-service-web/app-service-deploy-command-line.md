---
title: "コマンド ライン ツールを使用した Azure アプリのデプロイの自動化 |Microsoft Docs"
description: "コマンドラインから Azure アプリをデプロイできる方法に関する情報を見つけます"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 8b65980c-eb75-44a2-8e0f-f9eb9e617d16
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 283b1cfda82b4f96ad5148c522a4c9833cb4c381
ms.openlocfilehash: e0e2e65557911bcac06d4dc355f47e9331934f8a


---
# <a name="automate-deployment-of-your-azure-app-with-command-line-tools"></a>コマンド ライン ツールを使用した Azure アプリのデプロイの自動化
コマンド ライン ツールを使用して Azure アプリのデプロイを自動化できます。 この記事では、使用可能なツールと、これらのツールをデプロイ ワークフローで使用する方法を示す有用なリンクを一覧で示します。 

## <a name="a-namemsbuildaautomate-deployment-with-msbuild"></a><a name="msbuild"></a>MSBuild を使用したデプロイの自動化
[Visual Studio IDE](#vs) を使用して開発を行う場合は、[MSBuild](http://msbuildbook.com/) を使用して IDE 内のすべての作業を自動化することができます。 [Web Deploy](#webdeploy) または [FTP/FTPS](#ftp) を使用してファイルをコピーするように MSBuild を構成することができます。 Web Deploy では、データベースのデプロイなど、デプロイに関連する多くのタスクを自動化することもできます。

MSBuild を使用したコマンドライン デプロイの詳細については、次のリソースを参照してください。

* [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。 Visual Studio を使用して Azure のデプロイを行うチュートリアル シリーズの第&10; 部。 Visual Studio でプロファイルの発行を設定した後に、コマンド ラインを使用してデプロイを行う方法を示します。
* [『Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build』](http://msbuildbook.com/)。 MSBuild を使用してデプロイを実行する方法に関する章が掲載されている書籍。

## <a name="a-namepowershellaautomate-deployment-with-windows-powershell"></a><a name="powershell"></a>Windows PowerShell を使用したデプロイの自動化
[Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx)から MSBuild または FTP デプロイの機能を実行することができます。 この機能を実行する場合は、Azure の REST 管理 API を簡単に呼び出せる一連の Windows PowerShell コマンドレットを使用することもできます。

詳細については、次のリソースを参照してください。

* [GitHub リポジトリにリンクされる Web アプリのデプロイ](app-service-web-arm-from-github-provision.md)
* [Web アプリと SQL Database をプロビジョニングする](app-service-web-arm-with-sql-database-provision.md)
* [Azure でマイクロサービスを予測どおりにデプロイする](app-service-deploy-complex-application-predictably.md)
* [Azure での実際のクラウド アプリケーションのビルド - すべてを自動化](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。 電子書籍の中に掲載されているサンプル アプリケーションが、Windows PowerShell スクリプトをどのように使用して Azure テスト環境を作成し、その環境をデプロイするかを説明する、電子書籍の章。 Azure PowerShell の他のドキュメントへのリンクを掲載している「 [リソース](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) 」セクションを参照してください。
* [Windows PowerShell スクリプトを使用した開発環境およびテスト環境の発行](../vs-azure-tools-publishing-using-powershell-scripts.md)。 Visual Studio で生成される Windows PowerShell デプロイ スクリプトの使用方法

## <a name="a-nameapiaautomate-deployment-with-net-management-api"></a><a name="api"></a>.NET 管理 API を使用したデプロイの自動化
C# のコードを作成し、デプロイの目的で MSBuild や FTP の機能を実行することができます。 この作業を実行する場合は、Azure の管理 REST API にアクセスして、サイト管理機能を実行することができます。

詳細については、次のリソースを参照してください。

* [Azure 管理ライブラリと .NET を使用してすべてを自動化](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)。 .NET 管理 API の紹介とその他のドキュメントへのリンク。

## <a name="a-namecliadeploy-from-azure-command-line-interface-azure-cli"></a><a name="cli"></a>Azure コマンド ライン インターフェイス (Azure CLI) からデプロイ
Windows、Mac、Linux マシンでコマンド ラインを使用し、FTP を使用してデプロイを実行することもできます。 この作業を実行する場合は、Azure CLI を使用して Azure REST 管理 API にアクセスすることもできます。

詳細については、次のリソースを参照してください。

* [Azure コマンド ライン ツール](https://azure.microsoft.com/downloads/)。 コマンド ライン ツールに関する情報を掲載している Azure.com のポータル ページです。

## <a name="a-namewebdeployadeploy-from-web-deploy-command-line"></a><a name="webdeploy"></a>Web デプロイ コマンド ラインからデプロイ
[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) は IIS へのデプロイを行うマイクロソフトのソフトウェアであり、インテリジェントなファイルの同期機能に加えて、FTP を使用する場合は自動化できない他の多くのデプロイ関連タスクも実行または調整することができます。 たとえば、Web Deploy を使用して Web アプリと共に新しいデータベースをデプロイ、またはデータベースの更新をデプロイすることができます。 また、Web Deploy は、変更されたファイルのみをインテリジェントにコピーするため、既存のサイトを更新するのに要する時間を最小化することもできます。 Microsoft Visual Studio および Team Foundation Server では Web Deploy のビルトイン機能をサポートしていますが、コマンド ラインから Web Deploy を直接使用してデプロイを自動化することもできます。 Web Deploy の各コマンドは非常に強力ですが、学習曲線を急上昇させることもできます。

## <a name="more-resources"></a>その他のリソース
コマンド ラインから自動化するための他のデプロイ オプションは、[Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy) などのクラウド ベースのサービスを使用することです。 詳細については、「 [Deploy ASP.NET applications to Azure Web Sites (ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする)](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)」をご覧ください。

コマンド ライン ツールの詳細については、次のリソースを参照してください。

* [シンプルな Web アプリ: デプロイ](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/)。 Web デプロイの使用を簡単にするために作成したツールに関する David Ebbo のブログ
* [Web デプロイ ツール](http://technet.microsoft.com/library/dd568996)。 Microsoft TechNet サイトの公式ドキュメント。 作成以来期間が経過していますが、現在も有効な出発点として使用できます。
* [Web Deploy の使用](http://www.iis.net/learn/publish/using-web-deploy)。 Microsoft IIS.NET サイトの公式ドキュメント。 同じく作成以来期間が経過していますが、現在も有効な出発点として使用できます。
* [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。 MSBuild は Visual Studio によって使用されるビルド エンジンであり、Web アプリケーションを Web Apps にデプロイする目的でコマンド ラインから使用することもできます。 このチュートリアルは、Visual Studio のデプロイに注目したシリーズの一部です。




<!--HONumber=Jan17_HO1-->


