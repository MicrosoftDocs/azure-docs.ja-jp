---
title: Web サイトで ReportViewer を使用する | Microsoft Docs
description: このトピックでは、Microsoft Azure 仮想マシンに保存されたレポートを表示する Visual Studio ReportViewer コントロールを使用して、Microsoft Azure Web サイトを作成する方法を説明します。
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: b554dc1fa33519d87aa0c9c5ba9130b47cbea142
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971752"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Azure でホストされる Web サイトで ReportViewer を使用する
> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルが用意されています。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。

Microsoft Azure 仮想マシンに保存されたレポートを表示する Visual Studio ReportViewer コントロールを使用して、Microsoft Azure Web サイトを作成できます。 ReportViewer コントロールは、ASP.NET Web アプリケーション テンプレートを使用して作成する Web アプリケーション内にあります。

> [!IMPORTANT]
> ASP.NET MVC Web アプリケーション テンプレートでは、ReportViewer コントロールはサポートされません。

ReportViewer を Microsoft Azure Web サイトに組み込むには、次のタスクを完了する必要があります。

* **追加する** 
* **構成する** 
* **発行する** 

## <a name="prerequisites"></a>前提条件
「 [Azure Virtual Machines での SQL Server Business Intelligence](../classic/ps-sql-bi.md)」の「一般的な推奨事項とベスト プラクティス」セクションを参照してください。

> [!NOTE]
> ReportViewer コントロールは、Standard Edition 以上のエディションの Visual Studio に含まれています。 Web Developer Express Edition を使用している場合、ReportViewer ランタイム機能を使用するには、 [Microsoft Report Viewer 2012 Runtime](https://www.microsoft.com/download/details.aspx?id=35747) をインストールする必要があります。
>
> Microsoft Azure では、ローカル処理モードで構成された ReportViewer はサポートされません。

## <a name="adding-assemblies-to-the-deployment-package"></a>デプロイ パッケージにアセンブリを追加する
ASP.NET アプリケーションをオンプレミスでホストする場合、ReportViewer アセンブリは通常、Visual Studio のインストール時に IIS サーバーのグローバル アセンブリ キャッシュ (GAC) 内にインストールされるため、アプリケーションから直接アクセスが可能です。 ただし、ASP.NET アプリケーションをクラウドでホストする場合、Microsoft Azure では GAC へのインストールが許可されないため、ReportViewer アセンブリがアプリケーションでローカルで使用できるようにしておく必要があります。 このためには、プロジェクトにアセンブリへの参照を追加し、これらがローカルでコピーされるように構成します。

リモート処理モードでは、次のアセンブリが ReportViewer コントロールで使用されます。

* **Microsoft.ReportViewer.WebForms.dll**:ページ内で ReportViewer を使用するために必要な ReportViewer コードが含まれています。 プロジェクト内の ASP.NET ページに ReportViewer コントロールをドロップすると、このアセンブリへの参照がプロジェクトに追加されます。
* **Microsoft.ReportViewer.Common.dll**:実行時に ReportViewer コントロールによって使用されるクラスが含まれています。 自動的にはプロジェクトに追加されません。

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Microsoft.ReportViewer.Common に参照を追加する
* プロジェクトの **[参照]** ノードを右クリックして **[参照の追加]** を選択し、[.NET] タブからアセンブリを選択して **[OK]** をクリックします。

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>ASP.NET アプリケーションからアセンブリにローカル アクセスできるようにする
1. **[参照]** フォルダー内の Microsoft.ReportViewer.Common アセンブリをクリックすると、プロパティがプロパティ ペインに表示されます。
2. プロパティ ペインで、 **[ローカルにコピー]** を True に設定します。
3. Microsoft.ReportViewer.WebForms についても、手順 1. と 2. を繰り返します。

### <a name="to-get-reportviewer-language-pack"></a>ReportViewer Language Pack を入手する
1. [Microsoft ダウンロード センター](https://go.microsoft.com/fwlink/?LinkId=317386)から、適切な Microsoft Report Viewer 2012 Runtime 再頒布可能パッケージをインストールします。
2. ドロップダウン リストから言語を選択すると、ダウンロード センターの対応するページにリダイレクトされます。
3. **[ダウンロード]** をクリックして、ReportViewerLP.exe のダウンロードを開始します。
4. ReportViewerLP.exe をダウンロードしたら、**[実行]** をクリックしてすぐにインストールするか、**[保存]** をクリックしてコンピューターに保存します。 **[保存]** をクリックした場合は、ファイルを保存したフォルダーの名前を覚えておいてください。
5. ファイルを保存したフォルダーを見つけます。 ReportViewerLP.exe を右クリックし、**[管理者として実行]**、**[はい]** の順にクリックします。
6. ReportViewerLP.exe を実行した後、C:\windows\assembly にリソース ファイル **Microsoft.ReportViewer.Webforms.Resources** および **Microsoft.ReportViewer.Common.Resources** が含まれていることが確認できます。

### <a name="to-configure-for-localized-reportviewer-control"></a>ローカライズされた ReportViewer コントロール用に構成する
1. 前の手順に従って、Microsoft Report Viewer 2012 Runtime 再頒布可能パッケージをダウンロードしてインストールします。
2. プロジェクトに \<言語\> フォルダーを作成し、関連付けられたリソース アセンブリ ファイルをそこにコピーします。 コピーするリソース アセンブリ ファイルは、**Microsoft.ReportViewer.Webforms.Resources.dll** と **Microsoft.ReportViewer.Common.Resources.dll** です。リソース アセンブリ ファイルを選択し、プロパティ ウィンドウで **[出力ディレクトリにコピー]** を **[常にコピーする]** に設定します。
3. Web プロジェクトのカルチャと UI カルチャを設定します。 ASP.NET Web ページのカルチャと UI カルチャを設定する方法の詳細については、「[方法:ASP.NET Web ページのグローバリゼーション用のカルチャおよび UI カルチャを設定する](https://go.microsoft.com/fwlink/?LinkId=237461)」をご覧ください。

## <a name="configuring-authentication-and-authorization"></a>認証と承認を構成する
ReportViewer は、適切な資格情報を使用してレポート サーバーに対する認証を行う必要があります。必要なレポートにアクセスするには、その資格情報がレポート サーバーによって承認される必要があります。 認証についての詳細は、ホワイト ペーパー「[Reporting Services report viewer control and Microsoft Azure Virtual Machine based report servers](https://msdn.microsoft.com/library/azure/dn753698.aspx)」 (Reporting Services レポート ビューアー コントロールと Microsoft Azure Virtual Machines ベースのレポート サーバー) を参照してください。

## <a name="publish-the-aspnet-web-application-to-azure"></a>ASP.NET Web アプリケーションを Azure に発行する
ASP.NET Web アプリケーションを Azure に発行する手順については、[方法:Visual Studio から Azure への Web アプリケーションの移行および発行](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)に関する記事と [Web アプリと ASP.NET の概要](../../../app-service/app-service-web-get-started-dotnet.md)に関する記事をご覧ください。

> [!IMPORTANT]
> [Azure デプロイ プロジェクトの追加] または [Azure クラウド サービス プロジェクトの追加] コマンドがソリューション エクスプローラーのショートカット メニューに表示されない場合は、必要に応じてプロジェクトの [ターゲット フレームワーク] を .NET Framework 4 に変更してください。
>
> これら 2 つのコマンド機能は、実質的には同じです。 インストールされている Microsoft Azure SDK のバージョンに合わせて応じて、いずれか一方のコマンドがショートカット メニューに表示されます。
>
>

## <a name="resources"></a>リソース
[Microsoft レポート](https://go.microsoft.com/fwlink/?LinkId=205399)

[Azure Virtual Machines での SQL Server Business Intelligence](../classic/ps-sql-bi.md)

[ネイティブ モードのレポート サーバーを実行する Azure VM を PowerShell を使用して作成する](../classic/ps-sql-report.md)
