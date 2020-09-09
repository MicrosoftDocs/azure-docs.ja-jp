---
title: 検索ボックスにオートコンプリートと検索候補を追加する
titleSuffix: Azure Cognitive Search
description: suggester を作成し、検索ボックスに完成した用語や語句をオートコンプリートする要求を作成することで、Azure Cognitive Search における入力と並行した検索のクエリのアクションを有効にします。 また、候補となる一致を返すこともできます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: c0031b09dbb3335113cb52c9b3ec5e4fd4fa2758
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011581"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>クライアント アプリにオートコンプリートと検索候補を追加する

入力と並行した検索は、ユーザーが開始したクエリの生産性を向上させるための一般的な手法です。 Azure Cognitive Search では、このエクスペリエンスが "*オートコプリート*" によってサポートされています。これにより、部分的な入力に基づいて用語または語句が補完されます (「micro」と入力すると "microsoft" が表示されます)。 もう 1 つの形式は、"*検索候補*" です。これは、一致するドキュメントの短い一覧です (詳細ページにリンクできるように、ブックのタイトルを ID と共に返します)。 オートコンプリートも検索候補も、インデックスの一致を前提としません。 このサービスは、結果が返されないクエリは提示しません。

これらのエクスペリエンスを Azure Cognitive Search に実装するには、次のものが必要です。

+ バック エンドでの *suggester*。
+ 要求で[オートコンプリート](/rest/api/searchservice/autocomplete)または[検索候補](/rest/api/searchservice/suggestions)の API を指定する "*クエリ*"。
+ クライアント アプリで入力と並行した検索操作を処理するための "*UI コントロール*"。 このために既存の JavaScript ライブラリを使用することをお勧めします。

Azure Cognitive Search では、オートコンプリートされたクエリと候補となる結果が、検索インデックスと、suggester に登録した選択フィールドから取得されます。 suggester はインデックスの一部であり、クエリを補完するか、結果を提示するか、その両方を行うコンテンツを提供するフィールドを指定します。 インデックスが作成されて読み込まれると、suggester のデータ構造が内部で作成され、部分的なクエリに対する照合に使用されるプレフィックスが格納されます。 検索候補については、一意であるか、少なくとも反復しない適切なフィールドを選択することが、このエクスペリエンスにとって重要です。 詳細については、[suggester の作成](index-add-suggesters.md)に関するページを参照してください。

この記事の残りの部分では、クエリとクライアント コードに注目します。 重要なポイントを示すために、JavaScript と C# を使用します。 REST API の例は、各操作を簡潔に表現するために使用されています。 エンドツーエンドのコード サンプルへのリンクについては、「[次のステップ](#next-steps)」を参照してください。

## <a name="set-up-a-request"></a>要求の設定

要求の要素には、入力と並行した検索の API の 1 つ、部分クエリ、suggester が含まれます。 次のスクリプトでは、例としてオートコンプリート REST API を使用して、要求のコンポーネントを示します。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**suggesterName** には、用語または検索候補の補完に使用される、suggester に対応したフィールドが示されます。 特に検索候補の場合、フィールド リストは、一致する結果の中で明確な選択肢を提供するもので構成される必要があります。 コンピューター ゲームを販売するサイトの場合、このフィールドはゲーム タイトルになる場合があります。

**search** パラメーターでは、部分クエリを指定します。ここでは、文字が jQuery Autocomplete コントロールを介してクエリ要求に送られます。 上の例の "minecraf" は、コントロールによって渡された可能性のある内容を静的に表したものです。

この API で、部分クエリに長さの最小要件が適用されることはありません。そのため、1 文字だけにすることもできます。 ただし、jQuery Autocomplete では最小長が指定されます。 少なくとも 2 文字または 3 文字が一般的です。

一致するのは、入力文字列に含まれる用語の先頭です。 "the quick brown fox" の場合、オートコンプリートでも検索候補でも、"the"、"quick"、"brown"、または "fox" という部分的なバージョンで一致となりますが、"rown" または "ox" のような部分挿入辞では一致となりません。 さらに、一致ごとにダウンストリーム拡張のスコープが設定されます。 部分クエリ "quick br" は "quick brown" または "quick bread" で一致となりますが、"brown" または "bread" 自体は、"quick" が前に付かない限り一致となりません。

### <a name="apis-for-search-as-you-type"></a>入力と並行した検索の API

REST および .NET SDK のリファレンス ページについては、以下のリンク先を参照してください。

+ [検索候補 REST API](/rest/api/searchservice/suggestions) 
+ [オートコンプリート REST API](/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync メソッド](/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync メソッド](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>応答の構造化

オートコンプリートと検索候補の応答は、次のように、パターンに対して想定されるものです。[オートコンプリート](/rest/api/searchservice/autocomplete#response)の場合は用語のリストが返され、[検索候補](/rest/api/searchservice/suggestions#response)の場合は用語とドキュメント ID が返されるため、ドキュメントを取得できます (特定のドキュメントの詳細ページを取得するには、[Lookup Document](/rest/api/searchservice/lookup-document) API を使用します)。

応答は、要求のパラメーターによって形成されます。 オートコンプリートの場合は、[**autocompleteMode**](/rest/api/searchservice/autocomplete#autocomplete-modes) を設定して、テキスト補完が 1 つの用語で行われるか 2 つの用語で行われるかを指定します。 検索候補の場合は、選択したフィールドによって応答の内容が決まります。

検索候補については、応答をさらに絞り込んで、重複や、関連性がないように見える結果を回避する必要があります。 結果を制御するには、要求により多くのパラメーターを含めます。 次のパラメーターは、オートコンプリートと検索候補の両方に適用されますが、特に suggester に複数のフィールドが含まれている場合には、検索候補での必要性が高くなる可能性があります。

| パラメーター | 使用法 |
|-----------|-------|
| **$select** | suggester に複数の **sourceFields** がある場合は、 **$select** を使用して、値を提供するフィールドを選択します (`$select=GameTitle`)。 |
| **searchFields** | クエリを特定のフィールドに制限します。 |
| **$filter** | 結果セットに一致条件を適用します (`$filter=Category eq 'ActionAdventure'`)。 |
| **$top** | 結果を特定の数に制限します (`$top=5`)。|

## <a name="add-user-interaction-code"></a>ユーザー操作コードの追加

クエリ用語を自動入力するか、一致するリンクの一覧をドロップダウンするには、ユーザー操作コード (通常は JavaScript) が必要です。これにより、Azure Search のコグニティブ インデックスに対するオートコンプリートや検索候補クエリなど、外部ソースからの要求を使用できます。

このコードをネイティブに記述することもできますが、既存の JavaScript ライブラリの関数を使用する方がはるかに簡単です。 この記事では、検索候補用とオートコンプリート用の 2 つについて説明します。 

+ [Autocomplete ウィジェット (jQuery UI)](https://jqueryui.com/autocomplete/) は、検索候補の例で使用されます。 検索ボックスを作成した後、Autocomplete ウィジェットを使用する JavaScript 関数内でそれを参照することができます。 このウィジェットのプロパティでは、ソース (オートコンプリートまたは検索候補の関数)、操作が行われるまでの最小入力文字数、配置を設定します。

+ [XDSoft Autocomplete プラグイン](https://xdsoft.net/jqplugins/autocomplete/)は、オートコンプリートの例で使用されます。

これらのライブラリを使用して、検索候補とオートコンプリートの両方をサポートする検索ボックスを作成します。 検索ボックスで収集された入力は、検索候補およびオートコンプリートのアクションとペアになっています。

## <a name="suggestions"></a>検索候補

このセクションでは、検索ボックスの定義から始めて、候補となる結果の実装について説明します。 また、この記事で参照されている 1 つ目の JavaScript オートコンプリート ライブラリを呼び出す方法とスクリプトについても紹介します。

### <a name="create-a-search-box"></a>検索ボックスの作成

[jQuery UI Autocomplete ライブラリ](https://jqueryui.com/autocomplete/)と C# の MVC プロジェクトを想定した場合、**Index.cshtml** ファイルで JavaScript を使用して、検索ボックスを定義できます。 このライブラリでは、MVC コントローラーに対する非同期呼び出しを実行して検索候補を取得することで、入力と並行した検索操作が検索ボックスに追加されます。

\Views\Home フォルダーにある **Index.cshtml** では、検索ボックスを作成する行は次のようになる場合があります。

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

この例は、スタイリング用のクラス、JavaScript によって参照される ID、およびプレースホルダー テキストがあるシンプルな入力テキスト ボックスです。  

同じファイル内で、この検索ボックスを参照する JavaScript を埋め込みます。 次の関数は、検索候補 API を呼び出します。この API は、部分的な用語入力に基づいて一致する候補ドキュメントを要求します。

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

`source` では、検索ボックスの下に表示する項目の一覧を取得する場所を jQuery UI Autocomplete 関数に指示します。 このプロジェクトは、MVC プロジェクトであるため、クエリ候補を返すためのロジックを含む **HomeController.cs** 内で **Suggest** 関数を呼び出します。 また、この関数は、強調表示、あいまい一致、および用語を制御するためのいくつかのパラメーターを渡します。 オートコンプリート JavaScript API では、term パラメーターが追加されます。

`minLength: 3` により、検索ボックスに 3 文字以上が入力されている場合のみ、検索候補が表示されます。

### <a name="enable-fuzzy-matching"></a>あいまい一致の有効化

あいまい検索では、ユーザーが検索ボックスに単語を間違って入力した場合でも、類似する一致に基づいて結果を取得できます。 編集距離は 1 です。これは、ユーザー入力と一致との間に最大 1 文字の不一致がある可能性があることを意味します。 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>強調表示の有効化

強調表示では、結果内で入力に対応する文字にフォント スタイルが適用されます。 たとえば、部分入力が "micro" の場合、その結果が **micro**soft、**micro**scope などと表示されます。 強調表示は、HighlightPreTag パラメーターと HighlightPostTag パラメーターに基づき、Suggestion 関数でインラインで定義されています。

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Suggest 関数

C# と MVC アプリケーションを使用している場合は、Controllers ディレクトリにある **HomeController.cs** ファイルで、候補の結果用のクラスを作成できます。 .NET の場合、Suggest 関数は [DocumentsOperationsExtensions.Suggest メソッド](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)に基づいています。

`InitSearch` メソッドにより、Azure Cognitive Search サービスに対して認証済みの HTTP インデックス クライアントが作成されます。 .NET SDK の詳細については、「[.NET アプリケーションから Azure Cognitive Search を使用する方法](./search-howto-dotnet-sdk.md)」を参照してください。

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
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

Suggest 関数は、ヒットの強調表示が返されるかどうか、および入力された検索用語に加えてあいまい一致も使用するかどうかを決定する 2 つのパラメーターを使用します。 このメソッドは、検索候補 API に渡される [SuggestParameters](/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet) オブジェクトを作成します。 結果は、クライアントに表示できるように JSON に変換されます。

## <a name="autocomplete"></a>オートコンプリート

ここまでは、検索候補の中核として、検索 UX コードを使用してきました。 次のコード ブロックではオートコンプリートを示しており、XDSoft jQuery UI Autocomplete 関数を使用し、Azure Cognitive Search のオートコンプリートの要求を渡します。 検索候補と同様に、C# アプリケーションでも、ユーザー操作をサポートするコードは **index.cshtml** にあります。

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
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
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Autocomplete 関数

オートコンプリートは、[DocumentsOperationsExtensions.Autocomplete メソッド](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)に基づいています。 検索候補と同様に、このコード ブロックは **HomeController.cs** ファイル内にあります。

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

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Autocomplete 関数は、検索用語の入力を取得します。 このメソッドは、[AutoCompleteParameters オブジェクト](/rest/api/searchservice/autocomplete)を作成します。 結果は、クライアントに表示できるように JSON に変換されます。

## <a name="next-steps"></a>次のステップ

エンドツーエンドの手順や、入力と並行した検索の 2 つのエクスペリエンスを示すコードについては、次のリンクを参照してください。 どちらのコード例にも、検索候補とオートコンプリートのハイブリッド実装が含まれています。

+ [チュートリアル:C# での最初のアプリの作成 (レッスン 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C# コード サンプル: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# および JavaScript と REST のサイド バイ サイド コード サンプル](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)