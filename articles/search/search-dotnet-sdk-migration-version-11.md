---
title: .NET SDK バージョン 11 にアップグレードする
titleSuffix: Azure Cognitive Search
description: 以前のバージョンから Azure Cognitive Search .NET SDK バージョン 11 にコードを移行します。 新機能と必要なコード変更について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: df8841cb2dcac6335b09a5e7715f42c508c69e76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99536817"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Azure Cognitive Search .NET SDK バージョン 11 へのアップグレード

バージョン 10.0 以前の [.NET SDK](/dotnet/api/overview/azure/search) を使用している場合、この記事を参考にして、バージョン 11 と **Azure.Search.Documents** クライアント ライブラリにアップグレードできます。

バージョン 11 は、Azure SDK 開発チームによって完全に再設計され、リリースされたクライアント ライブラリです (以前のバージョンは、Azure Cognitive Search 開発チームによって作成されました)。 このライブラリは、他の Azure クライアント ライブラリとの整合性を高めるために再設計されました。[Azure.Core](/dotnet/api/azure.core) および [System.Text.Json](/dotnet/api/system.text.json) に依存しており、一般的なタスクにはなじみのあるアプローチを実装しています。

新しいバージョンには、次のような重要な違いがあります。

+ 複数ではなく、1 つのパッケージとライブラリ
+ 新しいパッケージ名: `Microsoft.Azure.Search` ではなく `Azure.Search.Documents` です。
+ 2 つではなく 3 つのクライアント: `SearchClient`、`SearchIndexClient`、`SearchIndexerClient`
+ さまざまな API 全体の名前付けの違いと、一部のタスクを簡略化する細かい構造の違い

この記事に加えて、.NET SDK バージョン 11 の変更内容の項目別一覧について[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)を確認できます。

## <a name="package-and-library-consolidation"></a>パッケージとライブラリの統合

バージョン 11 では、複数のパッケージとライブラリが 1 つに統合されています。 移行後は、管理するライブラリが少なくなります。

+ [Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [クライアント ライブラリの API リファレンス](/dotnet/api/overview/azure/search.documents-readme)

## <a name="client-differences"></a>クライアントの違い

2 つのバージョン間で対応するクライアント ライブラリがある場合、その対応付けを次の表に示します。

| 操作のスコープ | Microsoft.Azure.Search&nbsp;(v10) | Azure.Search.Documents&nbsp;(v11) |
|---------------------|------------------------------|------------------------------|
| クエリおよびインデックスの作成に使用されるクライアント。 | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| インデックス、アナライザー、シノニム マップに使用されるクライアント | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| インデクサー、データ ソース、スキルセットに使用されるクライアント | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**新規**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` は両方のバージョンに存在しますが、サポートする内容は異なります。 バージョン 10 では、`SearchIndexClient` によってインデックスやその他のオブジェクトが作成されます。 バージョン 11 では、`SearchIndexClient` に既存のインデックスを利用できます。 コードを更新するときの混乱を避けるために、クライアント参照が更新される順序にご注意ください。 「[アップグレードの手順](#UpgradeSteps)」のシーケンスに従うと、文字列の置換の問題を軽減できます。

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>名前付けとその他の API の違い

クライアントの違い (既に説明したため、ここでは省略します) に加え、他の複数の API は名前が変更され、場合によっては設計が変更されています。 クラス名の違いを以下にまとめます。 この一覧は完全なものではありませんが、API の変更をタスクごとにグループ化しているため、特定のコード ブロックを改訂する場合に役立ちます。 API 更新の項目別一覧については、GitHub の `Azure.Search.Documents` の[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)を参照してください。

### <a name="authentication-and-encryption"></a>認証と暗号化

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` ([プレビュー SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) に一般提供機能として存在します) | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>インデックス、アナライザー、シノニム マップ

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [フィールド](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (さらに `AnalyzerName` から `LexicalAnalyzerName` へ) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (さらに `StandardTokenizerV2` から `LuceneStandardTokenizerV2` へ) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (さらに `TokenizerName` から `LexicalTokenizerName` へ) |
| [SynonymMap.Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | [なし] : `Format` への参照を削除します。 |

フィールド定義は整理されています。[SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield)、[SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield)、[ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) は、フィールド定義を作成するための新しい API です。

### <a name="indexers-datasources-skillsets"></a>インデクサー、データソース、スキルセット

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [インデクサー](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Skill](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [スキルセット](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>データのインポート

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>クエリの定義と結果

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | 結果のドキュメントが 1 つか複数かに応じて、[SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) または [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1)。 |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |

### <a name="json-serialization"></a>JSON シリアル化

既定では、Azure SDK は、JSON のシリアル化に [System.Text.Json](/dotnet/api/system.text.json) を使用します。これらの API の機能を使用して、新しいライブラリに対応するものがないネイティブ [SerializePropertyNamesAsCamelCaseAttribute](/dotnet/api/microsoft.azure.search.models.serializepropertynamesascamelcaseattribute) クラスを介して以前に実装されたテキスト変換を処理します。

プロパティ名を camelCase にシリアル化するには、[JsonPropertyNameAttribute](/dotnet/api/system.text.json.serialization.jsonpropertynameattribute) を使用します ([こちらの例](https://github.com/Azure/azure-sdk-for-net/tree/d263f23aa3a28ff4fc4366b8dee144d4c0c3ab10/sdk/search/Azure.Search.Documents#use-c-types-for-search-results)に類似)。

または、[JsonSerializerOptions](/dotnet/api/system.text.json.jsonserializeroptions) で提供されている [JsonNamingPolicy](/dotnet/api/system.text.json.jsonnamingpolicy) を設定することもできます。 次の System.Text.Json のコード例 ([Microsoft.Azure.Core.Spatial の README](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial/README.md#deserializing-documents) から取得) は、個々のプロパティに割り当てることなく camelCase を使用する方法を示しています。

```csharp
// Get the Azure Cognitive Search endpoint and read-only API key.
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
AzureKeyCredential credential = new AzureKeyCredential(Environment.GetEnvironmentVariable("SEARCH_API_KEY"));

// Create serializer options with our converter to deserialize geographic points.
JsonSerializerOptions serializerOptions = new JsonSerializerOptions
{
    Converters =
    {
        new MicrosoftSpatialGeoJsonConverter()
    },
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
};

SearchClientOptions clientOptions = new SearchClientOptions
{
    Serializer = new JsonObjectSerializer(serializerOptions)
};

SearchClient client = new SearchClient(endpoint, "mountains", credential, clientOptions);
Response<SearchResults<Mountain>> results = client.Search<Mountain>("Rainier");
```

JSON シリアル化に Newtonsoft.Json を使用している場合は、同様の属性を使用するか、[JsonSerializerSettings](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonSerializerSettings.htm) のプロパティを使用して、グローバル名前付けポリシーを渡すことができます。 上記と同等の例については、Newtonsoft.Json の README の [ドキュメントの逆シリアル化の例](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md)を参照してください。


<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>バージョン 11 の機能

Azure Cognitive Search クライアント ライブラリの各バージョンは、REST API の対応するバージョンを対象としています。 この REST API はサービスの基盤と見なされ、個々の SDK によって REST API のバージョンがラップされます。 .NET 開発者であり、特定のオブジェクトや操作の背景を詳しく知りたい場合は、[REST API のドキュメント](/rest/api/searchservice/)を参照してください。

バージョン 11 は、[2020-06-30 の Search サービス](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)を対象としています。 バージョン 11 は一から構築された新しいクライアント ライブラリでもあるため、ほとんどの開発作業はバージョン 10 との同等性に重点を置いており、REST API 機能のサポートはまだ保留中です。

バージョン 11.0 では、次のオブジェクトと操作が完全にサポートされています。

+ インデックスの作成と管理
+ シノニム マップの作成と管理
+ すべてのクエリの種類と構文 (地理空間フィルターを除く)
+ データ ソースやスキルセットを含む、Azure データ ソースのインデックス作成のためのインデクサー オブジェクトと操作

バージョン 11.1 では、次のものが追加されています。

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (11.1 で追加)
+ カスタムのシリアル化をサポートするための[シリアライザー プロパティ](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (11.1 で追加)

### <a name="pending-features"></a>保留中の機能

バージョン 11 では、まだ次のバージョン 10 機能を使用できません。 これらの機能を必要とする場合は、サポートされるまで移行をお待ちください。

+ [ナレッジ ストア](knowledge-store-concept-intro.md)
+ 地理空間型 - 地理空間型のファースト クラスのサポートは、まだ準備中です。 現時点では、[Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial/) パッケージを使用して、地理的な操作をサポートすることができます。 [System.Text.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) および [Newtonsoft.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md) についての例があります。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>アップグレードの手順

次の手順では、コードの移行の基本について説明します。まず必要な最初の一連のタスク (特にクライアント参照) について手順を説明します。

1. Visual Studio でプロジェクト参照を右クリックし、[NuGet パッケージの管理] を選択して、[Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)をインストールします。

1. Microsoft.Azure.Search の using ディレクティブを次のように置き換えます。

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. JSON のシリアル化を必要とするクラスの場合は、`using Newtonsoft.Json` を `using System.Text.Json.Serialization` で置き換えます。

1. クライアント認証コードを変更します。 以前のバージョンでは、クライアント オブジェクトのプロパティを使用して、API キー ([SearchServiceClient.Credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) プロパティなど) を設定します。 現在のバージョンでは、[AzureKeyCredential](/dotnet/api/azure.azurekeycredential) クラスを使用して、キーを資格情報として渡します。これにより、必要な場合は、新しいクライアント オブジェクトを作成せずに API キーを更新することができます。

   クライアントのプロパティは、`Endpoint`、`ServiceName`、および `IndexName` のみに簡素化されています (該当する場合)。 次の例では、システムの [Uri](/dotnet/api/system.uri) クラスを使用して、エンドポイント、およびキー値を読み取るための[環境](/dotnet/api/system.environment)クラスを提供しています。

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. インデクサー関連オブジェクトの新しいクライアント参照を追加します。 インデクサー、データソース、またはスキルセットを使用している場合は、クライアント参照を [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) に変更します。 このクライアントはバージョン 11 で新しく追加されたものであり、元の機能はありません。

1. コレクションとリストを変更します。 新しい SDK では、リストに null 値が含まれている場合にダウンストリームの問題が発生するのを回避するため、すべてのリストが読み取り専用になっています。 コードを変更して、リストに項目を追加するようにします。 たとえば、Select プロパティに文字列を割り当てる代わりに、次のように文字列を追加します。

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   Select、Facets、SearchFields、SourceFields、ScoringParameters、OrderBy はすべて、現在再構築が必要なリストです。

1. クエリとデータ インポートのクライアント参照を更新します。 [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) のインスタンスは [SearchClient](/dotnet/api/azure.search.documents.searchclient) に変更する必要があります。 名前の混乱を避けるために、次の手順に進む前に、すべてのインスタンスをキャッチしてください。

1. インデックス、シノニム マップ、およびアナライザー オブジェクトのクライアント参照を更新します。 [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) のインスタンスは [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) に変更する必要があります。 

1. コードの残りの部分では、新しいライブラリの API を使用するようにクラス、メソッド、およびプロパティを更新します。 [名前付けの違い](#naming-differences)のセクションから始めることができますが、[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)を確認することもできます。

   同等の API が見つからない場合は、Microsoft でドキュメントを改善し、問題を調査することができるように、[https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) で問題を記録することをお勧めします。

1. ソリューションをリビルドします。 すべてのビルド エラーまたは警告を修正した後、[新しい機能](#WhatsNew)を利用するようにアプリケーションを変更できます。

<a name="ListOfChanges"></a>

## <a name="breaking-changes"></a>重大な変更

ライブラリと API に加えられた全面的な変更を考えると、バージョン 11 へのアップグレードは重大であり、コードがバージョン 10 以前との下位互換性を失うという点で、破壊的変更に相当します。 違いの詳細については、`Azure.Search.Documents` の[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) を参照してください。

サービス使用条件の更新時に、バージョン 11 のコード変更が、API のリファクタリングに限らず、既存の機能に関連している場合は、次のような動作変更があります。

+ [BM25 ランク付けアルゴリズム](index-ranking-similarity.md)では、前のランク付けアルゴリズムがより新しいテクノロジに置き換えられます。 新しいサービスはこのアルゴリズムを自動的に使用します。 既存のサービスについては、新しいアルゴリズムを使用するようにパラメーターを設定する必要があります。

+ このバージョンでは、[順序付けされた null 値の結果](search-query-odata-orderby.md)が変更されていて、null 値は、並べ替えが `asc` の場合は最初に、並べ替えが `desc` の場合は最後に表示されます。 null 値の並べ替え方法を処理するコードを記述した場合、そのコードを確認し、不要になった場合は削除する必要があります。

## <a name="next-steps"></a>次のステップ

+ [Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [GitHub のサンプル](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API リファレンス](/dotnet/api/overview/azure/search.documents-readme)