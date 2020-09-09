---
title: suggester の作成
titleSuffix: Azure Cognitive Search
description: suggester を作成し、オートコンプリートまたは自動候補クエリの用語を呼び出す要求を作成して、Azure Cognitive Search における先行入力クエリのアクションを有効にします。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4889e73e851e285c84d5d4429298e9a7cdacc140
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89014389"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>クエリでオートコンプリートと候補の結果を有効にする suggester を作成する

Azure Cognitive Search では、[検索インデックス](search-what-is-an-index.md)に追加された **suggester** コンストラクトによって、"逐次検索" が有効になります。 suggester では、2 つのエクスペリエンスがサポートされています。部分的な入力を補完して用語クエリ全体を作成する "*オートコンプリート*" と、特定の一致へのクリック スルーを促す "*候補*" です。 オートコンプリートでは、クエリが生成されます。 候補では、一致するドキュメントが生成されます。

次のスクリーンショットは、[C# で初めてアプリを作成する](tutorial-csharp-type-ahead-and-suggestions.md)方法に関するページのものですが、その両方を示しています。 オートコンプリートを使用すると、可能性のある語句が予測され、"tw" に "in" が補完されます。 候補はミニ検索結果であり、ホテル名のようなフィールドには、インデックスから一致するホテルの検索ドキュメントが表示されます。 候補については、説明的な情報を提供する任意のフィールドを表示できます。

![オートコンプリートされたクエリと提案されたクエリの視覚的な比較](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "オートコンプリートされたクエリと提案されたクエリの視覚的な比較")

これらの機能は、個別に、または組み合わせて使用できます。 Azure Cognitive Search でこれらの動作を実装するために、インデックスとクエリのコンポーネントがあります。 

+ インデックスで、suggester をインデックスに追加します。 ポータル、[REST API](/rest/api/searchservice/create-index)、または [.NET SDK](/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet) を使用できます。 この記事の残りの部分では、suggester の作成に焦点を絞って説明します。

+ クエリ要求では、[後で示す API 一覧](#how-to-use-a-suggester)のいずれかを呼び出します。

文字列フィールドの場合、フィールドごとに逐次検索のサポートが有効になります。 スクリーンショットに示したものと同様のエクスペリエンスを求めているのであれば、同じ検索ソリューション内に両方の先行入力の動作を実装できます。 どちらの要求でも特定のインデックスの*documents* コレクションをターゲットとし、ユーザーが入力文字列を 3 文字以上指定した時点から応答が返されます。

## <a name="what-is-a-suggester"></a>suggester とは

suggester は、部分的なクエリで一致するプレフィックスを格納することにより、search-as-you-type (入力と並行して検索する) 動作をサポートする内部データ構造です。 トークン化された用語と同様に、プレフィックスは、suggester フィールド コレクションで指定された各フィールドに 1 つずつ、逆インデックスで格納されます。

## <a name="define-a-suggester"></a>suggester を定義する

suggester を作成するには、[インデックス スキーマ](/rest/api/searchservice/create-index)に suggester を追加し、[各プロパティを設定](#property-reference)します。 suggester の作成に最適なタイミングは、それを使用するフィールドを定義するときです。

+ 文字列フィールドのみを使用する

+ フィールドで既定の標準 Lucene アナライザー (`"analyzer": null`) または [言語アナライザー](index-add-language-analyzers.md) (たとえば、`"analyzer": "en.Microsoft"`) を使用する

### <a name="choose-fields"></a>フィールドの選択

suggester は、いくつかのプロパティを備えていますが、基本的には、search-as-you-type エクスペリエンスを有効にしている文字列フィールドのコレクションです。 インデックスごとに 1 つの suggester があるため、suggester リストには、候補とオートコンプリートの両方のコンテンツを提供するすべてのフィールドを含める必要があります。

追加のコンテンツには、より多くの用語が補完候補として含まれるため、オートコンプリートには、大きいフィールド プールの方がより有効です。

一方、候補では、フィールド選択が選択的である場合により良い結果が得られます。 候補は検索ドキュメントのプロキシであるため、単一の結果を最もよく表すフィールドが必要になることに注意してください。 複数の一致を区別する名前、タイトル、またはその他の固有のフィールドが最適です。 フィールドが反復される値で構成されている場合、候補は同じ結果で構成され、ユーザーはどれをクリックすればよいかがわかりません。

両方の逐次検索エクスペリエンスを満たすには、オートコンプリートに必要なすべてのフィールドを追加しますが、その後、 **$select**、 **$top**、 **$filter**、**searchFields** を使用して、候補の結果を制御します。

### <a name="choose-analyzers"></a>アナライザーの選択

アナライザーを選択すると、フィールドをトークン化する方法と、その後のプレフィックスを付ける方法が決まります。 たとえば、"context-sensitive" などのハイフンでつながれた文字列の場合、言語アナライザーを使用すると、"context"、"sensitive"、"context-sensitive" というトークンの組み合わせになります。 ここでは標準の Lucene アナライザーを使用したので、ハイフンでつながれた文字列は存在しません。 

アナライザーを評価する場合、用語がどのようにトークン化され、その後、どのようにプレフィックスが付けられるかを理解するために、[テキストの分析 API](/rest/api/searchservice/test-analyzer) を使用することをお勧めします。 インデックスを作成したら、文字列に対してさまざまなアナライザーを試して、トークン出力を確認することができます。

[カスタム アナライザー](index-add-custom-analyzers.md)または[定義済みアナライザー](index-add-custom-analyzers.md#predefined-analyzers-reference) (標準の Lucene を除く) を使用するフィールドは、不適切な結果を防ぐために明示的に許可されません。

> [!NOTE]
> アナライザーの制約を回避する必要がある場合 (たとえば、特定のクエリ シナリオのためにキーワードまたは ngram アナライザーが必要な場合)、同じコンテンツに対して 2 つの別個のフィールドを使用する必要があります。 これにより、一方のフィールドで suggester を指定し、他方をカスタム アナライザーの構成で設定することができます。

### <a name="when-to-create-a-suggester"></a>suggester を作成するタイミング

suggester の作成に最適なタイミングは、フィールド定義自体を作成するときです。

既存のフィールドを使用して suggester を作成しようとすると、API ではそれは許可されません。 プレフィックスは、インデックス作成の間に、2 つ以上の文字の組み合わせから成る部分的な用語が完全な用語と並行してトークン化されるときに生成されます。 既存のフィールドが既にトークン化されている場合、それらを suggester に追加するには、インデックスを再構築する必要があります。 詳細については、[Azure Cognitive Search インデックスを再構築する方法](search-howto-reindex.md)に関するページを参照してください。

## <a name="create-using-rest"></a>REST を使用して作成する

REST API では、[インデックス作成](/rest/api/searchservice/create-index)または[インデックス更新](/rest/api/searchservice/update-index)を使用して suggester を追加します。 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>.NET を使用して作成する

C# では、[Suggester オブジェクト](/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)を定義します。 `Suggesters` はコレクションですが、1 つの項目のみを取ることができます。 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>プロパティ リファレンス

|プロパティ      |説明      |
|--------------|-----------------|
|`name`        |suggester の名前。|
|`searchMode`  |候補語句の検索に使用する戦略。 現在サポートされているモードは `analyzingInfixMatching` のみです。これは、現在、用語の先頭でマッチングを実行します。|
|`sourceFields`|検索候補の内容のソースである 1 つまたは複数のフィールドのリスト。 フィールドは `Edm.String` 型および `Collection(Edm.String)` 型である必要があります。 フィールドにアナライザーが指定されている場合は、(カスタム アナライザーではなく) [こちらの一覧](/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet)の名前付きアナライザーである必要があります。<p/> ベスト プラクティスとしては、検索バーまたはドロップダウン リストのどちらの補完された文字列でも、想定される適切な応答に利用するフィールドのみを指定します。<p/>ホテル名には有効桁数があるため、適切な選択肢です。 説明やコメントなどの詳細なフィールドは、文字数が多くなりすぎます。 同様に、カテゴリやタグなどの反復的なフィールドでは、効果が低くなります。 例には、複数のフィールドを含めることができることを例示するために、ひとまず、"カテゴリ" を組み入れています。 |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>suggester を使用する

クエリには suggester が使用されています。 suggester を作成したら、search-as-you-type エクスペリエンス用の次の API のいずれかを呼び出します。

+ [検索候補 REST API](/rest/api/searchservice/suggestions) 
+ [オートコンプリート REST API](/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync メソッド](/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync メソッド](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

検索アプリケーションでは、クライアント コードで、[jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) などのライブラリを利用して、部分クエリを収集し、一致を提供する必要があります。 このタスクの詳細については、[クライアント コードへのオートコンプリートまたは候補の結果の追加](search-autocomplete-tutorial.md)に関するページを参照してください。

API の使用方法については、オートコンプリート REST API の次の呼び出しで説明します。 この例では、2 つのポイントがあります。 第一に、すべてのクエリと同様に、操作はインデックスの documents コレクションに対して行われ、クエリには **search** パラメーターが含まれます。この場合、部分クエリが提供されます。 第二に、**suggesterName** を要求に追加する必要があります。 インデックスで suggester が定義されていない場合、オートコンプリートまたは検索候補の呼び出しは失敗します。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>サンプル コード

+ [C# での最初のアプリの作成 (レッスン 3 - 逐次検索の追加)](tutorial-csharp-type-ahead-and-suggestions.md)サンプルでは、suggester の構築、提案されたクエリ、オートコンプリート、ファセット ナビゲーションが示されています。 このコード サンプルはサンドボックスの Azure Cognitive Search サービスで実行され、事前に読み込まれたホテルのインデックスが使用されるので、F5 キーを押すだけでアプリケーションを実行できます。 サブスクリプションやサインインは必要ありません。

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) は、C# と Java 両方のコードが含まれる古いサンプルです。 それでも、suggester の構築、提案されたクエリ、オートコンプリート、ファセット ナビゲーションが示されています。 このコード サンプルでは、ホストされた [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) サンプル データが使用されます。 

## <a name="next-steps"></a>次のステップ

要求の形式の詳細については、次の記事を参照することをお勧めします。

> [!div class="nextstepaction"]
> [クライアント コードにオートコンプリートと検索候補を追加する](search-autocomplete-tutorial.md)