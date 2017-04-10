---
title: "現行バージョンの Azure Tools にプロジェクトをアップグレードする方法 | Microsoft Docs"
description: "Visual Studio の Azure プロジェクトを現行バージョンの Azure Tools にアップグレードする方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 1d64070a-078d-468a-87f4-e6715de6475f
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e346640f6cbf7969bf373b84d8a20a24a74f654e


---
# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>現行バージョンの Azure Tools for Visual Studio にプロジェクトをアップグレードする方法
## <a name="overview"></a>Overview
現行 (1.6 より後の) バージョン の Azure Tools をインストールすると、バージョン 1.6 (2011 年 11 月) より前の Azure Tools を使用して作成されたプロジェクトはすべて、プロジェクトを開いた際に自動的にアップグレードされます。 バージョン 1.6 (2011 年 11 月) を使用してプロジェクトを作成し、まだそのバージョンがインストールされている場合は、バージョンの低い方でプロジェクトを開けば、アップグレードするかどうかの判断を先延ばしすることができます。

## <a name="how-your-project-changes-when-you-upgrade-it"></a>アップグレードしたときのプロジェクトの変化
プロジェクトが自動的にアップグレードされた場合、またはアップグレードすることを明示的に指定した場合、特定のアセンブリの現行バージョンで動作するようにプロジェクトに変更が加えられます。また、このセクションでも説明していますが、いくつかのプロパティも変更されます。 既にあるプロジェクトと新しいバージョンのツールとの互換性を確保するために、他の変更が必要になる場合は、それらの変更を手動で行う必要があります。

* 新しいバージョンの Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll を参照するように、Web ロールの web.config ファイルと worker ロールの app.config ファイルがアップグレードされます。
* Microsoft.WindowsAzure.StorageClient.dll、Microsoft.WindowsAzure.Diagnostics.dll、Microsoft.WindowsAzure.ServiceRuntime.dll の各アセンブリが新しいバージョンにアップグレードされます。
* Azure のプロジェクト ファイル (.ccproj) に格納された発行プロファイルは、.azurePubXml という拡張子で **Publish** サブディレクトリ内の別のファイルに移されます。
* 新機能と変更された機能をサポートするために、発行プロファイルのいくつかのプロパティが更新されます。 デプロイされたクラウド サービスを同時にまたは段階的に更新できるため、**[AllowUpgrade]** は **[DeploymentReplacementMethod]** に置き換えられます。
* **UseIISExpressByDefault** プロパティが追加され、false に設定されます。これは、デバッグに使用する Web サーバーがインターネット インフォメーション サービス (IIS) から IIS Express に自動的に変更されることがないようにするためです。 最近のバージョンのツールで作成したプロジェクトの Web サーバーは、既定では IIS Express になります。
* 既にあるプロジェクトのロールで Azure のキャッシュ機能がホストされている場合、プロジェクトをアップグレードするときにサービス構成 (.cscfg ファイル) とサービス定義 (.csdef ファイル) 内の一部のプロパティが変更されます。 プロジェクトで使用されている Azure のキャッシュ機能が NuGet パッケージである場合、プロジェクトは最新バージョンのパッケージにアップグレードされます。 web.config ファイルを開き、クライアントの構成がアップグレード処理後も適切に維持されていることを確認する必要があります。 NuGet パッケージを使用せずに、クライアント アセンブリへの参照として Azure のキャッシュ機能を追加した場合は、アセンブリが更新されません。アセンブリへの参照を新しいバージョンに手動で更新する必要があります。

> [!IMPORTANT]
> F# プロジェクトでは、Azure アセンブリへの参照を手動で更新して、これらのアセンブリの新しいバージョンを参照する必要があります。
> 
> 

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Azure プロジェクトを現行リリースにアップグレードする方法
1. アップグレード後のプロジェクトに使用する Visual Studio 環境に、新しいバージョンの Azure Tools をインストールして、アップグレード対象のプロジェクトを開きます。 プロジェクトが 1.6 (2011 年 11 月) リリース未満の Azure Tools で作成されている場合、プロジェクトは新しいバージョンに自動的にアップグレードされます。 2011 年 11 月リリースでプロジェクトが作成され、そのバージョンがまだインストールされている場合、プロジェクトはそのバージョンで開きます。
2. [ソリューション エクスプ ローラー] で、プロジェクト ノードのショートカット メニューを開き、**[プロパティ]** を選択し、表示されるダイアログ ボックスの **[アプリケーション]** タブを選択します。
   
    そのプロジェクトに関連付けられているツールのバージョンが **[アプリケーション]** タブに表示されます。 新しいバージョンの Azure Tools が表示された場合、そのプロジェクトは既にアップグレードされています。 タブに表示されたバージョンよりも新しいツールがインストールされている場合は、 **[アップグレード]** ボタンが表示されます。
3. **[アップグレード]** ボタンをクリックして、プロジェクトを新しいバージョンのツールにアップグレードします。
4. プロジェクトをビルドし、API の変更に起因するエラーがあれば解決してください。 新しいバージョンのコードを変更する方法については、特定の API に関するドキュメントを参照してください。




<!--HONumber=Nov16_HO3-->


