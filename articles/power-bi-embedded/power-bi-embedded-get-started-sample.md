---
title: "サンプルの使用"
description: "Power BI Embedded、SDK を使用して対話型の Power BI レポートをビジネス インテリジェンス アプリケーションに追加する"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/02/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: beadcbfa4907d68a687ec5144136132d8b0439e1
ms.lasthandoff: 03/29/2017


---
# <a name="get-started-with-power-bi-embedded-sample"></a>Power BI Embedded の使用を開始するためのサンプル

**Microsoft Power BI Embedded**を使用すると、Web アプリケーションやモバイル アプリケーションに Power BI レポートをすぐに統合することができます。 この記事では、 **Power BI Embedded** の使用を開始するためのサンプルを紹介します。

先に進む前に、次のリソースを保存することができます。 これらのリソースは、サンプル アプリや独自のアプリに Power BI レポートを統合する際に役立ちます。

* [サンプル ワークスペース Web アプリ](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI Embedded API reference](https://msdn.microsoft.com/en-US/library/azure/mt711507.aspx)
* [Power BI Embedded .NET SDK (NuGet から入手可能) ](http://go.microsoft.com/fwlink/?LinkId=746472)
* [JavaScript レポート埋め込みサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE] 
> Power BI Embedded の使用を開始するためのサンプルを構成して実行するには、Azure サブスクリプションで少なくとも 1 つの **ワークスペース コレクション** 作成する必要があります。 Azure ポータルで **ワークスペース コレクション** を作成する方法については、 [Power BI Embedded の概要](power-bi-embedded-get-started.md)に関する記事をご覧ください。

## <a name="configure-the-sample-app"></a>サンプル アプリの構成

サンプル アプリの実行に必要なコンポーネントにアクセスするための Visual Studio 開発環境の設定手順を説明します。

1. GitHub でサンプル [Power BI Embedded - Integrate a report into a web app](http://go.microsoft.com/fwlink/?LinkId=761493) をダウンロードして解凍します。
2. Visual Studio で **PowerBI-embedded.sln** を開きます。 NuGet パッケージ マネージャー コンソールで **Update-Package** コマンドを実行して、このソリューションで使用するパッケージを更新する必要のある場合があります。
3. ソリューションをビルドします。
4. **ProvisionSample** コンソール アプリを実行します。 サンプルのコンソール アプリで、ワークスペースのプロビジョニングを行って、PBIX ファイルをインポートします。
5. 新しい**ワークスペース**をプロビジョニングするには、オプション 2 の **[Report management (レポート管理)]** を選択し、オプション 3 の **[Import PBIX Desktop file into a workspace (ワークスペースに PBIX デスクトップ ファイルをインポートする)]** を選択します。

6. **ワークスペース コレクション**名と**アクセス キー**を入力します。 これらは **Azure ポータル**で確認できます。 **アクセス キー**を取得する方法の詳細については、「Microsoft Power BI Embedded の概要」の「 [Power BI API のアクセス キーの表示](power-bi-embedded-get-started.md#view-power-bi-api-access-keys) 」をご覧ください。

    ![](media/powerbi-embedded-get-started-sample/azure-portal.png)
7. 新しく作成された **ワークスペース ID** を、この記事の後半で使用するためにコピーして保存します。 作成された**ワークスペース ID** は **Azure Portal** で確認できます。

    ![](media/powerbi-embedded-get-started-sample/workspace-id.png)
8. PBIX ファイルを**ワークスペース**にインポートするために、オプション **[6.Import PBIX Desktop file into an existing workspace]** を選択します。 PBIX ファイルがない場合は、[Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547) をダウンロードできます。
9. メッセージが表示されたら、分かりやすい **データセット**名を入力します。

次のように、応答が表示されます。

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> PBIX ファイルに直接クエリ接続が含まれている場合は、接続文字列を更新するオプション 7 を実行します。

これで、Power BI PBIX レポートを**ワークスペース**にインポートできました。 次に、**Power BI Embedded** の使用を開始するためのサンプル Web アプリを実行する方法を説明します。

## <a name="run-the-sample-web-app"></a>サンプル Web アプリの実行
サンプル Web アプリは、**ワークスペース**にインポートされたレポートを表示するサンプル アプリケーションです。 サンプル Web アプリを構成する方法は以下のとおりです。

1. **PowerBI-embedded** Visual Studio ソリューションで **EmbedSample** Web アプリケーションを右クリックして、**[スタートアップ プロジェクトに設定]** を選択します。
2. **EmbedSample** Web アプリケーションの **web.config** で、**[appSettings]** の **[AccessKey]**、**[WorkspaceCollection]** の名前、および **[WorkspaceId]** を編集します。

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. **EmbedSample** Web アプリケーションを実行します。

**EmbedSample** Web アプリケーションを実行すると、左側のナビゲーション パネルに **[レポート]** メニューが表示されます。 インポートしたレポートを表示するには、**[レポート]** を展開して、レポートをクリックします。 [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547) をインポートした場合、サンプル Web アプリは次のように表示されます。

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-sample-left-nav.png)

レポートをクリックすると、**EmbedSample** Web アプリケーションは次のように表示されます。

![](media/powerbi-embedded-get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>サンプル コードの検証

**Microsoft Power BI Embedded** のサンプルは、アプリへの **Power BI** レポートの統合方法を示すサンプル Web アプリです。 このサンプルは、モデル ビュー コント ローラー (MVC) デザイン パターンを使用して、ベスト プラクティスを説明します。 このセクションでは、サンプル コードのうち、**PowerBI-embedded** Web アプリ ソリューション内で検証できる部分について説明します。 モデル ビュー コント ローラー (MVC) パターンは、ユーザー入力に基づいて、ドメイン、プレゼンテーション、およびアクションのモデリングをモデル、ビュー、およびコントロールという 3 つの個別クラスに分割します。 MVC の詳細については、[ASP.NET の詳細](http://www.asp.net/mvc)に関するページをご覧ください。

**Microsoft Power BI Embedded** のサンプル コードは以下のように区切られています。 サンプル内のコードを簡単に見つけることができるように、各セクションには PowerBI-embedded.sln ソリューションのファイル名が含まれます。

> [!NOTE]
> このセクションは、コードの作成方法を説明するサンプル コードの概要です。 完全なサンプルを表示するには、Visual Studio で PowerBI-embedded.sln ソリューションを読み込んでください。

### <a name="model"></a>モデル

サンプルには、**ReportsViewModel** と **ReportViewModel** があります。

**ReportsViewModel.cs**: 複数の Power BI レポートを表します。

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**： 1 つの Power BI レポートを表します。

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>接続文字列

接続文字列は次の形式にする必要があります。

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

一般的なサーバーとデータベースの属性を使用すると失敗します。 たとえば、Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase を使用した場合です。

### <a name="view"></a>表示

**ビュー**では、複数の Power BI **レポート** の表示と 1 つの Power BI **レポート**の表示を管理します。

**Reports.cshtml**: **ActionLink** を作成するために **Model.Reports** を反復処理します。 **ActionLink** の構成は次のとおりです。

| パーツ | 説明 |
| --- | --- |
| タイトル |レポートの名前。 |
| QueryString |レポート ID へのリンク。 |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: **Model.AccessToken** と、**PowerBIReportFor** のラムダ式を設定します。

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Controller

**DashboardController.cs**: **アプリ トークン**を渡す PowerBIClient を作成します。 **資格情報**を取得するために、JSON Web トークン (JWT) が**署名キー**から生成されます。 **資格情報**は、**PowerBIClient** のインスタンスの作成に使用されます。 **PowerBIClient** のインスタンスを作成したら、GetReports() と GetReportsAsync() を呼び出すことができます。

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### <a name="integrate-a-report-into-your-app"></a>アプリへのレポートの統合

**レポート**を作成したら、**IFrame** を使用して Power BI **レポート**を埋め込みます。 **Microsoft Power BI Embedded** サンプルの powerbi.js のコード スニペットを次に示します。

![](media/powerbi-embedded-get-started-sample/power-bi-embedded-iframe-code.png)

## <a name="filter-reports-embedded-in-your-application"></a>アプリケーションに埋め込まれたレポートのフィルター

URL 構文を使用して、埋め込みレポートをフィルター処理できます。 そのためには、フィルターを指定した iFrame の src URL に、**eq** 演算子を使用した **$filter** クエリ文字列パラメーターを追加します。 フィルター クエリ構文を次に示します。

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} には、スペースや特殊文字を含めることはできません。 {fieldValue} は単一のカテゴリ値を受け入れます。  

## <a name="see-also"></a>関連項目

[Common Microsoft Power BI Embedded scenarios (Microsoft Power BI Embedded の一般的なシナリオ)](power-bi-embedded-scenarios.md)  
[Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)  
[レポートの埋め込み](power-bi-embedded-embed-report.md)  
[データセットからの新しいレポートの作成](power-bi-embedded-create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
ご質問は、 [Power BI コミュニティ](http://community.powerbi.com/)で尋ねてみてください。

