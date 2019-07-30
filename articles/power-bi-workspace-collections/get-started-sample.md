---
title: サンプルの使用
description: この記事では、Power BI ワークスペース コレクションの使用を開始するためのサンプルを紹介します。
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 9b45dd6bcc9aa3abf2d5078b54e51531cae4ec5f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669114"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Power BI ワークスペース コレクションのサンプルの使用

**Microsoft Power BI ワークスペース コレクション**を使用すると、Web アプリケーションまたはモバイル アプリケーションに Power BI レポートをすぐに統合することができます。 この記事では、**Power BI ワークスペース コレクション**の使用を開始するためのサンプルを紹介します。

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

先に進む前に、次のリソースを保存できます。それらは、サンプル アプリや独自のアプリに Power BI レポートを統合する際に役立ちます。

* [サンプル ワークスペース Web アプリ](https://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI ワークスペース コレクション API リファレンス](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Power BI .NET SDK](https://go.microsoft.com/fwlink/?LinkId=746472) (NuGet から入手可能)
* [JavaScript レポート埋め込みサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Power BI ワークスペース コレクションの使用を開始するためのサンプルを構成して実行するには、Azure サブスクリプションで少なくとも 1 つの**ワークスペース コレクション**を作成しておく必要があります。 Azure Portal で**ワークスペース コレクション**を作成する方法については、[Power BI ワークスペース コレクションの概要](get-started.md)に関する記事をご覧ください。

## <a name="configure-the-sample-app"></a>サンプル アプリの構成

サンプル アプリの実行に必要なコンポーネントにアクセスするための Visual Studio 開発環境の設定手順を説明します。

1. GitHub でサンプル [Power BI ワークスペース コレクション - Web アプリへのレポートの統合](https://go.microsoft.com/fwlink/?LinkId=761493)をダウンロードして解凍します。
2. Visual Studio で **PowerBI-embedded.sln** を開きます。 NuGet パッケージ マネージャー コンソールで **Update-Package** コマンドを実行して、このソリューションで使用するパッケージを更新する必要がある場合があります。
3. ソリューションをビルドします。
4. **ProvisionSample** コンソール アプリを実行します。 サンプルのコンソール アプリで、ワークスペースのプロビジョニングを行って、PBIX ファイルをインポートします。
5. 新しい**ワークスペース**をプロビジョニングするには、オプション 1 の **[Collection management (コレクション管理)]** を選択し、オプション 6 の **[Provision a new Workspace (新しいワークスペースをプロビジョニングする)]** を選択します。
6. 新しい**レポート**をインポートするには、オプション 2 の **[Report management (レポート管理)]** を選択し、オプション 3 の **[Import PBIX Desktop file into a workspace (ワークスペースに PBIX デスクトップ ファイルをインポートする)]** を選択します。

7. **ワークスペース コレクション**名と**アクセス キー**を入力します。 これらは **Azure Portal** で確認できます。 **アクセス キー**を取得する方法の詳細については、「Microsoft Power BI Embedded の概要」の「 [Power BI API のアクセス キーの表示](get-started.md#view-power-bi-api-access-keys) 」をご覧ください。

    ![Azure Portal 内のアクセス キー](media/get-started-sample/azure-portal.png)
8. 新しく作成された **ワークスペース ID** を、この記事の後半で使用するためにコピーして保存します。 作成された**ワークスペース ID** は **Azure Portal** で確認できます。

    ![Azure Portal 内のワークスペース ID](media/get-started-sample/workspace-id.png)
9. PBIX ファイルを**ワークスペース**にインポートするために、オプション **[6.Import PBIX Desktop file into an existing workspace]** を選択します。 PBIX ファイルがない場合は、[Retail Analysis Sample PBIX](https://go.microsoft.com/fwlink/?LinkID=780547) をダウンロードできます。
10. メッセージが表示されたら、分かりやすい **データセット**名を入力します。

次のように、応答が表示されます。

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> PBIX ファイルに直接クエリ接続が含まれている場合は、接続文字列を更新するオプション 7 を実行します。

これで、Power BI PBIX レポートを**ワークスペース**にインポートできました。 次に、**Power BI ワークスペース コレクション**の使用を開始するためのサンプル Web アプリを実行する方法について説明します。

## <a name="run-the-sample-web-app"></a>サンプル Web アプリの実行

サンプル Web アプリは、**ワークスペース**にインポートされたレポートを表示するサンプル アプリケーションです。 サンプル Web アプリを構成する方法は以下のとおりです。

1. **PowerBI-embedded** Visual Studio ソリューションで **EmbedSample** Web アプリケーションを右クリックして、 **[スタートアップ プロジェクトに設定]** を選択します。
2. **web.config** の **EmbedSample** Web アプリケーションで、次の **appSettings** を編集します:**AccessKey**、**WorkspaceCollection** 名、および **WorkspaceId**。

    ```xml
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. **EmbedSample** Web アプリケーションを実行します。

**EmbedSample** Web アプリケーションを実行すると、左側のナビゲーション パネルに **[レポート]** メニューが表示されます。 インポートしたレポートを表示するには、 **[レポート]** を展開して、レポートをクリックします。 [Retail Analysis Sample PBIX](https://go.microsoft.com/fwlink/?LinkID=780547) をインポートした場合、サンプル Web アプリは次のように表示されます。

![サンプル アプリケーション内の左側のナビゲーション](media/get-started-sample/sample-left-nav.png)

レポートをクリックすると、**EmbedSample** Web アプリケーションは次のように表示されます。

![アプリケーション内で表示されるレポートのサンプル](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>サンプル コードの検証

**Microsoft Power BI ワークスペース コレクション**のサンプルは、アプリへの **Power BI** レポートの統合方法を示すサンプル Web アプリです。 このサンプルは、モデル ビュー コント ローラー (MVC) デザイン パターンを使用して、ベスト プラクティスを説明します。 このセクションでは、サンプル コードのうち、**PowerBI-embedded** Web アプリ ソリューション内で検証できる部分について説明します。 Model-View-Controller (MVC) パターンは、ユーザー入力に基づいて、ドメイン、プレゼンテーション、およびアクションのモデリングをモデル、ビュー、およびコントロールという 3 つの個別クラスに分割します。 MVC の詳細については、[ASP.NET の詳細](https://www.asp.net/mvc)に関するページをご覧ください。

**Microsoft Power BI ワークスペース コレクション**のサンプル コードは以下のように区切られています。 サンプル内のコードを簡単に見つけることができるように、各セクションには PowerBI-embedded.sln ソリューションのファイル名が含まれます。

> [!NOTE]
> このセクションは、コードの作成方法を説明するサンプル コードの概要です。 完全なサンプルを表示するには、Visual Studio で PowerBI-embedded.sln ソリューションを読み込んでください。

### <a name="model"></a>モデル

サンプルには、**ReportsViewModel** と **ReportViewModel** があります。

**ReportsViewModel.cs**:複数の Power BI レポートを表します。

```csharp
public class ReportsViewModel
{
    public List<Report> Reports { get; set; }
}
```

**ReportViewModel.cs**:1 つの Power BI レポートを表します。

```csharp
public class ReportViewModel
{
    public IReport Report { get; set; }

    public string AccessToken { get; set; }
}
```

### <a name="connection-string"></a>接続文字列

接続文字列は次の形式にする必要があります。

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

一般的なサーバーとデータベースの属性を使用すると失敗します。 例: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>表示

**ビュー**では、複数の Power BI **レポート** の表示と 1 つの Power BI **レポート**の表示を管理します。

**Reports.cshtml**:**ActionLink** を作成するために **Model.Reports** を反復処理します。 **ActionLink** の構成は次のとおりです。

| パーツ | 説明 |
| --- | --- |
| タイトル |レポートの名前。 |
| QueryString |レポート ID へのリンク。 |
```cshtml
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
```
Report.cshtml:**Model.AccessToken** と、**PowerBIReportFor** のラムダ式を設定します。

```cshtml
@model ReportViewModel

...

<div class="side-body padding-top">
    @Html.PowerBIAccessToken(Model.AccessToken)
    @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
</div>
```

### <a name="controller"></a>コントローラー

**DashboardController.cs**:**アプリ トークン**を渡す PowerBIClient を作成します。 **資格情報**を取得するために、JSON Web トークン (JWT) が**署名キー**から生成されます。 **資格情報**は、**PowerBIClient** のインスタンスの作成に使用されます。 **PowerBIClient** のインスタンスを作成したら、GetReports() と GetReportsAsync() を呼び出すことができます。


CreatePowerBIClient()

```csharp
private IPowerBIClient CreatePowerBIClient()
{
    var credentials = new TokenCredentials(accessKey, "AppKey");
    var client = new PowerBIClient(credentials)
    {
        BaseUri = new Uri(apiUrl)
    };

    return client;
}
```

ActionResult Reports()

```csharp
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
```

タスク\<ActionResult > レポート (reportId の文字列)

```csharp
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
```

### <a name="integrate-a-report-into-your-app"></a>アプリへのレポートの統合

**レポート**を作成したら、**IFrame** を使用して Power BI **レポート**を埋め込みます。 **Microsoft Power BI ワークスペース コレクション** サンプルの powerbi.js のコード スニペットを次に示します。

```javascript
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<iframe style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

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

[Microsoft Power BI ワークスペース コレクションの一般的なシナリオ](scenarios.md)  
[Power BI ワークスペース コレクションでの認証と承認](app-token-flow.md)  
[レポートの埋め込み](embed-report.md)  
[データセットからの新しいレポートの作成](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

ご質問は、 [Power BI コミュニティ](https://community.powerbi.com/)で尋ねてみてください。
