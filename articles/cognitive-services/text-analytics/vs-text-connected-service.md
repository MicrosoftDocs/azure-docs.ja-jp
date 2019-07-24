---
title: チュートリアル:Visual Studio の接続済みサービスを使用して Text Analytics Service に接続する
titleSuffix: Azure Cognitive Services
description: ASP.NET Core Web アプリケーション から Text Analytics に接続する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 4e1c03085d6b1d0099ac66dd3d1dadd981a561aa
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004246"
---
# <a name="tutorial-connect-to-the-text-analytics-service-with-connected-services-in-visual-studio"></a>チュートリアル:Visual Studio の接続済みサービスを使用して Text Analytics Service に接続する

Text Analytics Service を使用することで、豊富な情報を抽出して視覚データを分類および処理したり、機械による画像のモデレートを実施してサービスのキュレーションを支援したりできます。

この記事と関連記事では、Text Analytics Service に Visual Studio 接続済みサービス機能を使用する方法について詳しく説明します。 この機能は、Cognitive Services 拡張機能がインストールされた Visual Studio 2017 15.7 またはそれ以降の両方で使用できます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 このサブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- Visual Studio 2017 バージョン 15.7 (Web 開発ワークロードもインストールされます)。 [こちら](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)でダウンロードできます。

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Text Analytics Service のサポートをプロジェクトに追加する

1. TextAnalyticsDemo という名前の新しい ASP.NET Core Web プロジェクトを作成します。 Web アプリケーション (モデル ビュー コントローラー) プロジェクト テンプレートを、すべて既定の設定で使用します。 コードをプロジェクトにコピーしたときに名前空間が一致するよう、プロジェクトの名前を MyWebApplication にすることが重要です。  この記事の例では MVC を使用しますが、Text Analytics 接続済みサービスは任意の ASP.NET プロジェクト タイプと共に使用できます。

1. **ソリューション エクスプローラー**で、**[接続済みサービス]** 項目をダブルクリックします。
   [接続済みサービス] ページが開いて、プロジェクトに追加できるサービスが表示されます。

   ![ソリューション エクスプローラーでの接続済みサービスのスクリーンショット](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. 利用可能なサービスのメニューで、**[Evaluate Sentiment with Text Analytics]\(Text Analytics でセンチメントを評価する\)** を選択します。

   ![[接続済みサービス] 画面のスクリーンショット](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Visual Studio にサインインしたときに、アカウントに関連付けられている Azure サブスクリプションがある場合は、サブスクリプションのドロップダウン リストがページに表示されます。

   ![Text Analytics 接続済みサービス画面のスクリーンショット](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. 使用するサブスクリプションを選択し、Text Analytics Service の名前を選択するか、または **[編集]** リンクを選択して自動生成の名前を修正し、リソース グループ、および価格レベルを選択します。

   ![リソース グループと価格レベルのフィールドのスクリーンショット](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   価格レベルの詳細については、リンク先を参照してください。

1. **[追加]** を選択して、接続済みサービスのサポートを追加します。
   Text Analytics Service への接続をサポートするために、Visual Studio はプロジェクトを修正し、NuGet パッケージ、構成ファイルのエントリ、およびその他の変更を追加します。 **出力ウィンドウ**には、プロジェクトに対して実行されている処理のログが表示されます。 次のように表示されます。

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.1'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Text Analytics Service を使用して、テキスト サンプルの言語を検出します。

1. Startup.cs で次の using ステートメントを追加します。
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. 構成フィールドを追加し、Startup クラスの構成フィールドを初期化するコンストラクターを追加して、プログラムで構成を有効にします。

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. DemoTextAnalyzeController という名前の Controllers フォルダーにクラス ファイルを追加し、その内容を次のコードに置き換えます。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    コードに含まれている GetTextAnalyzeClient は、Text Analytics API の呼び出しに使用できるクライアント オブジェクトと、指定されたテキストに対して DetectLanguage を呼び出す要求ハンドラーを取得します。

1. 上記のコードで使用される MyHandler ヘルパー クラスを追加します。

    ```csharp
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

1. Models フォルダーに、モデルのクラスを追加します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. 分析されたテキスト、決定された言語、および、分析での信頼レベルを表すスコアを表示するためのビューを追加します。 これを行うには、**Views** フォルダーを右クリックし、**[追加]**、**[ビュー]** の順に選択します。 表示されたダイアログ ボックスで、名前 _TextAnalyzeResult_ を指定し、既定の設定は変更せず、_TextAnalyzeResult.cshtml_ という名前の新しいファイルを **Views** フォルダーに作成し、次の内容をそのファイルにコピーします。
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. 例をビルドしてローカルで実行します。 いくつかテキストを入力して、Text Analytics がどの言語を検出するか確認します。
   
## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、リソース グループを削除します。 これにより、Cognitive Service と関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このチュートリアルで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

[Text Analytics Service のドキュメント](index.yml)を読み、Text Analytics Service について理解を深めます。
