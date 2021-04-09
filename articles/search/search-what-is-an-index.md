---
title: インデックスを作成する
titleSuffix: Azure Cognitive Search
description: スキーマ定義や物理データ構造など、Azure Cognitive Search のインデックス作成の概念とツールについて説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 96594d573c308727217f537e5421dcb79f02c2ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433796"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Azure Cognitive Search での検索インデックスの作成

Azure Cognitive Search では、フル テキスト クエリおよびフィルター適用済みクエリに使用される検索可能なコンテンツが "*検索インデックス*" に格納されます。 インデックスは、スキーマによって定義され、サービスに保存されます。2 番目の手順としてデータのインポートが続きます。 

インデックスには "*検索ドキュメント*" が格納されます。 概念的に、ドキュメントはインデックス内で検索可能なデータの 1 つの単位です。 小売業者であれば製品ごとにドキュメントがあり、報道機関であれば記事ごとにドキュメントがあります。 これらの概念をなじみのあるデータベースの同等のものに対応させるなら、*検索インデックス* は *テーブル* と同じで、*ドキュメント* はテーブルにおける *行* とほぼ同じです。

## <a name="whats-an-index-schema"></a>インデックス スキーマとは何ですか。

インデックスの物理的な構造は、スキーマによって決まります。 "フィールド" コレクションは通常、インデックスの最大の部分であり、各フィールドには、名前、[データ型](/rest/api/searchservice/Supported-data-types)の割り当て、および使用方法を決定する許容される動作を示す属性が設定されます。

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

その他の要素は簡潔にするために折りたたまれていますが、[suggester](index-add-suggesters.md)、[スコアリング プロファイル](index-add-scoring-profiles.md)、[アナライザー](search-analyzers.md)の各リンクから詳細を得ることができます。これらは、アナライザーによってサポートされる言語規則やその他の特性と、[クロス オリジン リモート スクリプティング (CORS)](#corsoptions) 設定に従って文字列をトークンに処理するために使用されます。

## <a name="choose-a-client"></a>クライアントを選択する

検索インデックスを作成する方法はいくつかあります。 早期の開発と概念実証のテストには、Azure portal または SDK をお勧めします。

開発時に、頻繁な再構築を計画します。 物理構造はサービス内で作成されるため、既存のフィールド定義への変更のほとんどにおいて[インデックスの削除と再作成](search-howto-reindex.md)が必要です。 リビルドを高速化するために、データのサブセットを使って作業することを検討してもよいでしょう。

### <a name="permissions"></a>アクセス許可

GET 要求を含む、検索インデックスに関連するすべての操作では、要求に対して 1 つの[管理者 API キー](search-security-api-keys.md)が必要です。

### <a name="limits"></a>制限

作成できるオブジェクトの数が、すべての[サービス レベルで制限](search-limits-quotas-capacity.md#index-limits)されています。 Free レベルを試している場合は、所定の期間内に 3 つのインデックスのみを作成できます。

### <a name="use-azure-portal-to-create-a-search-index"></a>Azure portal を使用して検索インデックスを作成する

ポータルには、検索インデックスを作成するための次の 2 つのオプションが用意されています。[**データのインポート ウィザード**](search-import-data-portal.md)と **インデックスの追加** によって、インデックス スキーマを指定するためのフィールドが提供されます。 ウィザードでは、インデクサー、データソース、およびデータの読み込みも作成することにより、追加の操作が組み込まれます。 これだと目的よりも機能が多い場合は、**インデックスの追加** または別の方法を使用する必要があります。

次のスクリーンショットは、ポータルで **インデックスの追加** 機能が見つかる場所を示したものです。 **データのインポート** がすぐそばにあります。

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="[インデックスの追加] コマンド" border="true":::

> [!Tip]
> ポータルを使用したインデックスの設計では、数値フィールドに対してフルテキスト検索機能を許可しないなど、特定のデータ型に対して要件とスキーマ ルールが適用されます。 機能するインデックスを作成したら、ポータルから JSON をコピーして、ソリューションに追加できます。

### <a name="use-a-rest-client"></a>REST クライアントを使用する

Postman と Visual Studio Code (Azure Cognitive Search 用の拡張機能を備えているもの) はどちらも、検索インデックス クライアントとして機能できます。 どちらのツールを使用しても、検索サービスに接続し、[インデックスを作成する (REST)](/rest/api/searchservice/create-index) 要求を送信できます。 REST クライアントを使用してオブジェクトを作成する方法がわかるチュートリアルと例が多数提供されています。 

各クライアントの詳細については、最初に次のいずれかの記事を参照してください。

+ [REST と Postman を使用して検索インデックスを作成する](search-get-started-rest.md)
+ [Visual Studio Code と Azure Cognitive Search の使用を開始する](search-get-started-vs-code.md)

インデックス要求を整理する方法については、「[インデックス操作 (REST)](/rest/api/searchservice/index-operations)」を参照してください。

### <a name="use-an-sdk"></a>SDK を使用する

Cognitive Search の場合、一般公開される機能は Azure SDK によって実装されています。 そのため、いずれかの SDK を使用して検索インデックスを作成できます。 これらのすべてに、インデックスを作成および更新するためのメソッドを含む **SearchIndexClient** が用意されています。

| Azure SDK | Client | 例 |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [azure-search-dotnet-samples/quickstart/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [インデックス](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="define-fields"></a>フィールドを定義する

検索ドキュメントは、`fields` コレクションによって定義されます。 クエリとキーのフィールドが必要になります。 また、フィルター、ファセット、および並べ替えをサポートするフィールドが必要になる場合もあります。 また、ユーザーに表示されないデータのフィールドが必要な場合もあります。たとえば、利益幅やマーケティング プロモーションのためのフィールドを作成して、検索順位の変更に使用できます。

Edm.String 型の 1 つのフィールドをドキュメント キーとして指定する必要があります。 これは各検索ドキュメントを一意に識別するために使用され、大文字と小文字が区別されます。 キーを使用してドキュメントを取得し、詳細ページを設定できます。

受信データ自体が階層化されている場合、入れ子構造を表すために、スキーマには[複合型](search-howto-complex-data-types.md)データ型を割り当てます。 あらかじめ登録されているサンプル データ セットである Hotels (ホテル) は、各ホテルとの一対一のリレーションシップを持つ Address (複数のサブフィールドを含む) と、各ホテルに複数の部屋が関連付けられている複合型コレクションの Rooms を使用した複合型を示しています。 

インデックスを作成する前に、アナライザーを文字列フィールドに割り当てます。 特定のフィールドでオートコンプリートを有効にする場合は、サジェスターに対して同じ操作を行います。

<a name="index-attributes"></a>

### <a name="attributes"></a>属性

フィールド属性は、フィールドがどのように使用されるか (フルテキスト検索、ファセット ナビゲーション、並べ替えなどの操作で使用されるかどうか) を決定します。 

文字列フィールドは多くの場合、"検索可能" および "取得可能" としてマークされます。 検索結果を絞り込むために使用されるフィールドには、"並べ替え可能"、"フィルター可能"、および "ファセット可能" が含まれます。

|属性|説明|  
|---------------|-----------------|  
|"検索可能" |フルテキスト検索可能であり、インデックス作成中の単語分割などの字句解析に従います。 検索可能フィールドを "sunny day" などの値に設定した場合、その値は内部的に個別のトークン "sunny" と "day" に分割されます。 詳細については、「[フルテキスト検索のしくみ](search-lucene-query-architecture.md)」を参照してください。|  
|"フィルター可能" |$filter クエリで参照されます。 型 `Edm.String` または `Collection(Edm.String)` のフィルター可能フィールドは単語分割されないため、比較は完全に一致するかどうかだけになります。 たとえば、このようなフィールドを "sunny day" に設定した場合、`$filter=f eq 'sunny'` では一致が見つかりませんが、`$filter=f eq 'sunny day'` では見つかります。 |  
|"並べ替え可能" |既定では、システムは結果をスコアで並べ替えますが、ドキュメント内のフィールドに基づいて並べ替えを構成できます。 型 `Collection(Edm.String)` のフィールドを "並べ替え可能" にすることはできません。 |  
|"ファセット可能" |通常、カテゴリ (たとえば、特定の市にあるホテル) ごとのヒット カウントを含む検索結果のプレゼンテーションで使用されます。 このオプションは、 `Edm.GeographyPoint`型のフィールドでは使用できません。 "フィルター可能"、"並べ替え可能"、または "ファセット可能" である型 `Edm.String` のフィールドの長さは、最大 32 キロバイトです。 詳細については、「[Create Index (REST API) (インデックスの作成 (REST API))](/rest/api/searchservice/create-index)」を参照してください。|  
|"キー" |インデックス内のドキュメントの一意識別子。 キー フィールドとして正確に 1 つのフィールドを選択する必要があり、それは型 `Edm.String` である必要があります。|  
|"取得可能" |検索結果でこのフィールドを返すことができるかどうかを決定します。 これは、あるフィールド (*利幅* など) をフィルター、並べ替え、またはスコア付けのメカニズムとして使用するが、このフィールドをエンド ユーザーには表示したくない場合に役立ちます。 `true` for `key` である必要があります。|  

いつでも新しいフィールドを追加できますが、既存のフィールド定義はインデックスの有効期間の間ロックされます。 このため、開発者は通常、単純なインデックスを作成したり、アイデアをテストしたり、ポータル ページを使用して設定を検索したりするためのポータルを使用します。 インデックスを容易に再構築できるようにコード ベースのアプローチに従う場合は、インデックス設計を頻繁に反復する方がより効率的です。

> [!NOTE]
> インデックスの作成に使用する API には、さまざまな既定の動作があります。 [REST API](/rest/api/searchservice/Create-Index) の場合、ほとんどの属性は既定で有効であり (たとえば、文字列フィールドの "searchable" および "retrievable" は true です)、無効にする場合は、単にそれらを設定するだけです。 .NET SDK の場合は、逆のことが言えます。 明示的に設定していないプロパティの場合、既定では、特に有効にしない限り、対応する検索動作は無効にされています。

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>属性とインデックスのサイズ (ストレージへの影響)

インデックスのサイズは、アップロードするドキュメントのサイズと、suggester を含めるかどうかや、個々のフィールドに属性を設定する方法など、インデックス構成によって決定されます。 

次のスクリーンショットは、属性のさまざまな組み合わせの結果であるインデックス格納パターンを示しています。 インデックスは **不動産サンプルインデックス** に基づいています。これは、データのインポート ウィザードを使用して簡単に作成できます。 インデックスのスキーマは表示されませんが、インデックス名に基づいて属性を推測できます。 たとえば、*realestate-searchable* インデックスでは "searchable" 属性が選択されていて他には何もなく、*realestate-retrievable* インデックスでは "retrievable" 属性が選択されていて他には何もなく、以下同様です。

![属性の選択に基づいたインデックス サイズ](./media/search-what-is-an-index/realestate-index-size.png "属性の選択に基づいたインデックス サイズ")

これらのインデックスのバリエーションは人為的なものですが、属性がストレージに与える影響の広範な比較のために参照できます。 設定 "retrievable" はインデックスのサイズを増加させますか? いいえ。 **suggester** にフィールドを追加するとインデックスのサイズが増加しますか? はい。 

フィルター処理されたフィールドと並べ替え済みのフィールドは、文字シーケンスを逐語的に照合できるようにトークン化されないため、フィールドをフィルター設定または並べ替え可能にすると、ストレージ消費量も増大します。

また、上記の表に反映されていない事柄に、[アナライザー](search-analyzers.md)の影響があります。 edgeNgram トークナイザーを使用して逐語的な文字シーケンス (a、ab、abc、abcd) を格納した場合、インデックスのサイズは、標準アナライザーを使用した場合よりも大きくなります。

> [!Note]
> ストレージ アーキテクチャは Azure Cognitive Search の実装の詳細と考えられており、予告なく変更されることがあります。 現在の動作が将来も変わらないという保証はありません。

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>`corsOptions` のバージョン情報

インデックス スキーマには、`corsOptions` を設定するためのセクションが含まれます。 ブラウザーではすべてのクロスオリジン要求が禁止されるので、既定ではクライアント側 JavaScript で API を呼び出すことはできません。 インデックスに対するクロスオリジン クエリを許可するには、**corsOptions** 属性を設定することによって、CORS (クロスオリジン リソース共有) を有効にします。 セキュリティ上の理由から、CORS がサポートされているのはクエリ API だけです。 

CORS に対しては以下のオプションを設定できます。

+ **allowedOrigins** (必須):インデックスへのアクセスが許可されるオリジンのリストです。 つまり、これらのオリジンから提供されるすべての JavaScript コードは、インデックスのクエリを許可されます (正しい api-key を提供する場合)。 各オリジンの標準的な形式は `protocol://<fully-qualified-domain-name>:<port>` ですが、多くの場合 `<port>` は省略されます。 詳しくは、「[Cross-origin resource sharing (クロスオリジン リソース共有)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)」(Wikipedia) をご覧ください。

  すべてのオリジンにアクセスを許可する場合は、**allowedOrigins** 配列の唯一の要素として `*` を指定します。 "*運用環境の検索サービスに対してはこれは推奨されません*" が、開発およびデバッグでは役に立つことがよくあります。

+ **maxAgeInSeconds** (省略可能):ブラウザーでは、この値を使用して、CORS プレフライト応答をキャッシュする期間 (秒) が決定されます。 負ではない整数を指定する必要があります。 この値が大きいほどパフォーマンスはよくなりますが、CORS ポリシーの変更が有効になるまでの時間は長くなります。 これを設定しないと、既定の期間として 5 分が使用されます。

## <a name="next-steps"></a>次のステップ

Azure Cognitive Search のインデックスの作成には、ほぼどのサンプルまたはチュートリアルを使用しても、実践の参考にできます。 まず、目次から任意のクイックスタートを選択できます。

ただし、データを使用してインデックスを読み込む方法についても理解しておく必要があります。 インデックスの定義とデータのインポートの方法は、連携して定義されます。 次の記事では、インデックスの読み込みの詳細について説明します。

+ [データ インポートの概要](search-what-is-data-import.md)

+ [ドキュメントの追加、更新、削除 (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 