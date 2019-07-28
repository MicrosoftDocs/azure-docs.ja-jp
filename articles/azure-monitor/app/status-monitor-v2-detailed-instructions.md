---
title: Azure Status Monitor v2 の詳細な手順 | Microsoft Docs
description: Status Monitor v2 を使い始めるための詳細な手順です。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
services: application-insights
documentationcenter: .net
author: ms-timothymothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: c8199c960229f9cc53cf57f9da3e1f17ebd9f5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074155"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Status Monitor v2:詳しい手順

この記事では、ApplicationMonitor モジュールを PowerShell ギャラリーにオンボードしてダウンロードする方法を詳しく説明します。
始めるために必要になる最も一般的なパラメーターについて説明します。
インターネットにアクセスできない場合のための手動の説明も含まれています。

> [!IMPORTANT]
> 現在、Status Monitor v2 はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 一部の機能は、サポートされていなかったり、制限されていたりする場合があります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="get-an-instrumentation-key"></a>インストルメンテーション キーを取得する

開始するには、インストルメンテーション キーが必要です。 詳細については、「[Application Insights リソースの作成](create-new-resource.md#copy-the-instrumentation-key)」を参照してください。

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>管理者として昇格された実行ポリシーで PowerShell を実行します。

**管理者として実行**

PowerShell でコンピューターに対して変更を行うには、管理者レベルのアクセス許可が必要です。

**実行ポリシー**
- 説明:既定では、PowerShell スクリプトの実行は無効になっています。 RemoteSigned スクリプトは現在のスコープに対してのみ許可することをお勧めします。
- リファレンス: 「[About Execution Policies (実行ポリシーの概要)](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)」と「[Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)」
- コマンド: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`。
- 省略可能なパラメーター:
    - `-Force` 確認プロンプトをバイパスします。

**エラーの例**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell の前提条件

`$PSVersionTable` コマンドを実行して、PowerShell のインスタンスを監査します。
このコマンドでは次の出力が生成されます。


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

これらの手順は、Windows 10 および上記のバージョンを実行しているコンピューター上で作成およびテストされています。

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell ギャラリーの前提条件

次の手順では、PowerShell ギャラリーからモジュールをダウンロードするようにサーバーを準備します。

> [!NOTE] 
> PowerShell ギャラリーは、Windows 10、Windows Server 2016、および PowerShell 6 でサポートされています。
> 以前のバージョンについては、「[PowerShellGet のインストール](https://docs.microsoft.com/powershell/gallery/installing-psget)」を参照してください。


1. 管理者として昇格された実行ポリシーで PowerShell を実行します。
2. NuGet パッケージ プロバイダーをインストールします。
    - 説明:PowerShellGallery などの NuGet ベースのリポジトリとやり取りするには、このプロバイダーが必要です
    - リファレンス: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)。
    - コマンド: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`。
    - 省略可能なパラメーター:
        - `-Proxy` 要求用のプロキシ サーバーを指定します。
        - `-Force` 確認プロンプトをバイパスします。
    
    NuGet が設定されていない場合、次のプロンプトを受け取ります。
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. PowerShell ギャラリーを信頼できるリポジトリとして構成します。
    - 説明:既定では、PowerShell ギャラリーは信頼されていないリポジトリです。
    - リファレンス: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)。
    - コマンド: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`。
    - 省略可能なパラメーター:
        - `-Proxy` 要求用のプロキシ サーバーを指定します。

    PowerShell ギャラリーが信頼されていない場合は、次のプロンプトを受け取ります。

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    `Get-PSRepository` コマンドを実行することにより、この変更を確認して、すべての PSRepositories を監査できます。

4. PowerShellGet の最新バージョンをインストールします。
    - 説明:このモジュールには、PowerShell ギャラリーから他のモジュールを取得するために使用されるツールが含まれます。 バージョン 1.0.0.1 は、Windows 10 と Windows Server に付属しています。 バージョン 1.6.0 以降が必要です。 インストールされているバージョンを確認するには、コマンド `Get-Command -Module PowerShellGet` を実行します。
    - リファレンス: 「[PowerShellGet のインストール](https://docs.microsoft.com/powershell/gallery/installing-psget)」。
    - コマンド: `Install-Module -Name PowerShellGet`。
    - 省略可能なパラメーター:
        - `-Proxy` 要求用のプロキシ サーバーを指定します。
        - `-Force` "インストール済み" の警告をバイパスして、最新バージョンをインストールします。

    最新バージョンの PowerShellGet を使っていない場合、次のエラーを受け取ります。
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. PowerShell を再起動します。 現在のセッションで新しいバージョンを読み込むことはできません。 新しい PowerShell セッションでは、最新バージョンの PowerShellGet が読み込まれます。

## <a name="download-and-install-the-module-via-powershell-gallery"></a>PowerShell ギャラリーからモジュールをダウンロードしてインストールする

次の手順では、PowerShell ギャラリーから Az.ApplicationMonitor モジュールをダウンロードします。

1. PowerShell ギャラリーのすべての前提条件が満たされていることを確認します。
2. 管理者として昇格された実行ポリシーで PowerShell を実行します。
3. Az.ApplicationMonitor モジュールをインストールします。
    - リファレンス: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)。
    - コマンド: `Install-Module -Name Az.ApplicationMonitor`。
    - 省略可能なパラメーター:
        - `-Proxy` 要求用のプロキシ サーバーを指定します。
        - `-AllowPrerelease` アルファ リリースとベータ リリースのインストールを許可します。
        - `-AcceptLicense` "ライセンス条項に同意する" のプロンプトをバイパスします
        - `-Force` "信頼されていないリポジトリ" の警告をバイパスします。

## <a name="download-and-install-the-module-manually-offline-option"></a>モジュールを手動でダウンロードしてインストールする (オフライン オプション)

何らかの理由で、PowerShell モジュールに接続できない場合は、Az.ApplicationMonitor モジュールを手動でダウンロードしてインストールすることができます。

### <a name="manually-download-the-latest-nupkg-file"></a>最新の nupkg ファイルを手動でダウンロードする

1. https://www.powershellgallery.com/packages/Az.ApplicationMonitor にアクセスします。
2. 「**Version History (バージョン履歴)** 」一覧から最新バージョンのファイルを選択します。
3. 「**Installation Options (インストール オプション)** 」の「**Manual Download (手動ダウンロード)** 」を選択します。

### <a name="option-1-install-into-a-powershell-modules-directory"></a>オプション 1:PowerShell モジュールのディレクトリにインストールする
手動でダウンロードした PowerShell モジュールを PowerShell ディレクトリにインストールし、PowerShell セッションで検出されるようにします。
詳細については、「[PowerShell モジュールをインストールする](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)」を参照してください。


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Expand-Archive (v1.0.1.0) を使用して zip ファイルとして nupkg を解凍する

- 説明:Microsoft.PowerShell.Archive (v1.0.1.0) の基本バージョンでは、nupkg ファイルを解凍できません。 ファイル名を .zip 拡張子に変更します。
- リファレンス: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)。
- コマンド:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Expand-Archive (v1.1.0.0) を使用して nupkg を解凍する

- 説明:拡張子を変更せずに nupkg ファイルを解凍するには、最新バージョンの Expand-Archive を使用します。
- リファレンス: 「[Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)」と「[Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)」。
- コマンド:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>オプション 2:手動で nupkg を解凍してインポートする
手動でダウンロードした PowerShell モジュールを PowerShell ディレクトリにインストールし、PowerShell セッションで検出されるようにします。
詳細については、「[PowerShell モジュールをインストールする](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)」を参照してください。

他のディレクトリにモジュールをインストールする場合は、[Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6) を使ってモジュールを手動でインポートします。

> [!IMPORTANT] 
> DLL は相対パスでインストールされます。
> このパッケージの内容を目的のランタイム ディレクトリに格納し、アクセス許可で読み取りは許可され、書き込みは許可されないことを確認します。

1. 拡張子を ".zip" に変更し、パッケージの内容を目的のインストール ディレクトリに抽出します。
2. Az.ApplicationMonitor.psd1 のファイル パスを検索します。
3. 管理者として昇格された実行ポリシーで PowerShell を実行します。
4. コマンド `Import-Module Az.ApplicationMonitor.psd1` を使用してモジュールを読み込みます。
    

## <a name="route-traffic-through-a-proxy"></a>プロキシを経由するトラフィックのルーティング

プライベート イントラネット上のコンピューターを監視するときは、プロキシ経由で HTTP トラフィックをルーティングする必要があります。

Az.ApplicationMonitor を PowerShell ギャラリーからダウンロードしてインストールする PowerShell コマンドでは、`-Proxy` パラメーターがサポートされています。
インストール スクリプトを作成するときは、上記の手順を確認します。

Application Insights SDK では、アプリのテレメトリを Microsoft に送信する必要があります。 web.config ファイルでアプリのプロキシ設定を構成することをお勧めします。 詳細については、「[Application Insights の FAQ」の「プロキシのパススルー](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)」を参照してください。


## <a name="enable-monitoring"></a>監視を有効にする

モニターを有効にするには `Enable-ApplicationInsightsMonitoring` コマンドを使用します。

このコマンドレットの使用方法の詳細については、[API リファレンス](status-monitor-v2-api-enable-monitoring.md)に関する記事を参照してください。



## <a name="next-steps"></a>次の手順

 テレメトリの表示:

- パフォーマンスと使用状況を監視するための[メトリックを探索](../../azure-monitor/app/metrics-explorer.md)します。
- 問題を診断するために[イベントとログを検索](../../azure-monitor/app/diagnostic-search.md)します。
- より高度なクエリのために[分析を使用](../../azure-monitor/app/analytics.md)します。
- [ダッシュボードを作成](../../azure-monitor/app/overview-dashboard.md)します。

 テレメトリの追加:

- サイトがライブの状態であることを確認するために [Web テストを作成](monitor-web-app-availability.md)します。
- Web ページ コードからの例外を参照してトレースの呼び出しを有効にするために、[Web クライアント テレメトリ](../../azure-monitor/app/javascript.md)を追加します。
- トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加](../../azure-monitor/app/asp-net.md)します。

Status Monitor v2 の活用:

- ガイドを使用して、Status Monitor v2 の[トラブルシューティング](status-monitor-v2-troubleshoot.md)を行います。
