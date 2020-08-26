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
ms.date: 08/05/2020
ms.openlocfilehash: 390376216700b760e96c2348b1ad61bb4561aad2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211512"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Azure Cognitive Search .NET SDK バージョン 11 へのアップグレード

バージョン 10.0 以前の [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search) を使用している場合、アバージョン 11 にアップグレードするには、この記事が役に立ちます。

バージョン 11 は、Azure SDK 開発チームによって完全に再設計され、リリースされたクライアント ライブラリです (以前のバージョンは、Azure Cognitive Search 開発チームによって作成されました)。 このライブラリは、他の Azure クライアント ライブラリとの整合性を高めるために再設計されました。[Azure.Core](https://docs.microsoft.com/dotnet/api/azure.core) および [System.Text.Json](https://docs.microsoft.com/dotnet/api/system.text.json) に依存しており、一般的なタスクにはなじみのあるアプローチを実装しています。

新しいバージョンには、次のような重要な違いがあります。

+ 複数ではなく、1 つのパッケージとライブラリ
+ 新しいパッケージ名: `Microsoft.Azure.Search` ではなく `Azure.Search.Documents` です。
+ 2 つではなく 3 つのクライアント: `SearchClient`、`SearchIndexClient`、`SearchIndexerClient`
+ さまざまな API 全体の名前付けの違いと、一部のタスクを簡略化する細かい構造の違い

## <a name="package-and-library-consolidation"></a>パッケージとライブラリの統合

バージョン 11 では、複数のパッケージとライブラリが 1 つに統合されています。 移行後は、管理するライブラリが少なくなります。

+ [Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [クライアント ライブラリの API リファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>クライアントの違い

2 つのバージョン間で対応するクライアント ライブラリがある場合、その対応付けを次の表に示します。

| 操作のスコープ | Microsoft.Azure.Search&nbsp;(v10) | Azure.Search.Documents&nbsp;(v11) |
|---------------------|------------------------------|------------------------------|
| クエリおよびインデックスの作成に使用されるクライアント。 | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| インデックス、アナライザー、シノニム マップに使用されるクライアント | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| インデクサー、データ ソース、スキルセットに使用されるクライアント | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**新規**)](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` は両方のバージョンに存在しますが、サポートする内容は異なります。 バージョン 10 では、`SearchIndexClient` によってインデックスやその他のオブジェクトが作成されます。 バージョン 11 では、`SearchIndexClient` に既存のインデックスを利用できます。 コードを更新するときの混乱を避けるために、クライアント参照が更新される順序にご注意ください。 「[アップグレードの手順](#UpgradeSteps)」のシーケンスに従うと、文字列の置換の問題を軽減できます。

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>名前付けとその他の API の違い

クライアントの違い (既に説明したため、ここでは省略します) に加え、他の複数の API は名前が変更され、場合によっては設計が変更されています。 クラス名の違いを以下にまとめます。 この一覧は完全なものではありませんが、API の変更をタスクごとにグループ化しているため、特定のコード ブロックを改訂する場合に役立ちます。 API 更新の項目別一覧については、GitHub の `Azure.Search.Documents` の[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)を参照してください。

### <a name="authentication-and-encryption"></a>認証と暗号化

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` ([プレビュー SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) に一般提供機能として存在します) | [SearchResourceEncryptionKey](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>インデックス、アナライザー、シノニム マップ

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [フィールド](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [SearchField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (さらに `AnalyzerName` から `LexicalAnalyzerName` へ) |
| [AnalyzeRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (さらに `StandardTokenizerV2` から `LuceneStandardTokenizerV2` へ) |
| [TokenInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (さらに `TokenizerName` から `LexicalTokenizerName` へ) |
| [SynonymMap.Format](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | [なし] : `Format` への参照を削除します。 |

フィールド定義は整理されています。[SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield)、[SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield)、[ComplexField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield) は、フィールド定義を作成するための新しい API です。

### <a name="indexers-datasources-skillsets"></a>インデクサー、データソース、スキルセット

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [インデクサー](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Skill](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [スキルセット](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>データのインポート

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>クエリの定義と結果

| バージョン 10 | バージョン 11 の相当するもの |
|------------|-----------------------|
| [DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | 結果のドキュメントが 1 つか複数かに応じて、[SearchResult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1) または [SearchResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1)。 |
| [DocumentSuggestResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>バージョン 11 の機能

Azure Cognitive Search クライアント ライブラリの各バージョンは、REST API の対応するバージョンを対象としています。 この REST API はサービスの基盤と見なされ、個々の SDK によって REST API のバージョンがラップされます。 .NET 開発者であり、特定のオブジェクトや操作の背景を詳しく知りたい場合は、[REST API のドキュメント](https://docs.microsoft.com/rest/api/searchservice/)を参照してください。

バージョン 11 は、[2020-06-30 の Search サービス](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)を対象としています。 バージョン 11 は一から構築された新しいクライアント ライブラリでもあるため、ほとんどの開発作業はバージョン 10 との同等性に重点を置いており、REST API 機能のサポートはまだ保留中です。

バージョン 11 では、次のオブジェクトと操作が完全にサポートされています。

+ インデックスの作成と管理
+ シノニム マップの作成と管理
+ すべてのクエリの種類と構文 (地理空間フィルターを除く)
+ データ ソースやスキルセットを含む、Azure データ ソースのインデックス作成のためのインデクサー オブジェクトと操作

### <a name="pending-features"></a>保留中の機能

バージョン 11 では、まだ次のバージョン 10 機能を使用できません。 これらの機能を使用する場合は、サポートされるまで移行をお待ちください。

+ 地理空間型
+ [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) (ただし、[この回避策](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/tests/Samples/FieldBuilder/FieldBuilder.cs)を使用できます)。
+ [ナレッジ ストア](knowledge-store-concept-intro.md)

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

1. [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) を [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) に置き換えます。

1. インデクサー関連オブジェクトのクライアント参照を更新します。 インデクサー、データソース、またはスキルセットを使用している場合は、クライアント参照を [SearchIndexerClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) に変更します。 このクライアントはバージョン 11 で新しく追加されたものであり、元の機能はありません。

1. クエリとデータ インポートのクライアント参照を更新します。 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) のインスタンスは [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) に変更する必要があります。 名前の混乱を避けるために、次の手順に進む前に、すべてのインスタンスをキャッチしてください。

1. インデックス、インデクサー、シノニム マップ、およびアナライザー オブジェクトのクライアント参照を更新します。 [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) のインスタンスは [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) に変更する必要があります。 

1. できる限り、新しいライブラリの API を使用するようにクラス、メソッド、およびプロパティを更新します。 [名前付けの違い](#naming-differences)のセクションから始めることができますが、[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)を確認することもできます。

   同等の API が見つからない場合は、Microsoft でドキュメントを改善し、問題を調査することができるように、[https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) で問題を記録することをお勧めします。

1. ソリューションをリビルドします。 すべてのビルド エラーまたは警告を修正した後、[新しい機能](#WhatsNew)を利用するようにアプリケーションを変更できます。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>バージョン 11 における破壊的変更

ライブラリと API に加えられた全面的な変更を考えると、バージョン 11 へのアップグレードは重大であり、コードがバージョン 10 以前との下位互換性を失うという点で、破壊的変更に相当します。 違いの詳細については、`Azure.Search.Documents` の[変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) を参照してください。

サービス バージョンの観点から見ると、10 から 11 に移動すると、次のように動作が変更されます。 

+ [BM25 ランク付けアルゴリズム](index-ranking-similarity.md)では、前のランク付けアルゴリズムがより新しいテクノロジに置き換えられます。 新しいサービスはこのアルゴリズムを自動的に使用します。 既存のサービスについては、新しいアルゴリズムを使用するようにパラメーターを設定する必要があります。

+ このバージョンでは、[順序付けされた null 値の結果](search-query-odata-orderby.md)が変更されていて、null 値は、並べ替えが `asc` の場合は最初に、並べ替えが `desc` の場合は最後に表示されます。 null 値の並べ替え方法を処理するコードを記述した場合、そのコードを確認し、不要になった場合は削除する必要があります。

## <a name="next-steps"></a>次のステップ

+ [Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [GitHub のサンプル](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API リファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)