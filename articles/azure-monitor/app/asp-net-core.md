---
title: Azure Application Insights for ASP.NET Core | Microsoft Docs
description: ASP.NET Core Web アプリケーションの可用性、パフォーマンス、使用状況を監視します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: ae0d3658d9ae8534b1596fa7363495926cd0dfe7
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520753"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights for ASP.NET Core

Azure Application Insights では、Web アプリケーションをコード レベルまで詳細に監視できます。 ユーザーは、Web アプリケーションの可用性、パフォーマンス、利用状況を簡単に監視できます。 アプリケーションのエラーを、ユーザーからの報告を待つことなく、迅速に特定して診断することもできます。

この記事では、Visual Studio でサンプルの ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) アプリケーションを作成する手順について説明します。 また、Application Insights を使用して監視を開始する方法についても説明します。

## <a name="prerequisites"></a>前提条件

- NET Core 2.0.0 SDK 以降
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) バージョン 15.7.3 以降と、ASP.NET および Web 開発のワークロード

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Visual Studio で ASP.NET Core プロジェクトを作成する

1. **Visual Studio 2017** を右クリックし、**[管理者として実行]** を選択します。
2. **[ファイル]** > **[新規作成]** > **[プロジェクト]** の順に選択します (Ctrl + Shift + N)。

   ![Visual Studio の [新規作成]、[プロジェクト] メニューのスクリーンショット](./media/asp-net-core/001-new-project.png)

3. **[Visual C#]** を展開します。 **[.NET Core]** > **[ASP.NET Core Web アプリケーション]** を選択します。 プロジェクト名とソリューション名を入力し、**[新しい Git リポジトリの作成]** をオンにします。

   ![Visual Studio の新規プロジェクト ウィザードのスクリーンショット](./media/asp-net-core/002-asp-net-core-web-application.png)

4. **[.NET Core]** > **[ASP.NET Core 2.0** **Web アプリケーション]** > **[OK]** を選択します。

    ![Visual Studio の新規プロジェクト テンプレート選択のスクリーンショット](./media/asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Application Insights の検索

ASP.NET Core 2 以降がベースのプロジェクトを扱う Visual Studio 2015 Update 2 以降では、プロジェクトに明示的に Application Insights を追加する前であっても、[Application Insights の検索](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)を活用できます。

この機能をテストするには、以下の手順に従います。

1. アプリを実行します。 アプリを実行するには、**IIS Express** アイコンを選択します (![Visual Studio IIS Express アイコンのスクリーン ショット](./media/asp-net-core/004-iis-express.png))。

2. **[表示]** > **[その他のウィンドウ]** > **[Application Insights の検索]** を選択します。

   ![Visual Studio の診断ツール選択のスクリーンショット](./media/asp-net-core/005-view-other-windows-search.png)

3. デバッグ セッションのテレメトリは、現在、ローカル分析に対してのみ使用できます。 Application Insights を完全に有効にするには、右上隅にある **[Telemetry Readiness]** (テレメトリ準備) を選択するか、次のセクションに示されている手順を行います。

   ![Visual Studio の [Application Insights の検索] のスクリーンショット](./media/asp-net-core/006-search.png)

> [!NOTE]
> ASP.NET Core プロジェクトに Application Insights を追加する前に、[Application Insights の検索](../../azure-monitor/app/visual-studio.md)や [CodeLens](../../azure-monitor/app/visual-studio-codelens.md) のような機能を Visual Studio のローカルで有効にする方法の詳細については、「[Application Insights の検索 (続き)](#application-insights-search-continued)」を参照してください。

## <a name="add-application-insights-telemetry"></a>Application Insights Telemetry の追加

1. **[プロジェクト]** > **[Application Insights Telemetry の追加]** を選択します (または、**[接続済みサービス]** を右クリックし、**[接続済みサービスの追加]** を選択することができます)。

    ![Visual Studio の新規プロジェクト選択メニューのスクリーンショット](./media/asp-net-core/007-project-add-telemetry.png)

2. **[開始]** を選択します。 (このテキストは、お使いの Visual Studio のバージョンに応じて多少異なる場合があります。 以前のバージョンの一部には、この代わりに **[Start Free]** (自由に開始) ボタンがあります。)

    ![Application Insights の [開始] ボタンのスクリーンショット](./media/asp-net-core/008-get-started.png)

3. サブスクリプションを選択し、**[リソース]** > **[登録]** を選択します。

## <a name="changes-made-to-your-project"></a>プロジェクトに加えられた変更

Application Insights は低オーバーヘッドです。 Application Insights テレメトリを追加することでプロジェクトに加えられた変更を確認するには:

**[ビュー]** > **[チーム エクスプローラー]** (Ctrl+\, Ctrl+M) > **[プロジェクト]** > **[変更]** を選択します

- 合計 4 つの変更が表示されます。

  ![Application Insights を追加することによって変更されたファイルのスクリーンショット](./media/asp-net-core/009-changes.png)

- 1 つの新規ファイルが作成されています。

  - _ConnectedService.json_

    ```json
    {
     "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
     "Version": "8.12.10405.1",
     "GettingStartedDocument": {
       "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
     }
    }
    ```

- 3 つのファイルが変更されます (変更の強調表示のためにその他のコメントが追加される)。

  - _appsettings.json_

    ```json
    {
      "Logging": {
        "IncludeScopes": false,
        "LogLevel": {
          "Default": "Warning"
        }
      },
    // Changes to file post adding Application Insights Telemetry:
      "ApplicationInsights": {
        "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
      }
    }
    //
    ```

  - _ContosoDotNetCore.csproj_

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
      <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
        <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
    <!---->
      </PropertyGroup>
      <ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
    <!---->
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
      </ItemGroup>
      <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
      </ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    <!---->
    </Project>
    ```

  -  _Program.cs_

      ```csharp
      using System;
      using System.Collections.Generic;
      using System.IO;
      using System.Linq;
      using System.Threading.Tasks;
      using Microsoft.AspNetCore;
      using Microsoft.AspNetCore.Hosting;
      using Microsoft.Extensions.Configuration;
      using Microsoft.Extensions.Logging;

      namespace DotNetCore
      {
          public class Program
          {
              public static void Main(string[] args)
              {
                  BuildWebHost(args).Run();
              }

              public static IWebHost BuildWebHost(string[] args) =>
                  WebHost.CreateDefaultBuilder(args)
      // Change to file post adding Application Insights Telemetry:
                      .UseApplicationInsights()
      //
                      .UseStartup<Startup>()
                      .Build();
          }
      }
      ```

## <a name="send-ilogger-logs-to-application-insights"></a>ILogger ログを Application Insights に送信する

Application Insights では、ILogger を介して送信されるログのキャプチャがサポートされます。 ログ記録を設定するには、[こちら](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)のコード サンプルをご確認ください。

## <a name="synthetic-transactions-with-powershell"></a>PowerShell を使用した代理トランザクション

代理トランザクションを使用してアプリに対する要求を自動化するには、以下の手順に従います。

1. アプリを実行するには、 ![Visual Studio IIS Express アイコンのスクリーンショット](./media/asp-net-core/004-iis-express.png) アイコンを選択します。

2. ブラウザーのアドレス バーから URL をコピーします。 この URL は `http://localhost:<port number>` という形式になっています。

   ![ブラウザーのアドレス バー内の URL のスクリーンショット](./media/asp-net-core/0013-copy-url.png)

3. 次の PowerShell ループを実行して、テスト アプリを使用して 100 個の代理トランザクションを作成します。 `localhost:` の後のポート番号を、前の手順でコピーした URI と一致するように変更します。 例: 

   ```powershell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Application Insights ポータルを開く

前のセクションの PowerShell コマンドを実行したら、Application Insights を開いてトランザクションを表示し、データが収集されていることを確認します。 

Visual Studio のメニューで、**[プロジェクト]** > **[Application Insights]** > **[Application Insights ポータルを開く]** の順に選択します。

   ![Application Insights の概要のスクリーンショット](./media/asp-net-core/010-portal.png)

> [!NOTE]
> 前のスクリーンショットの例では、**[ライブ ストリーム]**、**[ページ ビューの読み込み時間]**、**[失敗した要求]** が収集されていません。 次のセクションでは、これらの各項目を追加する手順について説明します。 **[ライブ ストリーム]** と **[ページ ビューの読み込み時間]** を既に収集している場合は、**[失敗した要求]** の手順のみを実行してください。

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>失敗した要求、ライブ ストリーム、およびページ ビューの読み込み時間を収集する

### <a name="failed-requests"></a>失敗した要求

失敗した要求は技術的には収集されていますが、失敗した要求はまだ何も発生していません。 プロセスを高速化するには、既存のプロジェクトにカスタム例外を追加して、実際の例外をシミュレートします。 Visual Studio でアプリがまだ実行されている場合は、続行する前に **[デバッグの停止]** (Shift + F5) を選択します。

1. **ソリューション エクスプローラー**で、**[ページ]** > **[About.cshtml]** を展開し、*[About.cshtml.cs]* を開きます。

   ![Visual Studio ソリューション エクスプローラーのスクリーンショット](./media/asp-net-core/011-about.png)

2. ``Message=`` の下に例外を追加し、ファイルへの変更を保存します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Live Stream

ASP.NET Core を使用した Application Insights のライブ ストリーム機能にアクセスするには、Microsoft.ApplicationInsights.AspNetCore 2.2.0 NuGet パッケージを更新します。

Visual Studio で、**[プロジェクト]** > **[NuGet パッケージの管理]** > **[Microsoft.ApplicationInsights.AspNetCore]** > (バージョン) **[2.2.0]** > **[更新]** を選択します。

  ![NuGet パッケージ マネージャーのスクリーンショット](./media/asp-net-core/012-nuget-update.png)

確認のプロンプトが複数表示されます。 内容を読み、変更に同意する場合は受け入れます。

### <a name="page-view-load-time"></a>ページ ビューの読み込み時間

1. Visual Studio で、**[ソリューション エクスプローラー]** > **[ページ]** に移動します。 2 つのファイル、*Layout.cshtml* と *ViewImports.cshtml* を変更する必要があります。

2. *ViewImports.cshtml* で、次のコードを追加します。

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. *Layout.cshtml* で、``</head>`` タグの前 (他のどのスクリプトよりも前) に次のコードを追加します。

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>失敗した要求、ページ ビューの読み込み時間、およびライブ ストリームをテストする

テストし、すべてが機能していることを確認するには、以下の手順に従います。

1. アプリを実行します。 アプリを実行するには、 ![Visual Studio IIS Express アイコンのスクリーンショット](./media/asp-net-core/004-iis-express.png) アイコンを選択します。

2. **[バージョン情報]** ページに移動して、テスト例外をトリガーします (デバッグ モードの場合は、例外が Application Insights に表示されるように Visual Studio で **[続行]** を選択する必要があります)。

3. 前に使用したシミュレート済みの PowerShell トランザクション スクリプトを再実行します (場合によっては、スクリプトでポート番号を調整する必要があります)。

4. Applications Insights の **[概要]** ページがもう開いていない場合は、Visual Studio のメニューで **[プロジェクト]** > **[Application Insights]** > **[Application Insights ポータルを開く]** を選択します。 

   > [!TIP]
   > 新しいトラフィックが表示されていない場合は、**[時間の範囲]** の値をオンにし、**[更新]** を選択します。

   ![概要ウィンドウのスクリーンショット](./media/asp-net-core/0019-overview-updated.png)

5. **[ライブ ストリーム]** を選択します。

   ![ライブ メトリック ストリームのスクリーンショット](./media/asp-net-core/0020-live-metrics-stream.png)

   (PowerShell スクリプトがまだ実行されている場合は、ライブ メトリックが表示されます。 PowerShell スクリプトが停止している場合は、ライブ ストリームが開いた状態でスクリプトを再度実行します。)

## <a name="application-insights-sdk-comparison"></a>Application Insights SDK の比較

Application Insights の製品グループは、[完全な .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) と .NET Core SDK との間での機能の同等性を達成することに取り組んできました。 [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) for Application Insights の 2.2.0 リリースでは、機能の差が大幅に解消されています。

以下の表は、[.NET と .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) の相違点とトレードオフの詳細を示しています。

   | SDK の比較 | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **ライブ メトリック**      | **+** |**-** | **+** |
   | **サーバー テレメトリ チャネル** | **+** |**-** | **+**|
   |**アダプティブ サンプリング**| **+** | **-** | **+**|
   | **SQL 依存関係呼び出し**     | **+** |**-** | **+**|
   | **パフォーマンス カウンター*** | **+** | **-**| **-**|

このコンテキストでのパフォーマンス カウンターは、[サーバー側のパフォーマンス カウンター](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) (プロセッサ、メモリ、ディスク使用率など) を参照しています。

## <a name="open-source-sdk"></a>オープンソース SDK
[コードを読んで協力してください。](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Application Insights の検索 (続き)

このセクションは、Visual Studio で Application Insights の検索が ASP.NET Core 2 プロジェクトのためにどのように機能するかをより深く理解するのに役立ちます。 Application Insights NuGet パッケージを明示的にまだインストールしていない場合でも、この検索はそのような機能を持ちます。 デバッグ出力を調べると役立つ場合もあります。

_insight_ という言葉の出力を検索すると、次のような結果が強調表示されます。

```DebugOutput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

出力で、CoreCLR は以下の 2 つのアセンブリを読み込みます。 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_

Application Insights テレメトリの各インスタンス内にある _unconfigured_ 参照は、このアプリケーションが ikey と関連付けられていないことを示しています。 アプリの実行中に生成されるデータは、Azure に送信されません。 このデータは、ローカルでの検索と分析にのみ使用できます。

この機能が可能となっているのは、一部には、NuGet パッケージ _Microsoft.AspNetCore.All_ が依存関係として [_Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1) を取っているためです

![Microsoft.AspNETCore.all の NuGet 依存関係グラフのスクリーンショット](./media/asp-net-core/013-dependency.png)

Visual Studio 外部の VSCode または別のエディターで ASP.NET Core プロジェクトを編集していた場合、プロジェクトに Application Insights を明示的に追加していなければ、これらのアセンブリはデバッグ中に自動的に読み込まれません。

ただし Visual Studio で、ローカルの Application Insights 機能がこのように外部アセンブリから有効化されることは、[IHostingStartup インターフェイス](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1)の使用によって実現されています。 このインターフェイスは、デバッグ中に Application Insights を動的に追加します。

[IHostingStartup を使用して ASP.NET Core 内の外部アセンブリ](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1)からアプリを拡張する方法の詳細について学ぶことができます。 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Visual Studio .NET Core プロジェクト内で Application Insights を無効にする

Application Insights の検索機能の自動有効化は便利な場合もありますが、予期しないときにデバッグ テレメトリが生成されるのが表示されると、混乱することがあります。

テレメトリの生成を無効にするだけで十分な場合は、_Startup.cs_ ファイルの **configure** メソッドに次のコード ブロックを追加できます。

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR には、まだ _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ と _Microsoft.ApplicationInsights.AspNetCore.dll_ が読み込まれますが、これらのファイルは何も行いません。

Visual Studio .NET Core プロジェクトで Application Insights を完全に無効にする場合は、**[ツール]** > **[オプション]** > **[プロジェクトおよびソリューション]** > **[Web プロジェクト]** を選択する方法が推奨されます。 **[ASP.NET Core Web プロジェクトのローカル Application Insights を無効にする]** チェック ボックスをオンにします。 この機能は、Visual Studio 15.6 で追加されました。

![Visual Studio の [オプション] ウィンドウの [Web プロジェクト] 画面のスクリーン ショット](./media/asp-net-core/014-disable.png)

以前のバージョンの Visual Studio を実行していて、*IHostingStartup* によって読み込まれたすべてのアセンブリを完全に削除する場合は、以下の 2 つのオプションがあります。

* `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` を _Program.cs_ に追加します。

  ```csharp
  using System;
  using System.Collections.Generic;
  using System.IO;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.Configuration;
  using Microsoft.Extensions.Logging;

  namespace DotNetCore
  {
      public class Program
      {
          public static void Main(string[] args)
          {
              BuildWebHost(args).Run();
          }

          public static IWebHost BuildWebHost(string[] args) =>
              WebHost.CreateDefaultBuilder(args)
                  .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                  .UseStartup<Startup>()
                  .Build();
      }
  }
  ```

* ``"ASPNETCORE_preventHostingStartup": "True"`` を _launchSettings.json_ 環境変数に追加します。

これらの方法のいずれかを使用する場合の問題点として、Application Insights のみが無効になるわけではありません。 これらの方法では、Visual Studio 内で *IHostingStartup* の有効化機能を使用していたものがすべて無効になります。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>次の手順
* [ユーザー フローの探索](../../azure-monitor/app/usage-flows.md): ユーザーがアプリ内をどのように移動しているかを把握します。
* [スナップショット コレクションを構成](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)して、例外がスローされたときのソース コードと変数の状態を確認します。
* [API の使用](../../azure-monitor/app/api-custom-events-metrics.md) : アプリのパフォーマンスと使用の詳細を表示するための独自のイベントとメトリックスを送信します。
* [可用性テスト](../../azure-monitor/app/monitor-web-app-availability.md)の使用: 世界中からアプリを常にチェックします。
