---
title: 先行入力クエリをインデックスに追加する - Azure Search
description: suggester を作成し、オートコンプリートまたは自動候補クエリの用語を呼び出す要求を作成して、Azure Search における先行入力クエリのアクションを有効にします。
ms.date: 09/30/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: d3f934bea5df821e51a4747170af4f7efd1eaacc
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828291"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Azure Search で先行入力のために suggester をインデックスに追加する

Azure Search の "入力と平行して検索を行う" 機能または先行入力機能は、[検索インデックス](search-what-is-an-index.md)に追加する **suggester** コンストラクトに基づいて行われます。 それは、先行入力を有効にする 1 つ以上のフィールドのリストです。

suggester では、2 種類の先行入力がサポートされています。"*オートコンプリート*" では入力した語句が補完され、"*検索候補*" では一致するドキュメントの短いリストが返されます。  

[C# での最初のアプリの作成](tutorial-csharp-type-ahead-and-suggestions.md)サンプルの次のスクリーンショットでは、先行入力が示されています。 オートコンプリートでは、ユーザーが検索ボックスに入力する内容が予測されます。 実際の入力は "tw" であり、オートコンプリートによってそれに "in" が補完されて、予想される検索用語が "twin" として解決されます。 検索候補は、ドロップダウン リストに表示されます。 検索候補には、結果を最もよく表現しているドキュメントの任意の一部を表示させることができます。 この例では、検索候補はホテル名です。 

![オートコンプリートと検索候補クエリの表示の比較](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "オートコンプリートと検索候補クエリの表示の比較")

Azure Search でこれらの動作を実装するために、インデックスとクエリのコンポーネントがあります。 

+ インデックスで、suggester をインデックスに追加します。 ポータル、[REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)、または [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet) を使用できます。 この記事の残りの部分では、suggester の作成に焦点を絞って説明します。 

+ クエリ要求では、[後で示す API 一覧](#how-to-use-a-suggester)のいずれかを呼び出します。

入力との平行検索のサポートは、フィールド単位で有効化されます。 スクリーンショットに示したものと同様のエクスペリエンスを求めているのであれば、同じ検索ソリューション内に両方の先行入力の動作を実装できます。 どちらの要求でも特定のインデックスの*documents* コレクションをターゲットとし、ユーザーが入力文字列を 3 文字以上指定した時点から応答が返されます。

## <a name="create-a-suggester"></a>suggester の作成

suggester は、いくつかのプロパティを備えていますが、基本的には先行入力のエクスペリエンスを有効にしているフィールドのコレクションです。 たとえば、旅行アプリでは、目的地、都市、アトラクションなどで先行入力検索を有効にすることがあります。 その場合、3 つのフィールドすべてをフィールド コレクションに追加します。

suggester を作成するために、それを 1 つのインデックス スキーマに追加します。 1 つのインデックスには 1 つの suggester を含めることができます (具体的には、suggesters コレクション内の 1 つの suggester)。 

### <a name="when-to-create-a-suggester"></a>suggester を作成するタイミング

suggester の作成に最適なタイミングは、フィールド定義自体を作成するときです。

既存のフィールドを使用して suggester を作成しようとすると、API ではそれは許可されません。 先行入力テキストは、インデックス作成の間に、2 つ以上の文字の組み合わせから成る部分的な用語が完全な用語と並行してトークン化されるときに作成されます。 既存のフィールドが既にトークン化されている場合、それらを suggester に追加するには、インデックスを再構築する必要があります。 インデックスの再作成について詳しくは、「[Azure Search インデックスを再構築する方法](search-howto-reindex.md)」をご覧ください。

### <a name="create-using-the-rest-api"></a>REST API を使用して作成する

REST API では、[インデックス作成](https://docs.microsoft.com/rest/api/searchservice/create-index)または[インデックス更新](https://docs.microsoft.com/rest/api/searchservice/update-index)を使用して suggester を追加します。 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```


### <a name="create-using-the-net-sdk"></a>.NET SDK を使用して作成する

C# では、[Suggester オブジェクト](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)を定義します。 `Suggesters` はコレクションですが、1 つの項目のみを取ることができます。 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="property-reference"></a>プロパティ リファレンス

|プロパティ      |説明      |
|--------------|-----------------|
|`name`        |suggester の名前。|
|`searchMode`  |候補語句の検索に使用する戦略。 現在サポートされている唯一のモードは `analyzingInfixMatching` です。文の先頭または中間にあるフレーズの柔軟なマッチングを実行します。|
|`sourceFields`|検索候補の内容のソースである 1 つまたは複数のフィールドのリスト。 フィールドは `Edm.String` 型および `Collection(Edm.String)` 型である必要があります。 フィールドにアナライザーが指定されている場合は、(カスタム アナライザーではなく) [こちらの一覧](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet)の名前付きアナライザーである必要があります。<p/>ベスト プラクティスとしては、検索バーまたはドロップダウン リストのどちらの補完された文字列でも、想定される適切な応答に利用するフィールドのみを指定します。<p/>ホテル名には有効桁数があるため、適切な選択肢です。 説明やコメントなどの詳細なフィールドは、文字数が多くなりすぎます。 同様に、カテゴリやタグなどの反復的なフィールドでは、効果が低くなります。 例には、複数のフィールドを含めることができることを例示するために、ひとまず、"カテゴリ" を組み入れています。 |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>suggester でのソース フィールドに対するアナライザーの制限

Azure Search では、個別の用語でクエリを実行できるようにフィールドの内容が分析されます。 suggester では、完全な用語に加えて、インデックスを作成するためのプレフィックスが必要です。このため、ソース フィールドに対して追加の分析が必要です。 カスタム アナライザーの構成では、さまざまなトークナイザーとフィルターを組み合わせることができるため、多くの場合、提案で必要なプレフィックスを生成することができません。 このため、Azure Search では、カスタム アナライザーを持つフィールドが suggester に含まれないようにします。

> [!NOTE] 
>  上記の制限を回避する必要がある場合は、同じコンテンツに対して 2 つの異なるフィールドを使用します。 これにより、一方のフィールドで suggester を指定し、他方をカスタム アナライザーの構成で設定することができます。

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>クエリで suggester を使用する

suggester を作成した後、クエリのロジックで適切な API を呼び出して、機能を開始します。 

+ [検索候補 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [オートコンプリート REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync メソッド](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync メソッド](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

API の使用方法については、オートコンプリート REST API の次の呼び出しで説明します。 この例では、2 つのポイントがあります。 1 つ目として、すべてのクエリと同様に、操作はインデックスのドキュメント コレクションに対して行われます。 2 つ目として、クエリ パラメーターを追加することができます。 多くのクエリ パラメーターは両方の API に共通ですが、リストはそれぞれで異なります。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

インデックスで suggester が定義されていない場合、オートコンプリートまたは検索候補の呼び出しは失敗します。

## <a name="sample-code"></a>サンプル コード

+ [C# での最初のアプリの作成](tutorial-csharp-type-ahead-and-suggestions.md)サンプルでは、suggester の構築、提案されたクエリ、オートコンプリート、ファセット ナビゲーションが示されています。 このコード サンプルはサンドボックスの Azure Search サービスで実行され、事前に読み込まれたホテルのインデックスが使用されるので、F5 キーを押すだけでアプリケーションを実行できます。 サブスクリプションやサインインは必要ありません。

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) は、C# と Java 両方のコードが含まれる古いサンプルです。 それでも、suggester の構築、提案されたクエリ、オートコンプリート、ファセット ナビゲーションが示されています。 このコード サンプルでは、ホストされた [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) サンプル データが使用されます。 


## <a name="next-steps"></a>次の手順

次の例において、要求の作成方法を確認することをお勧めします。

> [!div class="nextstepaction"]
> [検索候補とオートコンプリートの例](search-autocomplete-tutorial.md) 
