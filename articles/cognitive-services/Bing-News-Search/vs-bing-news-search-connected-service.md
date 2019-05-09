---
title: Visual Studio と C# の接続済みサービスを使用して Bing News Search API に接続する
titleSuffix: Azure Cognitive Services
description: ASP.NET Core Web アプリケーションから Bing News Search に接続します。
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 2925ca5a303876a68b6d605c7312d43af102b6e0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58088470"
---
# <a name="tutorial-connect-to-bing-news-search-api-with-connected-services-in-visual-studio-and-c"></a>チュートリアル:Visual Studio と C# の接続済みサービスを使用して Bing News Search API に接続する

Bing News Search を使用すると、Web にスコープした広告のない検索エンジンのパワーを、アプリおよびサービスで利用できるようになります。 Bing News Search は Cognitive Services で利用できる検索サービスの 1 つです。

この記事では、Visual Studio の接続サービス機能を Bing News Search のために使用する方法について詳しく説明します。 この機能は、Cognitive Services 拡張機能がインストールされた Visual Studio 2017 15.7 以降で使用できます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 このサブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- Visual Studio 2017 バージョン 15.7 (Web 開発ワークロードもインストールされます)。 [こちら](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)でダウンロードできます。

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Bing News Search API のサポートをプロジェクトに追加する

1. MyWebApplication という名前の、新しい ASP.NET Core Web アプリケーションを作成します。 **Web アプリケーション (モデル ビュー コントローラー)** プロジェクト テンプレートを、すべて既定の設定で使用します。 コードをプロジェクトにコピーしたときに名前空間が一致するよう、プロジェクトの名前を MyWebApplication にすることが重要です。 

1. **ソリューション エクスプローラー**で、**[追加]** > **[接続済みサービス]** を選択します。
   [接続済みサービス] ページが開いて、プロジェクトに追加できるサービスが表示されます。

   ![接続済みサービスのメニュー項目を追加するスクリーンショット](../media/vs-common/Connected-Service-Menu.PNG)

1. 利用可能なサービスのメニューで、**[Bring Intelligent Search To Your Apps]\(アプリでインテリジェント検索を可能にする\)** を選択します。

   ![接続済みサービスの一覧のスクリーンショット](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Visual Studio にサインインしたときに、アカウントに関連付けられている Azure サブスクリプションがある場合は、サブスクリプションのドロップダウン リストがページに表示されます。 使用するサブスクリプションを選択し、Bing News Search API の名前を選択します。 **[編集]** を選択して、自動的に生成された名前を変更することもできます。

   ![サブスクリプションと名前のフィールドのスクリーンショット](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. リソース グループと価格レベルを選択します。

   ![リソース グループと価格レベルのフィールドのスクリーンショット](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   価格レベルの詳細が必要な場合、**[価格のレビュー]** を選択します。

1. **[追加]** を選択して、接続済みサービスのサポートを追加します。
   Bing News Search API への接続をサポートするために、Visual Studio はプロジェクトを修正し、NuGet パッケージ、構成ファイルのエントリ、およびその他の変更を追加します。 出力には、プロジェクトに対して実行されている処理のログが表示されます。 次のように表示されます。

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   この時点で、appsettings.json ファイルには次の新しい設定が含まれています。

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Bing News Search API を使用して Web ページに検索機能を追加する

Bing News Search API のサポートをプロジェクトに追加したので、ここでは、API を使用してインテリジェント検索を Web ページに追加する方法を示します。

1. *Startup.cs* 内の `ConfigureServices` メソッドで、`IServiceCollection.AddSingleton` の呼び出しを追加します。 これにより、キー設定を含む構成オブジェクトがプロジェクト内のコードから使用できるようになります。
 
   ```csharp
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddMvc();
           services.AddSingleton<IConfiguration>(Configuration);
       }
   ```


1. **Models** フォルダーの下に、*BingNewsModel.cs* という名前の新しいクラス ファイルを追加します。 プロジェクトに別の名前を付けた場合、MyWebApplication の代わりに独自のプロジェクトの名前空間を使用します。 内容を次のコードに置き換えます。
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   このモデルは、Bing News Search サービスの呼び出しの結果を格納するために使用されます。
 
1. **Controllers** フォルダー内で、*IntelligentSearchController.cs* という名前の新しいクラス ファイルを追加します。 内容を次のコードに置き換えます。

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   このコードでは、キーを含む構成オブジェクトをコンストラクターが設定します。 `Search` ルートのメソッドは、単なる `BingSearchResult` 関数へのリダイレクトです。 これは `GetNewsSearchClient` メソッドを呼び出して `NewsSearchAPI` クライアント オブジェクトを取得します。  `NewsSearchAPI` クライアント オブジェクトに含まれる `SearchAsync` メソッドは、サービスを実際に呼び出して、さきほど作成した `SearchResult` モデルに結果を返します。 

1. 上記のコードで参照されていたクラス `MyHandler` を追加します。 これは、検索サービスの非同期呼び出しをその基底クラス `DelegatingHandler` に委任します。

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. 検索を送信して結果を表示するためのサポートを追加するには、**Views** フォルダー内で、**IntelligentSearch** という名前の新しいフォルダーを作成します。 この新しいフォルダー内で、ビュー *BingSearchResult.cshtml* を追加します。 次のコードをコピーします。

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Web アプリケーションをローカルで起動し、さきほど作成したページの URL (/IntelligentSearch/BingSearchResult) を入力し、[Search] ボタンを使用して検索要求を送信します。

   ![Bing News Search 結果のスクリーンショット](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったリソース グループは削除できます。 これにより、Cognitive Service と関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 削除するリソース グループを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください]** ボックスにリソース グループの名前を入力して **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

Bing News Search API の詳細については、「[Bing News Search とは](index.yml)」を参照してください。
