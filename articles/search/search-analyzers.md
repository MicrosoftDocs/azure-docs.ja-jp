---
title: 言語処理とテキスト処理のためのアナライザー
titleSuffix: Azure Cognitive Search
description: インデックスの検索可能なテキスト フィールドにアナライザーを割り当てて、既定の標準 Lucene をカスタムの定義済みの代替または言語固有の代替と置換します。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: d40dd0b91f9dcfb7bf5b6e8f084f25ee4f90d780
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596554"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Azure Cognitive Search でのテキスト処理のためのアナライザー

*アナライザー* は、クエリ文字列内のテキストとインデックス付きドキュメントを処理する [フル テキスト検索](search-lucene-query-architecture.md)のコンポーネントです。 テキスト処理 (字句解析とも呼ばれます) は変形力を備えていて、以下のようなアクションを通してクエリ文字列に変更を加えます。

+ 重要でない単語 (ストップワード) と句読点を削除します
+ フレーズやハイフンでつながれた単語を構成部分に分割します
+ 大文字になっている単語があれば小文字に変換します
+ ストレージの効率性のために単語をプリミティブな原形に単純化し、時制に関係なく一致を見つけられるようにする

分析は、"検索可能" としてマークされている `Edm.String` フィールドに適用されます。これがフルテキスト検索を指定します。 

この構成になっているフィールドの場合、トークンが作成されるときのインデックス作成中に分析が行われ、その後、クエリが解析されてエンジンが一致するトークンをスキャンするクエリの実行中に、再度分析されます。 インデックス作成とクエリの両方に同じアナライザーが使用されると一致が発生する可能性が高くなりますが、要件に応じて、各ワークロードに向けて個別にアナライザーを設定できます。

フィルタやあいまい検索など、フルテキスト検索 *ではない* 種類のクエリでは、クエリ側で分析フェーズは行われません。 代わりに、パーサーは、一致の基準としてユーザーが指定したパターンを使用して、これらの文字列を検索エンジンに直接送信します。 通常、これらのクエリ フォームでは、パターン マッチングを機能させるために文字列全体のトークンが必要です。 インデックス作成中に用語全体のトークンを取得するには、[カスタム アナライザー](index-add-custom-analyzers.md)が必要になることがあります。 クエリ用語が分析されるタイミングとその理由の詳細については、「[Azure Cognitive Search でのフルテキスト検索](search-lucene-query-architecture.md)」を参照してください。

字句解析の背景については、次のビデオ クリップで簡単な説明を聴いてください。

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>既定のアナライザー  

Azure Cognitive Search のクエリでは、検索可能とマークされているすべての文字列フィールドに対して、アナライザーが自動的に呼び出されます。 

Azure Cognitive Search の既定では、["Unicode テキストのセグメント化"](https://unicode.org/reports/tr29/) 規則に従ってテキストを要素に分割する、[Apache Lucene 標準アナライザー (標準 Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) を使用します。 さらに、標準アナライザーはすべての文字を小文字形式に変換します。 インデックス付きドキュメントと検索語句の両方について、インデックス作成とクエリ処理の間に分析が行われます。  

フィールド単位で既定値をオーバーライドすることができます。 代わりのアナライザーとしては、言語処理用の[言語アナライザー](index-add-language-analyzers.md)、[カスタム アナライザー](index-add-custom-analyzers.md)、または[使用可能なアナライザーの一覧](index-add-custom-analyzers.md#built-in-analyzers)に記載されている組み込みアナライザーを使用できます。

## <a name="types-of-analyzers"></a>アナライザーの種類

次の一覧では、Azure Cognitive Search で使用可能なアナライザーについて説明しています。

| カテゴリ | 説明 |
|----------|-------------|
| [標準 Lucene のアナライザー](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | 既定値。 指定や構成は必要ありません。 この汎用アナライザーは、多くの言語とシナリオで適切に実行されます。|
| 組み込みアナライザー | そのまま使用され、名前で参照されます。 言語の型と言語に依存しない型の 2 つの型があります。 </br></br>[特殊 (言語を選ばない) アナライザー](index-add-custom-analyzers.md#built-in-analyzers)は、特殊な処理または最小限の処理が必要なテキスト入力に使用します。 このカテゴリのアナライザーの例には、 **Asciifolding**、 **Keyword**、 **Pattern**、 **Simple**、 **Stop**、 **Whitespace** などがあります。 </br></br>[言語アナライザー](index-add-language-analyzers.md)は、各言語に合わせて高度の言語サポートが必要な場合に使用されます。 Azure Cognitive Search は、35 個の Lucene 言語アナライザーと 50 個の Microsoft 自然言語処理アナライザーをサポートしています。 |
|[カスタム アナライザー](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 1 つのトークナイザー (必須) と省略可能なフィルター (文字またはトークン) から構成される既存の要素を組み合わせたユーザー定義の構成のことです。|

**Pattern** や **Stop** など、いくつかの組み込みアナライザーは、限られた構成オプションしかサポートしていません。 これらのオプションを設定するには、[組み込みアナライザー](index-add-custom-analyzers.md#built-in-analyzers)で文書化されている組み込みアナライザーと代替オプションの 1 つで構成されるカスタム アナライザーを作成します。 他のカスタム構成と同様に、新しい構成に *myPatternAnalyzer* などの名前を付けて、Lucene パターン アナライザーの名前を区別できるようにします。

## <a name="how-to-specify-analyzers"></a>アナライザーを指定する方法

アナライザーの設定は省略可能です。 一般的な規則として、どれだけ意図したように機能するかを確認するため、最初に既定の標準 Lucene アナライザーを使用してみます。 クエリで期待した結果が返されない場合は、異なるアナライザーに切り替えることが正しい解決策であることがよくあります。

1. [インデックス](/rest/api/searchservice/create-index)にフィールド定義を作成する場合は、"analyzer" プロパティを次のいずれかに設定します。**keyword** などの [定義済みアナライザー](index-add-custom-analyzers.md#built-in-analyzers)、`en.microsoft` などの [言語アナライザー](index-add-language-analyzers.md)、またはカスタム アナライザー (同じインデックス スキーマで定義されたもの) です。  
 
   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
   ```

   [言語アナライザー](index-add-language-analyzers.md)を使おうとしている場合は、"analyzer" プロパティを使用して指定する必要があります。 "searchAnalyzer" プロパティと "indexAnalyzer" プロパティでは、言語アナライザーはサポートされていません。

1. または、"indexAnalyzer" と "searchAnalyzer" を設定して、ワークロードごとにアナライザーを変更します。 これらのプロパティは一緒に設定され、"analyzer" プロパティを置き換えます。これは null である必要があります。 インデックス化やクエリのどちらかの操作で、他方の処理には必要がない特定の変換が必要な場合は、インデックス化とクエリに異なるアナライザーを使用することもできます。

   ```json
     "fields": [
    {
      "name": "ProductGroup",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "indexAnalyzer": "keyword",
      "searchAnalyzer": "standard"
    },
   ```

1. カスタム アナライザーの場合のみ、インデックスの **[analyzers]** セクションにエントリを作成してから、前の 2 つの手順のいずれかに従って、フィールド定義にカスタム アナライザーを割り当てます。 詳細については、[インデックスの作成](/rest/api/searchservice/create-index)および[カスタム アナライザーの追加](index-add-custom-analyzers.md)に関する記事を参照してください。

## <a name="when-to-add-analyzers"></a>アナライザーを追加する時期

アナライザーを追加して割り当てる最適な時期は、アクティブな開発中、インデックスの削除と再作成がルーチンである時期です。

アナライザーは、用語をトークン化するために使用されるため、フィールドの作成時にアナライザーを割り当てる必要があります。 実際には、物理的に作成済みのフィールドに analyzer または indexAnalyzer を割り当てることは許可されていません (ただし、インデックスに影響を与えることなく、いつでも searchAnalyzer プロパティを変更できます)。

既存のフィールドのアナライザーを変更するには、[インデックスを完全に再構築する](search-howto-reindex.md)必要があります (個々のフィールドを再構築することはできません)。 運用環境のインデックスの場合は、新しいアナライザーの割り当てを指定した新しいフィールドを作成することで再構築を延期し、古いものの代わりに使用を開始することができます。 新しいフィールドを組み込むには [Update Index](/rest/api/searchservice/update-index) を使用し、それを事前設定するには [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) を使用してください。 後で、計画的なインデックス サービスの一環として、インデックスをクリーンアップし、不要になったフィールドを削除することができます。

既存のインデックスに新しいフィールドを追加するには、[Update Index](/rest/api/searchservice/update-index) を呼び出してフィールドを追加し、[mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) を呼び出してそこにデータを格納します。

既存のインデックスにカスタム アナライザーを追加し、次のエラーを回避する場合は、[Update Index](/rest/api/searchservice/update-index) で "allowIndexDowntime" フラグを渡します。

*"ダウンタイムが発生するため、インデックスの更新は許可されていません。新しいアナライザー、トークナイザー、トークン フィルター、または文字フィルターを既存のインデックスに追加するためには、インデックス更新要求で 'allowIndexDowntime' クエリ パラメーターを 'true' に設定してください。この操作によりインデックスが少なくとも数秒間オフラインになるため、インデックス作成とクエリ要求が失敗することに注意してください。インデックスを更新すると、インデックスのパフォーマンスと書き込み可用性が数分にわたり損なわれる場合があります。インデックスが非常に大きい場合、その時間も長くなります。"*

## <a name="recommendations-for-working-with-analyzers"></a>アナライザーの使用方法に関する推奨事項

このセクションでは、アナライザーの使用方法についてのアドバイスを提供します。

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>特定の要件がない場合は読み取り/書き込みに 1 つのアナライザー

Azure Cognitive Search では、追加の indexAnalyzer および searchAnalyzer フィールド プロパティにより、インデックス作成用と検索用に異なるアナライザーを指定できます。 指定しなかった場合、analyzer プロパティで設定されたアナライザーが、インデックス作成と検索の両方に使用されます。 アナライザーが指定されなかった場合は、標準の Lucene アナライザーが使用されます。

一般的な規則は、特定の要件で別に指示がない場合は、インデックスとクエリの両方に同じアナライザーを使用することです。 テストは徹底的に行ってください。 検索時とインデックス作成時にテキスト処理が異なると、検索アナライザーの構成とインデックス作成アナライザーの構成が揃っていない場合、クエリ用語とインデックス用語に不一致が生じるおそれがあります。

### <a name="test-during-active-development"></a>アクティブな開発中のテスト

標準アナライザーをオーバーライドするには、インデックスの再構築が必要です。 可能であれば、アクティブな開発中に使用するアナライザーを決めてから、インデックスを運用環境に展開します。

### <a name="inspect-tokenized-terms"></a>トークン化された用語の検査

検索結果が期待した内容ではない場合、よくあるシナリオとして、クエリの用語入力とインデックス内のトークン化された用語間にトークンの違いがあることが考えられます。 トークンが同じではない場合、一致処理は具体化に失敗します。 トークナイザーの出力を検査する場合、調査ツールとして [Analyze API](/rest/api/searchservice/test-analyzer) を使用することをお勧めします。 応答は、特定のアナライザーによって生成されるトークンで構成されます。

<a name="examples"></a>

## <a name="rest-examples"></a>REST の例

いくつかの主なシナリオについて、アナライザーの定義例を示します。

+ [カスタム アナライザーの例](#Custom-analyzer-example)
+ [アナライザーをフィールドに割り当てる例](#Per-field-analyzer-assignment-example)
+ [インデックス作成用と検索用のアナライザーを混在させる](#Mixing-analyzers-for-indexing-and-search-operations)
+ [言語アナライザーの例](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>カスタム アナライザーの例

この例では、カスタム オプションを使用してアナライザー定義を示しています。 文字フィルター、トークナイザー、トークン フィルターのオム オプションは、名前付きコンストラクトとは別に指定され、アナライザー定義で参照されます。 定義済みの要素はそのまま使用され、単純に名前で参照されます。

この例について手順を説明します。

+ アナライザーは、検索可能なフィールドのフィールド クラスのプロパティです。

+ カスタム アナライザーは、インデックス定義の一部です。 軽くカスタマイズされているか (1 つのフィルターの 1 つのオプションがカスタマイズされているなど)、複数個所でカスタマイズされている可能性があります。

+ この例で、カスタム アナライザーは "my_analyzer" です。このアナライザーはカスタマイズされた標準トークナイザー "my_standard_tokenizer" と、小文字とカスタマイズされた asciifolding フィルターである "my_asciifolding" という 2 つのトークン フィルターを使用しています。

+ 2 つのカスタム文字フィルター "map_dash" および "remove_whitespace" も定義します。 1 つ目はすべてのダッシュをアンダースコアに置き換え、2 つ目はすべてのスペースを削除します。 マッピング規則でスペースを UTF-8 エンコードする必要があります。 文字フィルターはトークン化よりも先に適用され、結果のトークンに影響を及ぼします (標準トークナイザーはダッシュとスペースで区切りますが、アンダースコアでは区切りません)。

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>フィールドごとのアナライザー割り当ての例

標準アナライザーが既定です。 たとえば、既定のアナライザーを、パターン アナライザーなど、別の定義済みアナライザーで置き換えるとします。 カスタム オプションを設定していない場合、フィールド定義で名前を使用して指定する必要があります。

"analyzer" 要素は、フィールドごとに標準アナライザーをオーバーライドします。 グローバルなオーバーライドはありません。 この例で、`text1` はパターン アナライザーと `text2` を使用します。これはアナライザーを指定せず、既定値を使用します。

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
```

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>インデックス作成と検索の各操作用のアナライザーを混在させる

API には、インデックス作成と検索に別のアナライザーを指定するための追加のインデックス属性が含まれています。 searchAnalyzer 属性と indexAnalyzer 属性をペアで指定し、1 つの analyzer 属性を置き換える必要があります。


```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
```

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>言語アナライザーの例

複数言語の文字列を含むフィールドでは、言語アナライザーを使用できますが、他のフィールドは既定値のままです (または、他の定義済みまたはカスタム アナライザーを使用します)。 言語アナライザーを使用する場合、インデックス作成と検索操作の両方に使用する必要があります。 言語アナライザーを使用するフィールドでは、インデックス作成と検索に別のアナライザーを使用することはできません。

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
```

## <a name="c-examples"></a>C# の例

.NET SDK のサンプル コードを使用している場合は、アナライザーを使用または構成するためにこれらのサンプルを追加できます。

+ [組み込みのアナライザーを割り当てる](#Assign-a-language-analyzer)
+ [アナライザーを構成する](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>言語アナライザーを割り当てる

構成なしでそのまま使用されるすべてのアナライザーは、フィールド定義で指定します。 インデックスの **[analyzers]** セクションにエントリを作成する必要はありません。 

言語アナライザーはそのままで使用されます。 それらを使用するには、Azure Cognitive Search でサポートされているテキスト アナライザーを提供する [LexicalAnalyzerName](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) 型を指定して、[LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) を呼び出します。

カスタム アナライザーも、フィールド定義に同じように指定されますが、これを機能させるには、次のセクションで説明するように、インデックス定義にアナライザーを指定する必要があります。

```csharp
    public partial class Hotel
    {
       . . . 
        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(AnalyzerName = "url-analyze")]
        public string Url { get; set; }
      . . .
    }
```

<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>カスタム アナライザーを定義する

カスタマイズまたは構成が必要な場合は、アナライザー コンストラクトをインデックスに追加します。 定義したら、前の例で示したようにそれをフィールド定義に追加できます。

[CustomAnalyzer](/dotnet/api/azure.search.documents.indexes.models.customanalyzer) オブジェクトを作成します。 カスタム アナライザーは、既知のトークナイザー、0 個以上のトークン フィルター、および 0 個以上の文字フィルター名のユーザー定義の組み合わせです。

+ [CustomAnalyzer.Tokenizer](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer)
+ [CustomAnalyzer.TokenFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenfilters)
+ [CustomAnalyzer.CharFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.charfilters)

次の例では、[uax_url_email トークナイザー](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer)と[小文字トークン フィルター](/dotnet/api/microsoft.azure.search.models.tokenfiltername.lowercase)を使用する "url-analyze" という名前のカスタム アナライザーを作成します。

```csharp
private static void CreateIndex(string indexName, SearchIndexClient adminClient)
{
   FieldBuilder fieldBuilder = new FieldBuilder();
   var searchFields = fieldBuilder.Build(typeof(Hotel));

   var analyzer = new CustomAnalyzer("url-analyze", "uax_url_email")
   {
         TokenFilters = { TokenFilterName.Lowercase }
   };

   var definition = new SearchIndex(indexName, searchFields);

   definition.Analyzers.Add(analyzer);

   adminClient.CreateOrUpdateIndex(definition);
}
```

その他の例については、[CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

クエリの実行の詳細については、「[Azure Cognitive Search でのフルテキスト検索](search-lucene-query-architecture.md)」を参照してください。 記事では、例を使って、表面上は直感的ではないと思われるような動作について説明しています。

アナライザーの詳細については、次の記事を参照してください。

+ [言語アナライザー](index-add-language-analyzers.md)
+ [カスタム アナライザー](index-add-custom-analyzers.md)
+ [検索インデックスの作成](search-what-is-an-index.md)
+ [複数言語インデックスの作成](search-language-support.md)