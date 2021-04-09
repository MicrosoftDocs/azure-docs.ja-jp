---
title: JSON BLOB による検索
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search Blob インデクサーを使用してテキスト コンテンツのために Azure JSON BLOB をクロールします。 インデクサーにより、選択したデータ ソース (Azure Blob Storage など) のデータ インジェストが自動化されます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259052"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Azure Cognitive Search で BLOB インデクサーを使用して JSON BLOB のインデックスを作成する方法

この記事では、JSON ドキュメントで構成される BLOB に対して [BLOB インデクサーを構成する](search-howto-indexing-azure-blob-storage.md)方法について説明します。 Azure Blob Storage の JSON BLOB では、一般に次のような形式が想定されています。

+ 1 つの JSON ドキュメント
+ 整形式の JSON 要素の配列を含む JSON ドキュメント
+ 改行で区切られた複数のエンティティを含む JSON ドキュメント

BLOB インデクサーには、構造に基づいて検索ドキュメントの出力を最適化する **`parsingMode`** パラメーターが用意されています。解析モードは次のオプションで構成されます。

| parsingMode | JSON ドキュメント | 説明 |
|--------------|-------------|--------------|
| **`json`** | BLOB あたり 1 つ | (既定) JSON BLOB を 1 つのテキスト チャンクとして解析します。 各 JSON BLOB が、1 つの検索ドキュメントになります。 |
| **`jsonArray`** | BLOB あたり複数 | 配列の各要素が別々の検索ドキュメントになる、BLOB 内の JSON 配列を解析します。  |
| **`jsonLines`** | BLOB あたり複数 | 個々の要素が改行で区切られた複数の JSON エンティティ (配列) を含む BLOB を解析します。 インデクサーにより、改行ごとに新しい検索ドキュメントが開始されます。 |

**`jsonArray`** と **`jsonLines`** の両方について、[1 つの BLOB にインデックスを付けて多数の検索ドキュメントを生成する方法](search-howto-index-one-to-many-blobs.md)に関するページを確認し、同じ BLOB から生成された複数の検索ドキュメントのドキュメント キーのあいまいさ排除が BLOB インデクサーによってどのように処理されるかを理解する必要があります。

インデクサーの定義では、必要に応じて、[フィールド マッピング](search-indexer-field-mappings.md)を設定して、ターゲットの検索インデックスの設定に使用されるソース JSON ドキュメントのプロパティを選択できます。 たとえば、 **`jsonArray`** 解析モードを使用しているときに、配列が下位レベルのプロパティとして存在する場合は、BLOB 内での配列の配置場所を示す **`document root`** プロパティを設定できます。

以下のセクションで、各モードについて詳しく説明します。 インデクサーのクライアントと概念の詳細については、[検索インデクサーの作成](search-howto-create-indexers.md)に関するページを参照してください。 また、[基本的な BLOB インデクサーの構成](search-howto-indexing-azure-blob-storage.md)の詳細についても理解しておく必要があります。これについては、ここでは説明しません。

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>1 つの JSON ドキュメントにインデックスを付ける (BLOB ごとに 1 つ)

既定では、BLOB インデクサーによって JSON BLOB が 1 つのテキスト チャンクとして、コンテナー内の BLOB ごとに 1 つの検索ドキュメントに解析されます。 JSON が構造化されている場合、検索ドキュメントはその構造を反映でき、個々の要素が個々のフィールドとして表されます。 たとえば、Azure Blob Storage に次の JSON ドキュメントがあると仮定します。

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

BLOB インデクサーにより、JSON ドキュメントが 1 つの検索ドキュメントに解析され、ソースの "text"、"datePublished"、"tags" が、同じ名前と型のターゲット インデックス フィールドと照合されて、インデックスが読み込まれます。 "text"、"datePublished、"tags" のフィールドでインデックスを指定すると、BLOB インデクサーは、要求にフィールド マッピングがない場合でも正しいマッピングを推測することができます。

既定の動作は JSON BLOB ごとに 1 つの検索ドキュメントですが、'json' 解析モードを設定すると、コンテンツの内部フィールド マッピングが変更され、`content` 内のフィールドが検索インデックスの実際のフィールドに昇格します。 **`json`** 解析モードのインデクサー定義の例は次のようになります。

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> すべてのインデクサーと同様に、フィールドが明確に一致しない場合は、[基本的な BLOB インデクサーの構成](search-howto-indexing-azure-blob-storage.md)に関するページで説明されているように、BLOB のコンテンツとメタデータに使用できる暗黙のフィールド マッピングを使用しない限り、個々の[フィールド マッピング](search-indexer-field-mappings.md)を明示的に指定する必要があります。

### <a name="json-example-single-hotel-json-files"></a>json の例 (1 つの hotel JSON ファイル)

GitHub の [hotel JSON ドキュメント データ セット](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents)は、各 BLOB が構造化 JSON ファイルを表す JSON 解析のテストに役立ちます。 データ ファイルを BLOB ストレージにアップロードし、 **[データのインポート]** ウィザードを使用して、このコンテンツが個々の検索ドキュメントにどのように解析されるかをすばやく評価できます。 

データ セットは 5 つの BLOB で構成され、それぞれに address コレクションと rooms コレクションを含む hotel ドキュメントが含まれています。 BLOB インデクサーによって両方のコレクションが検出され、インデックス スキーマ内の入力ドキュメントの構造が反映されます。

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON 配列を解析する

別の方法として、[JSON 配列] オプションを使用できます。 このオプションは、BLOB に整形式の JSON オブジェクトの配列が含まれ、各要素を個別の検索ドキュメントにする場合に役立ちます。 次の JSON BLOB では、 **`jsonArrays`** を使用して、それぞれ `"id"` フィールドと `"text"` フィールドを持つ 3 つの個別のドキュメントが生成されます。  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

インデクサーの **`parameters`** プロパティには、解析モード値が含まれています。 JSON 配列の場合、インデクサーの定義は次の例のようになります。

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>jsonArrays の例 (clinical trials サンプル データ)

GitHub の [clinical trials JSON データ セット](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json)は、JSON 配列解析のテストに役立ちます。 データ ファイルを BLOB ストレージにアップロードし、 **[データのインポート]** ウィザードを使用して、このコンテンツが個々の検索ドキュメントにどのように解析されるかをすばやく評価できます。 

データ セットは 8 つの BLOB で構成され、それぞれにエンティティの JSON 配列が含まれており、エンティティは合計 100 個になります。 エンティティは、どのフィールドに値が入力されるかによって異なりますが、最終的な結果は、すべての BLOB のすべての配列で、エンティティごとに 1 つの検索ドキュメントが生成されます。

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>入れ子になった JSON 配列の解析

入れ子になった要素を持つ JSON 配列では、 **`documentRoot`** を指定して、複数レベルの構造であることを示すことができます。 たとえば、次のような BLOB があるとします。

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

この構成を使用して、`level2` プロパティに格納されている配列にインデックスを付けます。

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>改行で区切られた JSON エンティティを解析する

BLOB に改行で分離された複数の JSON エンティティが含まれ、各要素を独立した検索ドキュメントにする場合は、 **`jsonLines`** を使用します。

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

JSON 行では、インデクサーの定義は次の例のようになります。

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>jsonLines の例 (caselaw サンプル データ)

GitHub の [caselaw JSON データ セット](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw)は、JSON 改行解析のテストに役立ちます。 他のサンプルと同様に、このデータを BLOB ストレージにアップロードし、 **[データのインポート]** ウィザードを使用して、個々の BLOB に対する解析モードの影響をすばやく評価できます。

データ セットは、改行で区切られた 10 個の JSON エンティティを含む 1 つの BLOB で構成されています。各エンティティは 1 つの訴訟を表します。 最終的には、エンティティごとに 1 つの検索ドキュメントが生成されます。

## <a name="map-json-fields-to-search-fields"></a>JSON フィールドを検索フィールドにマップする

フィールドの名前と型が同じでない場合は、フィールド マップを使用して、ソース フィールドとマップ先フィールドを関連付けます。 ただし、フィールド マッピングを使用すると、JSON ドキュメントの一部を照合して、検索ドキュメントの最上位レベルのフィールドに "引き上げる" こともできます。

このシナリオを次の例で説明します。 フィールド マッピングの詳細については、[フィールド マッピング](search-indexer-field-mappings.md)に関するページを参照してください。

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

`Edm.String` 型の `text` フィールド、`Edm.DateTimeOffset` 型の `date` フィールド、`Collection(Edm.String)` 型の `tags` フィールドを持つ検索インデックスがあるとします。 ソースの "datePublished" とインデックスの `date` フィールド間の違いに注目してください。 JSON を必要な形式にマッピングするには、次のフィールド マッピングを使用します。

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

ソース フィールドは、[JSON ポインター](https://tools.ietf.org/html/rfc6901)の表記を使用して指定されています。 スラッシュから始めて、JSON ドキュメントのルートを参照し、その後、スラッシュ区切りのパスを使用して、目的のプロパティ (任意の入れ子レベル) まで指定します。

0 から始まるインデックスを使用して個々の配列要素を参照することもできます。 たとえば、上の例から "tags" 配列の最初の要素を選択するには、次のようなフィールド マッピングを使用します。

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> **`sourceFieldName`** が JSON BLOB に存在しないプロパティを参照している場合、そのマッピングはエラーなしでスキップされます。 この動作により、スキーマが異なる JSON BLOB に対してインデックス作成を続行できます (一般的なユース ケース)。 検証チェックが行われないので、不当な理由でドキュメントが失われないように、マッピングに入力ミスがないかよくご確認ください。
>

## <a name="next-steps"></a>次のステップ

+ [BLOB インデクサーの構成](search-howto-indexing-azure-blob-storage.md)
+ [フィールド マッピングの定義](search-indexer-field-mappings.md)
+ [インデクサーの概要](search-indexer-overview.md)
+ [BLOB インデクサーを使用して CSV BLOB にインデックスを付ける方法](search-howto-index-csv-blobs.md)
+ [チュートリアル: Azure Blob Storage で半構造化データを検索する](search-semi-structured-data.md)