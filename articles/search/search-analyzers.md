---
title: 言語処理とテキスト処理のためのアナライザー - Azure Search
description: インデックスの検索可能なテキスト フィールドにアナライザーを割り当てて、既定の標準 Lucene をカスタムの定義済みの代替または言語固有の代替と置換します。
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: e3738980206277587ca367339d75da4f3faa643a
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651823"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Azure Search でのテキスト処理のためのアナライザー

*アナライザー*は、クエリ文字列内のテキストとインデックス付きドキュメントを処理する[フル テキスト検索エンジン](search-lucene-query-architecture.md)のコンポーネントです。 シナリオに応じて、さまざまなアナライザーがさまざまな方法でテキストを操作します。 言語アナライザーは検索品質を向上させるために言語規則を使用してテキストを処理しますが、他のアナライザーは、文字を小文字に変換するなどのより基本的なタスクを実行します。 

言語アナライザーが最もよく使用され、Azure Search インデックスのすべての検索可能フィールドには既定のアナライザーが割り当てられています。 テキスト分析中は、次の言語変換が一般的です。

+ 重要ではない単語 (ストップワード) と感嘆符は削除されます。
+ フレーズとハイフンでつながれた単語は、構成要素に分割されます。
+ 大文字の単語は小文字に変換されます。
+ 単語は、時制に関係なく一致が見つかるように、原形に戻されます。

言語アナライザーは、テキスト入力を情報の保存と取得に効率的な原型または原形に変換します。 変換は、インデックスが構築されるときにインデックスの作成中に実行され、インデックスの読み取り時の検索中にも実行されます。 両方の操作に同じアナライザーを使用すると、期待する検索結果が得られる可能性が高くなります。

## <a name="default-analyzer"></a>既定のアナライザー  

Azure Search では、["Unicode テキストのセグメント化"](https://unicode.org/reports/tr29/) の規則に従ってテキストを要素に分割する [Apache Lucene 標準アナライザー (標準 Lucene)](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) を既定として使用します。 さらに、標準アナライザーはすべての文字を小文字形式に変換します。 インデックス付きドキュメントと検索語句の両方について、インデックス作成とクエリ処理の間に分析が行われます。  

すべての検索可能フィールドで自動的に使用されます。 フィールド単位で既定値をオーバーライドすることができます。 代わりのアナライザーとしては、[言語アナライザー](index-add-language-analyzers.md)、[カスタム アナライザー](index-add-custom-analyzers.md)、または[使用可能なアナライザーの一覧](index-add-custom-analyzers.md#AnalyzerTable)の定義済みアナライザーを使用できます。


## <a name="types-of-analyzers"></a>アナライザーの種類

次の一覧では、Azure Search で使用可能なアナライザーについて説明しています。

| Category | 説明 |
|----------|-------------|
| [標準 Lucene のアナライザー](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | [既定]。 指定や構成は必要ありません。 この汎用アナライザーは、ほとんどの言語とシナリオで適切に実行されます。|
| 定義済みアナライザー | そのまま使用するように完成した製品として提供されます。 <br/>特殊と言語という 2 種類があります。 "定義済み" とは、カスタマイズまたは構成なしで、名前で参照するためです。 <br/><br/>[特殊 (言語を選ばない) アナライザー](index-add-custom-analyzers.md#AnalyzerTable)は、特殊な処理または最小限の処理が必要なテキスト入力に使用します。 非言語の定義済みアナライザーには、**Asciifolding**、**Keyword**、**Pattern**、**Simple**、**Stop**、**Whitespace** などがあります。<br/><br/>[言語アナライザー](index-add-language-analyzers.md)は、各言語に合わせて高度の言語サポートが必要な場合に使用されます。 Azure Search は、35 個の Lucene 言語アナライザーと 50 個の Microsoft 自然言語処理アナライザーをサポートしています。 |
|[カスタム アナライザー](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 1 つのトークナイザー (必須) と省略可能なフィルター (文字またはトークン) から構成される既存の要素を組み合わせたユーザー定義の構成のことです。|

**Pattern** や **Stop** など、いくつかの定義済みアナライザーは、限られた構成オプションしかサポートしていません。 これらのオプションを設定するには、実際には、[定義済みアナライザーのリファレンス](index-add-custom-analyzers.md#AnalyzerTable)で説明されている定義済みアナライザーと代替オプションの 1 つで構成されるカスタム アナライザーを作成します。 他のカスタム構成と同様に、新しい構成に *myPatternAnalyzer* などの名前を付けて、Lucene パターン アナライザーの名前を区別できるようにします。

## <a name="how-to-specify-analyzers"></a>アナライザーを指定する方法

1. (カスタム アナライザーの場合のみ) 名前付きの **analyzer** セクションをインデックス定義に作成します。 詳細については、[インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)および[カスタム アナライザーの追加](index-add-custom-analyzers.md)に関する記事を参照してください。

2. インデックスの[フィールド定義](https://docs.microsoft.com/rest/api/searchservice/create-index)で、フィールドの **analyzer** プロパティをターゲット アナライザーの名前に設定します (たとえば、`"analyzer" = "keyword"`)。 有効な値には、定義済みアナライザー名、言語アナライザーまたは、これもインデックス スキーマで定義されるカスタム アナライザーが含まれます。 サービスでインデックスが作成される前に、インデックス定義フェーズでアナライザーの割り当てを計画します。

3. また、1 つの **analyzer** プロパティの代わりに、**indexAnalyzer** および **searchAnalyzer** フィールド パラメーターを使用して、インデックスとクエリに異なるアナライザーを設定することができます。 データの取得と準備のどちらかで、もう一方の処理には必要のない特定の変換が必要な場合、別々のアナライザーをこれらの処理に使用します。

既に物理的に作成されているフィールドに **analyzer** または **indexAnalyzer** を割り当てることは許可されていません。 この情報が明確でない場合は、再構築が必要なアクションとその理由について、次の表の説明を参照してください。
 
 | シナリオ | 影響 | 手順 |
 |----------|--------|-------|
 | 新しいフィールドの追加 | 最小限 | フィールドがまだスキーマに存在しない場合、インデックスにもフィールドはまだ物理的に存在していないため、フィールドのリビジョンは実行されていません。 [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) を使用して既存のインデックスに新しいフィールドを追加し、[mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) を使用して事前設定することができます。|
 | **analyzer** または **indexAnalyzer** を既存のインデックス付きフィールドに追加します。 | [再構築](search-howto-reindex.md) | そのフィールドの転置インデックスは最初から再作成する必要があり、それらのフィールドの内容は再度インデックス作成する必要があります。 <br/> <br/>現在開発中のインデックスの場合は、新しいフィールド定義を取得するために、インデックスを[削除](https://docs.microsoft.com/rest/api/searchservice/delete-index)して[作成](https://docs.microsoft.com/rest/api/searchservice/create-index)します。 <br/> <br/>運用中のインデックスの場合は、改定された定義を提供する新しいフィールドを作成することによって再構築を延期し、古いものの代わりに使用を開始することができます。 新しいフィールドを組み込むには [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) を使用し、それを事前設定するには [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) を使用してください。 後で、計画的なインデックス サービスの一環として、インデックスをクリーンアップし、不要になったフィールドを削除することができます。 |

## <a name="when-to-add-analyzers"></a>アナライザーを追加する時期

アナライザーを追加して割り当てる最適な時期は、アクティブな開発中、インデックスの削除と再作成がルーチンである時期です。

インデックスの定義が確定したら新しい分析コンストラクトをインデックスに追加できますが、次のエラーを回避したい場合は **allowIndexDowntime** フラグを [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) に渡す必要があります。

*"ダウンタイムが発生するため、インデックスの更新は許可されていません。新しいアナライザー、トークナイザー、トークン フィルター、または文字フィルターを既存のインデックスに追加するためには、インデックス更新要求で 'allowIndexDowntime' クエリ パラメーターを 'true' に設定してください。この操作によりインデックスが少なくとも数秒間オフラインになるため、インデックス作成とクエリ要求が失敗することに注意してください。インデックスを更新すると、インデックスのパフォーマンスと書き込み可用性が数分にわたり損なわれる場合があります。インデックスが非常に大きい場合、その時間も長くなります。"*

アナライザーをフィールドに割り当てるときも、同じことが当てはまります。 アナライザーはフィールドの定義の不可欠な部分であるため、フィールドの作成時にしか追加できません。 既存のフィールドにアナライザーを追加する場合は、インデックスを[削除して再構築する](search-howto-reindex.md)か、または必要なアナライザーを持つ新しいフィールドを追加する必要があります。

説明したように、**searchAnalyzer** の場合は例外です。 アナライザーを指定する 3 つの方法 (**analyzer**、**indexAnalyzer**、**searchAnalyzer**) のうち、既存のフィールドで変更できるのは **searchAnalyzer** 属性だけです。

## <a name="recommendations-for-working-with-analyzers"></a>アナライザーの使用方法に関する推奨事項

このセクションでは、アナライザーの使用方法についてのアドバイスを提供します。

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>特定の要件がない場合は読み取り/書き込みに 1 つのアナライザー

Azure Search では、追加の **indexAnalyzer** および **searchAnalyzer** フィールド パラメーターを使用して、インデックス作成と検索に別々のアナライザーを指定することができます。 指定しなかった場合、**analyzer** プロパティで設定されたアナライザーが、インデックス作成と検索の両方に使用されます。 `analyzer` が指定されなかった場合は、標準の Lucene アナライザーが使用されます。

一般的な規則は、特定の要件で別に指示がない場合は、インデックスとクエリの両方に同じアナライザーを使用することです。 テストは徹底的に行ってください。 検索時とインデックス作成時にテキスト処理が異なると、検索アナライザーの構成とインデックス作成アナライザーの構成が揃っていない場合、クエリ用語とインデックス用語に不一致が生じるおそれがあります。

### <a name="test-during-active-development"></a>アクティブな開発中のテスト

標準アナライザーをオーバーライドするには、インデックスの再構築が必要です。 可能であれば、アクティブな開発中に使用するアナライザーを決めてから、インデックスを運用環境に展開します。

### <a name="inspect-tokenized-terms"></a>トークン化された用語の検査

検索結果が期待した内容ではない場合、よくあるシナリオとして、クエリの用語入力とインデックス内のトークン化された用語間にトークンの違いがあることが考えられます。 トークンが同じではない場合、一致処理は具体化に失敗します。 トークナイザーの出力を検査する場合、調査ツールとして [Analyze API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) を使用することをお勧めします。 応答は、特定のアナライザーによって生成されるトークンで構成されます。

### <a name="compare-english-analyzers"></a>英語のアナライザーの比較

[Search Analyzer Demo](https://alice.unearth.ai/) はサードパーティ製のデモ アプリです。標準 Lucene アナライザー、Lucene の英語アナライザー、Microsoft の英語自然言語プロセッサの比較が横並びに表示されます。 インデックスは固定です。よくあるストーリーのテキストが含まれています。 検索用語を入力するごとに、隣接するウィンドウに各アナライザーの結果が表示され、同じ文字列を各アナライザーがどのように処理するかを把握できます。 

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

* アナライザーは、検索可能なフィールドのフィールド クラスのプロパティです。
* カスタム アナライザーは、インデックス定義の一部です。 軽くカスタマイズされているか (1 つのフィルターの 1 つのオプションがカスタマイズされているなど)、複数個所でカスタマイズされている可能性があります。
* この例で、カスタム アナライザーは "my_analyzer" です。このアナライザーはカスタマイズされた標準トークナイザー "my_standard_tokenizer" と、小文字とカスタマイズされた asciifolding フィルターである "my_asciifolding" という 2 つのトークン フィルターを使用しています。
* 2 つのカスタム文字フィルター "map_dash" および "remove_whitespace" も定義します。 1 つ目はすべてのダッシュをアンダースコアに置き換え、2 つ目はすべてのスペースを削除します。 マッピング規則でスペースを UTF-8 エンコードする必要があります。 文字フィルターはトークン化よりも先に適用され、結果のトークンに影響を及ぼします (標準トークナイザーはダッシュとスペースで区切りますが、アンダースコアでは区切りません)。

~~~~
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
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>フィールドごとのアナライザー割り当ての例

標準アナライザーが既定です。 たとえば、既定のアナライザーを、パターン アナライザーなど、別の定義済みアナライザーで置き換えるとします。 カスタム オプションを設定していない場合、フィールド定義で名前を使用して指定する必要があります。

"analyzer" 要素は、フィールドごとに標準アナライザーをオーバーライドします。 グローバルなオーバーライドはありません。 この例で、`text1` はパターン アナライザーと `text2` を使用します。これはアナライザーを指定せず、既定値を使用します。

~~~~
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
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>インデックス作成と検索の各操作用のアナライザーを混在させる

API には、インデックス作成と検索に別のアナライザーを指定するための追加のインデックス属性が含まれています。 **searchAnalyzer** 属性と **indexAnalyzer** 属性をペアで指定し、1 つの **analyzer** 属性を置き換える必要があります。


~~~~
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
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>言語アナライザーの例

複数言語の文字列を含むフィールドでは、言語アナライザーを使用できますが、他のフィールドは既定値のままです (または、他の定義済みまたはカスタム アナライザーを使用します)。 言語アナライザーを使用する場合、インデックス作成と検索操作の両方に使用する必要があります。 言語アナライザーを使用するフィールドでは、インデックス作成と検索に別のアナライザーを使用することはできません。

~~~~
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
~~~~

## <a name="c-examples"></a>C# の例

.NET SDK のサンプル コードを使用している場合は、アナライザーを使用または構成するためにこれらのサンプルを追加できます。

+ [組み込みのアナライザーを割り当てる](#Assign-a-language-analyzer)
+ [アナライザーを構成する](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>言語アナライザーを割り当てる

構成なしでそのまま使用されるすべてのアナライザーは、フィールド定義で指定します。 アナライザーのコンストラクトを作成する必要はありません。 

この例では、Microsoft の英語およびフランス語のアナライザーを説明フィールドに割り当てます。 これは、[DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) サンプルの hotels.cs ファイルの Hotel クラスを使用して作成した、hotels インデックスのより大きな定義から抜粋されたスニペットです。

Azure Search でサポートされているテキスト アナライザーを提供する [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) 型を指定して、[Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) を呼び出します。

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>カスタム アナライザーを定義する

カスタマイズまたは構成が必要な場合は、アナライザーのコンストラクトをインデックスに追加する必要があります。 定義したら、前の例で示したようにそれをフィールド定義に追加できます。

[CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) オブジェクトを作成します。 その他の例については、[CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/src/SDKs/Search/DataPlane/Search.Tests/Tests/CustomAnalyzerTests.cs) に関するページを参照してください。

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>次の手順

+ 包括的な説明については、「[Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)」を参照してください。 この記事では、例を使って、表面上は直感的ではないと思われるような動作について説明しています。

+ [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) の例に関するセクションや[単純なクエリ構文](query-simple-syntax.md)で紹介されているさまざまなクエリ構文をポータルの Search エクスプローラーで試します。

+ [言語に固有の字句解析器](index-add-language-analyzers.md)を適用する方法について書かれた記事を参照します。

+ 個々のフィールドに対して最小限の処理または特殊な処理を適用するための[カスタム アナライザーを構成](index-add-custom-analyzers.md)します。

+ このデモ Web サイトで隣接する列の[標準と英語のアナライザーを比較](https://alice.unearth.ai/)します。 

## <a name="see-also"></a>関連項目

 [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [単純なクエリ構文](query-simple-syntax.md) 

 [Full Lucene クエリ構文](query-lucene-syntax.md) 
 
 [検索結果の処理方法](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
