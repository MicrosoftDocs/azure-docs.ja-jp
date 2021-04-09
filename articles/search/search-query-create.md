---
title: クエリを作成する
titleSuffix: Azure Cognitive Search
description: Cognitive Search でクエリ要求を作成する方法、テストおよびコードに使用するツールと API、およびどのようにしてインデックスのデザインからクエリ決定が開始されるのかについて説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 4f5cc0d5eefd5969566040e4148ca7358d348736
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951506"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Azure Cognitive Search でのクエリの作成

クエリを初めて作成する場合、この記事ではクエリを設定するためのアプローチとメソッドについて説明します。 また、クエリ要求の概要を示し、フィールド属性と言語アナライザーによってクエリ結果がどのような影響を受けるかについても説明します。

## <a name="whats-a-query-request"></a>クエリ要求とは

クエリとは、単一の検索インデックスのドキュメント コレクションに対する読み取り専用の要求です。 ここでは、'Search' パラメーターを指定します。また、語句、引用符で囲まれたフレーズ、および演算子で構成されたクエリ式が含まれます。

追加のパラメーターを使用すると、クエリと応答をより詳細に定義することができます。 たとえば、'searchFields' では特定のフィールドに対してクエリを実行します。'select' では結果で返されるフィールドを指定し、'count' ではインデックス内で見つかった一致の数を返します。

次の例では、使用可能なパラメーターのサブセットを示すことにより、クエリ要求の一般的な概念を示します。 クエリの構成の詳細については、[クエリの種類と構成](search-query-overview.md)および[ドキュメントの検索 (REST)](/rest/api/searchservice/search-documents) に関する記事を参照してください。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>クライアントを選択する

Azure portal や Postman などのツール、または API を使用してクエリ クライアントをインスタンス化するコードが必要です。 早期の開発と概念実証のテストには、Azure portal または REST API をお勧めします。

### <a name="permissions"></a>アクセス許可

クエリ要求を含むすべての操作は[管理 API キー](search-security-api-keys.md)で動作しますが、クエリ要求は必要に応じて[クエリ API キー](search-security-api-keys.md#create-query-keys)を使用できます。 クエリ API キーを強くお勧めします。 サービスあたり最大 50 個作成でき、アプリケーションごとに異なるキーを割り当てることができます。

Azure portal では、ツール、ウィザード、およびオブジェクトへのアクセスには、サービスに対する共同作成者ロール以上のメンバーシップが必要です。 

### <a name="use-azure-portal-to-query-an-index"></a>Azure portal を使用してインデックスに対してクエリを実行する

[検索エクスプローラー (ポータル)](search-explorer.md) は、Azure portal のクエリ インターフェイスで、基になる検索サービスでインデックスに対してクエリを実行します。 内部的には、ポータルは[ドキュメントの検索](/rest/api/searchservice/search-documents)要求を行いますが、オートコンプリート、提案、またはドキュメント検索を呼び出すことはできません。 

プレビューを含む、任意のインデックスと REST API バージョンを選択できます。 クエリ文字列では、すべてのクエリ パラメーター (filter、select、searchFields など) をサポートする単純な構文または完全な構文を使用できます。 ポータルでは、インデックスを開くと、フィールド属性に簡単にアクセスできるように、横に並んだタブでインデックス JSON 定義と共に検索エクスプローラーを操作できます。 クエリのテスト中に、検索可能、並べ替え可能、フィルター可能、およびファセット可能の各フィールドを確認します。

### <a name="use-a-rest-client"></a>REST クライアントを使用する

Postman と Visual Studio Code (Azure Cognitive Search 用の拡張機能を備えているもの) はどちらも、クエリ クライアントとして機能できます。 どちらのツールを使用しても、検索サービスに接続し、[ドキュメントの検索 (REST)](/rest/api/searchservice/search-documents) 要求を送信できます。 インデックスに対するクエリの実行のための REST クライアントを示すチュートリアルと例が多数提供されています。 

各クライアントの詳細については、最初にこれらのいずれかの記事を参照してください (どちらにもクエリの説明が含まれています)。

+ [REST と Postman を使用して検索インデックスを作成する](search-get-started-rest.md)
+ [Visual Studio Code と Azure Cognitive Search の使用を開始する](search-get-started-vs-code.md)

各要求はスタンドアロンであるため、要求ごとにエンドポイント、インデックス名、および API バージョンを指定する必要があります。 その他のプロパティ、Content-Type、API キーは、要求ヘッダーで渡されます。 詳細については、[ドキュメントの検索 (REST)](/rest/api/searchservice/search-documents) に関する記事で、クエリ要求の作成について参照してください。

### <a name="use-an-sdk"></a>SDK を使用する

Cognitive Search の場合、一般公開される機能は Azure SDK によって実装されています。 そのため、いずれかの SDK を使用してインデックスに対するクエリを実行できます。 これらのすべてには、ドキュメントの検索を使用したインデックスの読み込みから、クエリ要求の作成に至るまでの、インデックスと対話するためのメソッドを持つ **SearchClient** が用意されています。

| Azure SDK | Client | 例 |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | 保留中  |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query.py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>クエリの種類を選択する: simple | full

クエリがフルテキスト検索の場合、検索語句やフレーズとして渡されたテキストを処理するためにクエリ パーサーが使用されます。Azure Cognitive Search には 2 つのクエリ パーサーが用意されています。 

+ 単純なパーサーは、[単純なクエリ構文](query-simple-syntax.md)を認識します。 このパーサーは、自由形式のテキスト クエリの速度と有効性により既定で選択されています。 この構文では、用語検索と語句検索に共通の検索演算子 (AND、OR、NOT) とプレフィックス (`*`) 検索をサポートしています (Seattle や Seaside に対する "sea*" など)。 一般的には、最初に単純なパーサーを試してから、アプリケーションの要件でより強力なクエリが必要になる場合に完全なパーサーに移行することをお勧めします。

+ [完全な Lucene クエリ構文](query-Lucene-syntax.md#bkmk_syntax)は、`queryType=full` を要求に追加したときに有効になり、[Apache Lucene パーサー](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)に基づいています。

完全な構文と単純な構文は、両方が同じプレフィックスとブール演算をサポートしているという点で重複しますが、完全な構文のほうがより多くの演算子を用意しています。 完全では、ブール式の演算子や、あいまい検索、ワイルドカード検索、近接検索、正規表現などの高度なクエリ用の演算子が多数用意されています。

## <a name="choose-query-methods"></a>クエリ メソッドを選択する

検索は基本的にユーザー主導の動作であり、検索ボックスから、またはページでのクリック イベントから、用語や語句が収集されます。 次の表は、予想される検索エクスペリエンスと共にユーザー入力を収集するためのメカニズムをまとめたものです。

| 入力 | エクスペリエンス |
|-------|---------|
| [検索メソッド](/rest/api/searchservice/search-documents) | ユーザーは、演算子の有無にかかわらず検索ボックスに用語または語句を入力し、[検索] をクリックして要求を送信します。 検索は同じ要求でフィルターと共に使用できますが、オートコンプリートや提案と共には使用できません。 |
| [オートコンプリート メソッド](/rest/api/searchservice/autocomplete) | ユーザーがいくつかの文字を入力すると、新しい文字が入力されるたびにクエリが開始されます。 応答は、インデックスからの完成した文字列になります。 指定された文字列が有効な場合、ユーザーは [検索] をクリックしてそのクエリをサービスに送信します。 |
| [提案メソッド](/rest/api/searchservice/suggestions) | オートコンプリートと同様に、ユーザーがいくつかの文字を入力すると、増分クエリが生成されます。 応答は一致したドキュメントのドロップダウン リストであり、通常はいくつかの一意または説明のフィールドで表されます。 いずれかの選択が有効な場合、ユーザーが 1 つをクリックすると、一致したドキュメントが返されます。 |
| [ファセット ナビゲーション](/rest/api/searchservice/search-documents#query-parameters) | ページには、クリック可能なナビゲーション リンクまたは階層リンクが表示され、検索範囲を絞り込むことができます。 ファセット ナビゲーション構造は、初期クエリに基づいて動的に構成されます。 たとえば、`search=*` は、可能なすべてのカテゴリから構成されるファセット ナビゲーション ツリーを設定します。 ファセット ナビゲーション構造はクエリ応答から作成されますが、次のクエリを表現するためのメカニズムでもあります。 REST API リファレンスでは、`facets` は、ドキュメントの検索操作のクエリ パラメーターとして記述されていますが、`search` パラメーターを指定せずに使用できます。|
| [フィルター メソッド](/rest/api/searchservice/search-documents#query-parameters) | フィルターは、結果を絞り込むためにファセットと共に使用されます。 また、ページの背後にフィルターを実装することもでき、たとえば、言語固有のフィールドでページを初期化することができます。 REST API リファレンスでは、`$filter` はドキュメントの検索操作のクエリ パラメーターとして記述されていますが、`search` パラメーターを指定せずに使用できます。|

## <a name="know-your-field-attributes"></a>フィールド属性を把握する

以前に[クエリの種類と構成](search-query-overview.md)を確認した場合、クエリ要求のパラメーターは、インデックスでフィールドにどのような属性が付けられているかによって異なるということを覚えているかもしれません。 たとえば、クエリ、フィルター、または並べ替え順序で使用するには、フィールドを *検索可能*、*フィルター可能*、および *並べ替え可能* にする必要があります。 同様に、*取得可能* としてマークされたフィールドのみを結果に表示することができます。 要求で `search`、`filter`、および `orderby` パラメーターを指定し始めるときには、予期しない結果を避けるために、必ず属性を確認してください。

[ホテルのサンプル インデックス](search-get-started-portal.md)の下にあるポータルのスクリーンショットでは、`"$orderby"` だけの句で使用できるのは、"LastRenovationDate" と "Rating" の最後の 2 つのフィールドだけです。

![ホテル サンプルのインデックス定義](./media/search-query-overview/hotel-sample-index-definition.png "ホテル サンプルのインデックス定義")

フィールド属性の詳細については、[インデックス (REST API) の作成](/rest/api/searchservice/create-index)に関するページをご覧ください。

## <a name="know-your-tokens"></a>トークンを把握する

インデックス作成中、検索エンジンは文字列のテキスト分析を実行するためにアナライザーを使用し、クエリ時の照合の可能性を最大限に高めます。 少なくとも文字列は小文字に変換されますが、レンマ化とストップ ワードの削除も行われる可能性があります。 通常、長い文字列や複合語は、空白、ハイフン、またはダッシュによって分割され、個別のトークンとしてインデックスが付けられます。 

ここでの要点は、インデックスに含まれていると思われることと、実際に含まれていることは異なる場合があるということです。 クエリで予想された結果が返されない場合は、[分析テキスト (REST API)](/rest/api/searchservice/test-analyzer) を通じてアナライザーによって作成されたトークンを検査できます。 トークン化とクエリへの影響の詳細については、「[部分的な用語検索と特殊文字を含むパターン](search-query-partial-matching.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

これで、クエリ要求のしくみについての理解が深まったので、次のクイックスタートで実際に体験してみてください。

+ [Search エクスプローラー](search-explorer.md)
+ [REST におけるクエリの実行方法](search-get-started-rest.md)
+ [.NET におけるクエリの実行方法](search-get-started-dotnet.md)
+ [Python におけるクエリの実行方法](search-get-started-python.md)
+ [JavaScript におけるクエリの実行方法](search-get-started-javascript.md)