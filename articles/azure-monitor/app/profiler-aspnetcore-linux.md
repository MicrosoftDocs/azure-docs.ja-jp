---
title: Application Insights Profiler を使用して ASP.NET Core Azure Linux Web アプリをプロファイルする | Microsoft Docs
description: Application Insights Profiler の使用方法についての概念の概要と、詳細な手順を説明したチュートリアルです。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.reviewer: mbullwin
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: cweining
ms.openlocfilehash: 35789cc1e516fb24d5e985e12b44fe3cd01b795d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790157"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Application Insights Profiler を使用して ASP.NET Core Azure Linux Web アプリをプロファイルする

現在、この機能はプレビュー段階にあります。

[Application Insights](../../azure-monitor/app/app-insights-overview.md) を使用するときにライブ Web アプリの各メソッドにどれくらい時間がかかっているかを確認できます。 Application Insights Profiler は現在、Azure App Service 上の Linux でホストされている ASP.NET Core Web アプリで利用できます。 このガイドでは、ASP.NET Core Linux Web アプリのためにプロファイラー トレースを収集するしくみについて段階的に説明します。

このチュートリアルを完了すると、図に示されているトレースのようなプロファイラー トレースをアプリで収集できるようになります。 この例では、プロファイラー トレースは、特定の Web 要求が待ち時間のために遅くなっていることを示しています。 アプリを遅くしているコード内の "*ホット パス*" には炎を模したアイコンが付きます。 **HomeController** セクションの **About** メソッドが **Thread.Sleep** 関数を呼び出しているため、Web アプリが遅くなっています。

![プロファイラー トレース](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>前提条件
以下の指示は、あらゆる Windows、Linux、Mac 開発環境に適用されます。

* [.NET Core SDK 2.1.2 以降](https://dotnet.microsoft.com/download/archives)をインストールします。
* 「[Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)」(はじめに - Git をインストールする) の指示に基づいて Git をインストールします。

## <a name="set-up-the-project-locally"></a>プロジェクトをローカルで設定する

1. お使いのコンピューターでコマンド プロンプト ウィンドウを開きます。 以下の指示は、あらゆる Windows、Linux、Mac 開発環境で有効です。

1. ASP.NET Core MVC Web アプリケーションを作成します。

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. 作業ディレクトリをプロジェクトのルート フォルダーに変更します。

1. プロファイラー トレースを収集する NuGet パッケージを追加します。

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Program.cs で Application Insights を有効にします。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Startup.cs でプロファイラーを有効にします。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. **HomeController.cs** セクションに無作為で数秒遅らせるためのコード行を追加します。

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```

1. 変更内容をローカル リポジトリに保存し、コミットします。

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>プロジェクトをホストする Linux Web アプリを作成する

1. App Service on Linux を使用して Web アプリ環境を作成します。

    ![Linux Web アプリを作成する](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. デプロイ資格情報を作成します。

    > [!NOTE]
    > 後で Web アプリをデプロイするときに使用するためにパスワードを記録しておきます。

    ![デプロイ資格情報を作成する](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. デプロイ オプションを選択します。 Azure Portal の指示に従い、Web アプリでローカル Git リポジトリを設定します。 Git リポジトリが自動的に作成されます。

    ![Git リポジトリを設定する](./media/profiler-aspnetcore-linux/setup-git-repo.png)

その他のデプロイ オプションについては、[この記事](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)を参照してください。

## <a name="deploy-your-project"></a>プロジェクトのデプロイ

1. コマンド プロンプト ウィンドウで、プロジェクトのルート フォルダーを参照します。 App Service のリポジトリを指すように Git リモート リポジトリを追加します。

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * デプロイ資格情報を作成するために使用した**ユーザー名**を使用します。
    * App Service on Linux を使用して Web アプリを作成するために使用した**アプリ名**を使用します。

2. Azure に変更をプッシュし、プロジェクトをデプロイします。

    ```
    git push azure master
    ```

次の例のような出力が表示されます。

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Application Insights を追加して Web アプリを監視する

1. [Application Insights リソースを作成します](./../../azure-monitor/app/create-new-resource.md )。

2. Application Insights リソースの **iKey** 値をコピーし、自分の Web アプリで次のように設定します。

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    アプリ設定を変更すると、サイトは自動的に再起動します。 新しい設定が適用されると、すぐにプロファイラーが 2 分間実行されます。 その後、プロファイラーは 1 時間ごとに 2 分間実行されます。

3. Web サイトにいくらかのトラフィックを生成させます。 サイトの **[About]** ページを何回か更新することで、トラフィックを生成できます。

4. イベントを 2 から 5 分間待機して、Application Insights に集計します。

5. Azure Portal で Application Insights の **[パフォーマンス]** ウィンドウを参照します。 ウィンドウの右下でプロファイラー トレースを確認できます。

    ![プロファイラー トレースを表示する](./media/profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>既知の問題

### <a name="profile-now-button-doesnt-work-for-linux-profiler"></a>Linux プロファイラーで、[今すぐプロファイル] ボタンが機能しません。
Linux バージョンの App Insights プロファイラーで、[今すぐプロファイル] ボタンを使用したオンデマンドのプロファイリングがまだサポートされていません。


## <a name="next-steps"></a>次の手順
Azure App Service でホストされているカスタム コンテナーを使用する場合は、[コンテナー化された ASP.NET Core アプリケーションのサービス プロファイラーを有効にする方法](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp)に関するページの手順で Application Insight Profiler を有効にします。

問題やご提案があれば、次の Application Insights GitHub リポジトリにご報告ください:[ApplicationInsights-Profiler-AspNetCore:Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues)。
