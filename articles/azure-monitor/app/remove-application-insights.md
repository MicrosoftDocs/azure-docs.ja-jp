---
title: Visual Studio での Application Insights の削除 - Azure Monitor
description: Visual Studio で ASP.NET 用および ASP.NET Core 用 Application Insights SDK を削除する方法。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1d70413fa6a47e2d41693db6eb705f31b2a2b1b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704312"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Visual Studio で Application Insights を削除する方法

この記事では、Visual Studio で ASP.NET 用および ASP.NET Core 用 Application Insights SDK を削除する方法について説明します。

Application Insights を削除するには、ご利用のアプリケーション内の API から NuGet パッケージと参照を削除する必要があります。 Visual Studio で Package Management コンソール、または NuGet ソリューションの管理を使用して、NuGet パッケージをアンインストールできます。 次のセクションでは、NuGet パッケージを削除する 2 つの方法と、ご利用のプロジェクトに自動的に追加された内容について説明します。 追加されたファイルと、API への呼び出しを行った独自のコード内の領域が削除されていることを必ず確認してください。

## <a name="uninstall-using-the-package-management-console"></a>Package Management コンソールを使用してアンインストールする

# <a name="net"></a>[.NET](#tab/net)

1. Package Management コンソールを開くには、上部のメニューで [ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール] の順に選択します。
     
    ![上部のメニューで、[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール] の順にクリックします](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > トレース コレクションが有効になっている場合は、Microsoft.ApplicationInsights.TraceListener を最初にアンインストールする必要があります。 「`Uninstall-package Microsoft.ApplicationInsights.TraceListener`」と入力してから、次の手順に従って、Microsoft.ApplicationInsights.Web を削除します。

1. 次のコマンドを入力します。`Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    コマンドを入力すると、Application Insights パッケージとそのすべての依存関係がプロジェクトからアンインストールされます。
    
    ![コンソールにコマンドを入力する](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Package Management コンソールを開くには、上部のメニューで [ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール] の順に選択します。

    ![上部のメニューで、[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール] の順にクリックします](./media/remove-application-insights/package-manager.png)

1. 次のコマンドを入力します。` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    コマンドを入力すると、Application Insights パッケージとそのすべての依存関係がプロジェクトからアンインストールされます。

---

## <a name="uninstall-using-the-visual-studio-nuget-ui"></a>Visual Studio NuGet UI を使用してアンインストールする

# <a name="net"></a>[.NET](#tab/net)

1. 右側の *ソリューション エクスプローラー* で、 **[ソリューション]** を右クリックし、 **[ソリューションの NuGet パッケージの管理]** を選択します。

    その後、プロジェクトに含まれるすべての NuGet パッケージを編集するための画面が表示されます。
    
     ![ソリューション エクスプローラーで、[ソリューション] を右クリックしてから、[ソリューションの NuGet パッケージの管理] を選択します。](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > トレース コレクションが有効になっている場合は、まず、[依存関係の削除] が選択されていない状態で Microsoft.ApplicationInsights.TraceListener をアンインストールし、次に、以下の手順に従って、[依存関係の削除] が選択された状態で Microsoft.ApplicationInsights.Web をアンインストールする必要があります。

1. **Microsoft.ApplicationInsights.Web** パッケージをクリックします。 右側で、 **[プロジェクト]** の横にあるチェックボックスをオンにして、すべてのプロジェクトを選択します。

1. アンインストール時にすべての依存関係を削除するには、プロジェクトを選択したセクションの下にある **[オプション]** ドロップダウン ボタンを選択します。

    *[アンインストール オプション]* で、 *[依存関係の削除]* の横にあるチェックボックスをオンにします。

1. **[アンインストール]** を選択します。
    
    ![[依存関係の削除] がチェックされ、アンインストールが強調表示された Microsoft.ApplicationInsights.Web ウィンドウが表示されたスクリーンショット。](./media/remove-application-insights/uninstall-framework.png)

    アプリケーションから削除されるすべての依存関係を示すダイアログボックスが表示されます。 **[OK]** を選択してアンインストールします。
    
    ![削除する依存関係が記載されたダイアログ ボックスが表示されたスクリーンショット。](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  すべてのものがアンインストールされた後も、*ソリューション エクスプローラー* に "ApplicationInsights.config" と "AiHandleErrorAttribute.cs" が表示される場合があります。  この 2 つのファイルは手動で削除できます。

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. 右側の *ソリューション エクスプローラー* で、 **[ソリューション]** を右クリックし、 **[ソリューションの NuGet パッケージの管理]** を選択します。

    その後、プロジェクトに含まれるすべての NuGet パッケージを編集するための画面が表示されます。

    ![ソリューション エクスプローラーで、[ソリューション] を右クリックしてから、[ソリューションの NuGet パッケージの管理] を選択します。](./media/remove-application-insights/manage-nuget-core.png)

1. "Microsoft.ApplicationInsights.AspNetCore" パッケージをクリックします。 右側で、 *[プロジェクト]* の横にあるチェックボックスをオンにして、すべてのプロジェクトを選択し、さらに **[アンインストール]** を選択します。

    ![依存関係の削除をオンにしてからアンインストールする](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Application Insights を追加すると作成される内容

ご利用のプロジェクトに Application Insights を追加すると、ファイルが作成され、ファイルの一部にコードが追加されます。 NuGet パッケージをアンインストールしても、そのファイルとコードが必ず破棄されるとは限りません。 Application Insights を完全に削除するには、プロジェクトに追加した API 呼び出しと共に、追加したコードまたはファイルを確認し、それらを手動で削除する必要があります。

# <a name="net"></a>[.NET](#tab/net)

Visual Studio ASP.NET プロジェクトに Application Insights Telemetry を追加すると、次のファイルが追加されます。

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

次のコードが追加されます。

- [自分のプロジェクトの名前].csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout.cshtml

    プロジェクトに Layout.cshtml ファイルが含まれている場合は、次のコードが追加されます。
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Visual Studio ASP.NET Core テンプレート プロジェクトに Application Insights Telemetry を追加すると、次のコードが追加されます。

- [自分のプロジェクトの名前].csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.json:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>次のステップ

- [Azure Monitor](../overview.md)
