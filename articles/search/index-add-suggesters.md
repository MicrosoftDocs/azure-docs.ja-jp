---
title: 先行入力クエリをインデックスに追加する - Azure Search
description: suggester を作成し、オートコンプリートまたは自動候補クエリの用語を呼び出す要求を作成して、Azure Search における先行入力クエリのアクションを有効にします。
ms.date: 03/22/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: a8bc86c2d3511fa04e595b8b2988d9a98bf084b2
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650463"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Azure Search で先行入力のために suggester をインデックスに追加する

**suggester** は、"入力と平行して検索を行う" エクスペリエンスをサポートする、[Azure Search インデックス](search-what-is-an-index.md)内のコンストラクトです。 先行入力クエリによる入力を有効にするフィールドの一覧を含みます。 1 つのインデックス内では、同一の suggester によって、次の 2 つのいずれかまたは両方の先行入力バリアントがサポートされます。*autocomplete* では、入力中の用語や語句を補完し、*suggestions* では結果の簡単なリストを提示します。 

次のスクリーンショットは、両方の先行入力機能を示しています。 この Xbox の検索ページでは、オートコンプリートの項目から、そのクエリに対する新しい検索結果ページへ遷移できます。ここでの検索候補は、該当する特定のゲームのページへ遷移する実際の結果になっています。 検索バー内の 1 つの項目に対するオートコンプリートを限定したり、ここに示したようなリストを提示したりすることが可能です。 検索候補には、結果を最もよく表現しているドキュメントの任意の一部を表示させることができます。

![オートコンプリートと検索候補クエリの表示の比較](./media/index-add-suggesters/visual-comparison-suggest-complete.png "オートコンプリートと検索候補クエリの表示の比較")

Azure Search でこれらの動作を実装するために、インデックスとクエリのコンポーネントがあります。 

+ インデックス コンポーネントは suggester です。 ポータル、REST API、または .NET SDK を使用して suggester を作成できます。 

+ クエリ コンポーネントは、クエリ要求上で指定されるアクション (検索候補またはオートコンプリートのどちらかのアクション) です。 

> [!Important]
> オートコンプリートは現在プレビュー段階にあり、プレビューの REST APIs および .NET SDK では利用可能です。 運用環境のアプリケーションは、対象としていません。 

入力との平行検索のサポートは、フィールド単位で有効化されます。 スクリーンショットに示したものと同様のエクスペリエンスを求めているのであれば、同じ検索ソリューション内に両方の先行入力の動作を実装できます。 どちらの要求でも特定のインデックスの*documents* コレクションをターゲットとし、ユーザーが入力文字列を 3 文字以上指定した時点から応答が返されます。

## <a name="create-a-suggester"></a>suggester の作成

suggester は、いくつかのプロパティを備えていますが、基本的には先行入力のエクスペリエンスを有効にしているフィールドのコレクションです。 たとえば、旅行アプリでは、目的地、都市、アトラクションなどで先行入力検索を有効にすることがあります。 その場合、3 つのフィールドすべてをフィールド コレクションに追加します。

suggester を作成するために、それを 1 つのインデックス スキーマに追加します。 1 つのインデックスには 1 つの suggester を含めることができます (具体的には、suggesters コレクション内の 1 つの suggester)。 

### <a name="use-the-rest-api"></a>REST API の使用

REST API では、[インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)または[インデックスの更新](https://docs.microsoft.com/rest/api/searchservice/update-index)によって、suggesters を追加できます。 

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
suggester が作成された後、クエリのロジックに [Suggestions API](https://docs.microsoft.com/rest/api/searchservice/suggestions) または [Autocomplete API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) を追加して機能を呼び出します。

### <a name="use-the-net-sdk"></a>.NET SDK を使用する

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

## <a name="property-reference"></a>プロパティ リファレンス

suggesters に関する重要な留意点は、名前 (suggesters は要求上で名前で参照されます)、searchMode (現在は "analyzingInfixMatching" 1 つのみ)、および先行入力が有効になるフィールドの一覧があることです。 

suggester を定義するプロパティは次のとおりです。

|プロパティ      |説明      |
|--------------|-----------------|
|`name`        |suggester の名前。 [Suggestions REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) または[Autocomplete REST API (プレビュー)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) を呼び出すときに、suggester の名前を使用します。|
|`searchMode`  |候補語句の検索に使用する戦略。 現在サポートされている唯一のモードは `analyzingInfixMatching` です。文の先頭または中間にあるフレーズの柔軟なマッチングを実行します。|
|`sourceFields`|検索候補の内容のソースである 1 つまたは複数のフィールドのリスト。 `Edm.String` 型および `Collection(Edm.String)` 型のフィールドだけを、検索候補のソースにできます。 カスタム言語アナライザーが設定されていないフィールドのみを使用できます。<p/>検索バーまたはドロップダウン リストのどちらに入力される文字列でも、想定される適切な応答に利用するフィールドのみを指定します。<p/>ホテル名には有効桁数があるため、適切な選択肢です。 説明やコメントなどの詳細なフィールドは、文字数が多くなりすぎます。 同様に、カテゴリやタグなどの反復的なフィールドでは、効果が低くなります。 例には、複数のフィールドを含めることができることを例示するために、ひとまず、"カテゴリ" を組み入れています。 |

## <a name="when-to-create-a-suggester"></a>suggester を作成するタイミング

インデックスの再構築を回避するには、suggester と `sourceFields` に指定されるフィールドが同時に作成される必要があります。

suggester を既存のインデックスに追加する場合に、既存のフィールドが `sourceFields` に含まれていると、フィールド定義が根本から変更されて再構築が必要になります。 詳しくは、「[Azure Search インデックスを再構築する方法](search-howto-reindex.md)」をご覧ください。

## <a name="how-to-use-a-suggester"></a>suggester の使用方法

前述のように、検索候補クエリ、オートコンプリート、またはその両方に対して suggester を使用できます。 

suggester は要求において、操作に沿って参照されます。 たとえば、GET REST の呼び出しの場合、documents コレクションには `suggest` または `autocomplete` のどちらかを指定します。 REST の場合、suggester が作成された後に、クエリ ロジック内で [Suggestions API](https://docs.microsoft.com/rest/api/searchservice/suggestions) または [Autocomplete API (プレビュー)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) を使用します。

.NET の場合、[SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) または [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet) を使用します。

両方の要求を示した例については、[Azure Search でオートコンプリートと検索候補を追加する例](search-autocomplete-tutorial.md)に関するページをご覧ください。

## <a name="sample-code"></a>サンプル コード

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) サンプルには、C# および Java コードの両方を含み、suggester コンストラクション、検索候補クエリ、オートコンプリート、およびファセット ナビゲーションを例示しています。 

サンドボックスの Azure Search サービスと事前に読み込まれたインデックスを使用してるので、F5 キーを押すだけで実行できます。 サブスクリプションやサインインは必要ありません。

## <a name="next-steps"></a>次の手順

次の例において、要求の作成方法を確認することをお勧めします。

> [!div class="nextstepaction"]
> [検索候補とオートコンプリートの例](search-autocomplete-tutorial.md) 
