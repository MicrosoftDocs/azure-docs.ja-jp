---
title: 検索ボックスに検索候補とオートコンプリートを追加する - Azure Search
description: suggester を作成し、検索ボックスに完成した用語や語句を入力する要求を作成することで、Azure Search における先行入力クエリのアクションを有効にします。
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183281"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Azure Search アプリケーションに提案やオートコンプリートを追加する

この記事では、[検索候補](https://docs.microsoft.com/rest/api/searchservice/suggestions)および[オートコンプリート](https://docs.microsoft.com/rest/api/searchservice/autocomplete)を使用して、search-as-you-type (入力と並行して検索) の動作をサポートするパワフルな検索ボックスを作成します。

+ *検索候補*は、入力時に生成される候補の結果です。各検索候補は、それまでにユーザーが入力した内容と一致するインデックスからの単一の結果です。 

+ *オートコンプリート*は、ユーザーが現在入力している単語または語句を "完成" させます。 結果が返される代わりに、クリエが完成されます。ユーザーがそのクエリを実行し、結果を返すことができます。 検索候補と同様に、クエリ内の完成された単語や語句はインデックスでの一致に基づいています。 インデックスでゼロの結果を返すクエリはこのサービスから提供されません。

**DotNetHowToAutocomplete** のサンプル コードをダウンロードして実行し、これらの機能を評価できます。 サンプル コードは、[NYCJobs デモ データ](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)を使用して設定される事前構築済みのインデックスをターゲットとします。 NYCJobs インデックスには、[Suggester の作成](index-add-suggesters.md)が含まれます。これは、検索候補またはオートコンプリートを使用する場合の要件です。 サンドボックス サービスでホストされる用意されたインデックスを使用するか、NYCJobs サンプル ソリューション内のデータ ローダーを使用して[独自のインデックスにデータを設定](#configure-app)できます。 

**DotNetHowToAutocomplete** のサンプルでは、C# および JavaScript の両方の言語バージョンで、検索候補とオートコンプリートの両方を実行します。 C# 開発者は、[Azure Search .NET SDK](https://aka.ms/search-sdk) を使用する ASP.NET MVC ベースのアプリケーションを実行できます。 オートコンプリートまたは候補のクエリ呼び出しを実行するためのロジックは、HomeController.cs ファイルにあります。 JavaScript 開発者の場合、同等のクエリ ロジックが IndexJavaScript.cshtml にあります。これには、[Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) への直接呼出しが含まれます。 

どちらの言語バージョンでも、フロントエンドのユーザー エクスペリエンスは、[jQuery UI](https://jqueryui.com/autocomplete/) および [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) ライブラリに基づいています。 これらのライブラリを使用して、検索候補とオートコンプリートの両方をサポートする検索ボックスを作成します。 検索ボックスで収集された入力は、検索候補およびオートコンプリートのアクション (HomeController.cs または IndexJavaScript.cshtml で定義されているアクションなど) とペアになっています。

この演習では、次のタスクについて説明します。

> [!div class="checklist"]
> * JavaScript で検索入力ボックスを実装し、検索候補の一致またはオートコンプリートの用語のための要求を発行します
> * C# では、検索候補とオートコンプリートのアクションを HomeController.cs に定義します
> * JavaScript では、REST API を直接呼び出して同じ機能を提供します

## <a name="prerequisites"></a>前提条件

このソリューションでは、用意された NYCJobs デモ インデックスをホストするライブ サンドボックス サービス使用するため、この演習では Azure Search サービスはオプションです。 独自の検索サービスでこの例を実行する場合は、[NYC ジョブ インデックスを構成する](#configure-app)手順を参照してください。

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) (任意のエディション)。 サンプル コードと手順については、無料の Community エディションでテストされています。

* [DotNetHowToAutoComplete サンプル](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)をダウンロードします。

このサンプルは、検索候補、オートコンプリート、ファセット ナビゲーション、およびクライアント側のキャッシュに対応する包括的なものです。 readme とコメントで、サンプルによって提供される機能の詳細な説明を確認してください。

## <a name="run-the-sample"></a>サンプルを実行する

1. Visual Studio で **AutocompleteTutorial.sln** を開きます。 ソリューションには、NYC ジョブのデモ インデックスへ接続できる ASP.NET MVC プロジェクトが含まれます。

2. F5 キーを押してプロジェクトを実行し、任意のブラウザーにページを読み込みます。

上部に、C# または JavaScript を選択するためのオプションが表示されます。 C# オプションは、ブラウザーから HomeController を呼び出し、Azure Search .Net SDK を使用して結果を取得します。 

JavaScript オプションは、ブラウザーから直接 Azure Search REST API を呼び出します。 このオプションでは、コントローラーがフローから除外されるため、通常はパフォーマンスが大幅に向上します。 自分のニーズと言語設定に合ったオプションを選択できます。 オートコンプリートの例を示すさまざまなページがあり、それぞれにガイダンスが示されています。 それぞれの例に推奨するサンプル テキストがあり、そのテキストを使って試してみることができます。  

検索ボックスに数文字を入力して、何が起こるかを観察してください。

## <a name="search-box"></a>検索ボックス

C# と JavaScript の両方のバージョンで、検索ボックスの実装はまったく同じです。 

\Views\Home フォルダーの下の **Index.cshtml** ファイルを開き、コードを見ます。

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

この例は、スタイリング用のクラス、JavaScript によって参照される ID、およびプレースホルダー テキストがあるシンプルな入力テキスト ボックスです。  魔法は埋め込み JavaScript にあります。

C# 言語のサンプルでは、Index.cshtml 内で JavaScript を使用して、[jQuery UI Autocomplete ライブラリ](https://jqueryui.com/autocomplete/)を利用します。 このライブラリでは、MVC コントローラーに対する非同期呼び出しを実行して検索候補を取得することで、検索ボックスにオートコンプリートの機能が追加されます。 JavaScript 言語バージョンは、IndexJavaScript.cshtml にあります。 これには、検索バー用の次のスクリプトに加えて、Azure Search に対する REST API 呼び出しが含まれます。

最初の例の JavaScript コードを見てみましょう。このコードでは、jQuery UI Autocomplete 関数を呼び出し、検索候補の要求を渡します。

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

上記のコードは、ページの読み込み時にブラウザーで実行され、"example1a" 入力ボックスの jQuery UI オートコンプリートを構成します。  `minLength: 3` は、検索ボックスに少なくとも 3 文字が入力されたときのみ、検索候補を表示することを保証します。  重要なのは、source 値です。

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

上記の行は、検索ボックスの下に表示する項目の一覧を取得する場所を jQuery UI Autocomplete 関数に指示します。 このプロジェクトは、MVC プロジェクトであるため、クエリ候補を返すためのロジックを含む HomeController.cs 内の Suggest 関数を呼び出します (Suggest については次のセクションで説明します)。 また、この関数は、強調表示、あいまい一致、および用語を制御するためのいくつかのパラメーターを渡します。 オートコンプリート JavaScript API では、term パラメーターが追加されます。

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>あいまい一致をサポートするようにサンプルを拡張する

あいまい検索では、ユーザーが検索ボックスに単語を間違って入力した場合でも、類似する一致に基づいて結果を取得できます。 必須ではありませんが、この検索では、先行入力操作の信頼性が大幅に改善されます。 あいまい一致を実行できるように source 行を変更しましょう。

変更元の行は次のとおりです。

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

これを、次のように変更します。

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

F5 キーを押してアプリケーションを起動します。

"execative" と入力してみます。入力した文字と完全に一致していないにもかかわらず、"executive" という結果が返ってくることに注目してください。

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>Azure Search オートコンプリートによってサポートされる jQuery オートコンプリート

ここまでは、検索候補の中核として、検索 UX コードを使用しました。 次のコード ブロックでは、UI Autocomplete 関数 (index.cshtml の行 91) を示し、Azure Search オートコンプリートの要求を渡します。

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C# の例

Web ページの JavaScript コードの見直しが終わったので、次は、Azure Search .NET SDK を使用して検索候補の一致を実際に取得するサーバー側の C# コントローラー コードを見ていきましょう。

Controllers ディレクトリ内の **HomeController.cs** ファイルを開きます。 

最初に気付くのは、クラスの上部にある `InitSearch` と呼ばれるメソッドです。 このメソッドでは、Azure Search サービスに対する認証済み HTTP インデックス クライアントを作成します。 詳細については、「[.NET アプリケーションから Azure Search を使用する方法](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)」をご覧ください。

行 41 の Suggest 関数に注目してください。 これは、[DocumentsOperationsExtensions.Suggest メソッド](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)に基づいています。

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Suggest 関数は、ヒットの強調表示が返されるかどうか、および入力された検索用語に加えてあいまい一致も使用するかどうかを決定する 2 つのパラメーターを使用します。 このメソッドは、検索候補 API に渡される [SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet) オブジェクトを作成します。 結果は、クライアントに表示できるように JSON に変換されます。

行 69 の Autocomplete 関数に注目してください。 これは、[DocumentsOperationsExtensions.Autocomplete メソッド](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)に基づいています。

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Autocomplete 関数は、検索用語の入力を取得します。 このメソッドは、[AutoCompleteParameters オブジェクト](https://docs.microsoft.com/rest/api/searchservice/autocomplete)を作成します。 結果は、クライアントに表示できるように JSON に変換されます。

(省略可能) Suggest 関数の始めにブレークポイントを追加し、コードをステップ実行します。 SDK によって返される応答と、メソッドから返される結果に変換される様子に注目します。

ページ上のその他の例でも、同じパターンに従って、ヒットの強調表示と、オートコンプリートの結果のクライアント側のキャッシュをサポートするファセットが追加されています。 これらを見直してそのしくみを理解し、検索エクスペリエンスでどのように活用するかを判断してください。

## <a name="javascript-example"></a>JavaScript の例

オートコンプリートと検索候補の JavaScript 実装により REST API が呼び出されます。その際、ソースとして URI が使用され、インデックスと操作が指定されます。 

JavaScript 実装を確認するには、**IndexJavaScript.cshtml** を開きます。 検索ボックスに jQuery UI Autocomplete 関数も使用され、検索用語の入力を収集し、検索候補の一致または完成された用語を取得するために Azure Search に非同期呼び出しを実行します。 

最初の例の JavaScript コードを見てみましょう。

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

この例を上記の home コント ローラーを呼び出す例と比較した場合、いくつかの類似点に気付くでしょう。  `minLength` と `position` のオートコンプリート構成は、完全に同じです。 

大きな違いは source にあります。 home コントローラー内の Suggest メソッドを呼び出す代わりに、REST 要求が JavaScript 関数内に作成され、Ajax を使用して実行されます。 応答は "success" で処理され、source として使用します。

REST 呼び出しでは、実行中の API 呼び出しが[オートコンプリート](https://docs.microsoft.com/rest/api/searchservice/autocomplete)または[検索候補](https://docs.microsoft.com/rest/api/searchservice/suggestions)のどちらであるかを指定するために URI を使用します。 次の URI はそれぞれ行 9 と 10 にあります。

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

行 148 には、`autocompleteUri` を呼び出すスクリプトがあります。 `suggestUri` に対する最初の呼び出しは、行 39 にあります。

> [!Note]
> JavaScript 内のサービスに対する REST 呼び出しの実行は、REST API の便利なデモとしてここに提供されていますが、ベストプラクティスまたは推奨事項と見なすべきものではありません。 スクリプトに API キーやエンドポイントを含めると、スクリプトからこれらの値を読み取ることができる人物がサービスに対してサービス拒否攻撃を実行する機会を与えてしまいます。 学習目的での JavaScript の使用は安全ですが、無料サービスにホストされているインデックスでは、運用環境のコードでのインデックス作成およびクエリ操作に Java または C# を使用することをお勧めします。 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>お使いのサービスで実行されるよう NYCJobs を構成する

これまでは、ホストされている NYCJobs デモ インデックスを使用してきました。 インデックスを含むコード全体を完全に使用する場合は、次の手順に従って独自の検索サービスにインデックスを作成して読み込みます。

1. [Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 この例では、無料のサービスを使用できます。 

   > [!Note]
   > 無料の Azure Search サービスを使用している場合、インデックスは 3 つに制限されています。 NYCJobs データ ローダーは、2 つのインデックスを作成します。 ご利用のサービスに、新しいインデックスを受け入れる余地があることを確認してください。

1. [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) サンプル コードをダウンロードします。

1. NYCJobs サンプル コードの DataLoader フォルダー内の **DataLoader.sln** を Visual Studio で開きます。

1. Azure Search サービスの接続情報を追加します。 DataLoader プロジェクト内の App.config を開き、TargetSearchServiceName と TargetSearchServiceApiKey appSettings を、利用している Azure Search サービスと Azure Search Service API キーを反映するように更新します。 この情報は、Azure portal で確認できます。

1. F5 キーを押してアプリケーションを起動し、2 つのインデックスを作成して NYCJob サンプル データをインポートします。

1. **AutocompleteTutorial.sln** を開き、 **AutocompleteTutorial** プロジェクト内の Web.config を編集します。 SearchServiceName と SearchServiceApiKey の値を、お使いの検索サービスで有効な値に変更します。

1. F5 キーを押してアプリケーションを実行します。 サンプル Web アプリが既定のブラウザーで開きます。 インデックスとデータがお使いのサービスでホストされているだけで、操作方法はサンドボックス バージョンと同じです。

## <a name="next-steps"></a>次の手順

この例では、オートコンプリートと検索候補をサポートする検索ボックスを作成するための基本的な手順について説明しています。 ASP.NET MVC アプリケーションをビルドし、Azure Search .NET SDK または REST API を使用して検索候補を取得する方法を確認しました。

次の手順では、検索候補とオートコンプリートを検索エクスペリエンスに統合します。 次のリファレンス記事が役立ちます。

> [!div class="nextstepaction"]
> [オートコンプリート REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [検索候補 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [インデックス作成 REST API のファセット インデックス属性](https://docs.microsoft.com/rest/api/searchservice/create-index)

