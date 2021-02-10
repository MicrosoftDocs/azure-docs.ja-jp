---
title: インデクサーの作成
titleSuffix: Azure Cognitive Search
description: データの取得元と格納先を決定するためのプロパティをインデクサーに設定します。 パラメーターを設定して、実行時の動作を変更できます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: ade5880a6b06f448df23eb77d81201a521f1d240
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430047"
---
# <a name="create-a-search-indexer"></a>検索インデクサーを作成する

検索インデクサーを使用すると、外部データ ソースから検索サービスの検索インデックスにドキュメントとコンテンツを転送するための、自動化されたワークフローが提供されます。 もともとの設計では、Azure のデータ ソースからテキストとメタデータが抽出され、ドキュメントが JSON にシリアル化されて、結果のドキュメントがインデックス作成のために検索エンジンに渡されます。 その後、ディープ コンテンツ処理のための [AI エンリッチメント](cognitive-search-concept-intro.md)をサポートするように拡張されています。 

インデクサーを使用すると、記述する必要のあるコードの量と複雑さが大幅に減少します。 この記事では、ソース固有のインデクサーと[スキルセット](cognitive-search-working-with-skillsets.md)を使用するさらに高度な作業の準備として、インデクサーを作成するメカニズムについて説明します。

## <a name="whats-an-indexer-definition"></a>インデクサーの定義とは

インデクサーは、ソース フィールドからインデックス フィールドに英数字コンテンツをプルするテキストベースのインデックス付けのため、または区別されていないテキストの構造の分析や、画像でのテキストや情報の分析を行い、インデックスへのそのコンテンツの追加も行う AI ベースの処理のために、使用されます。 次のインデックス定義は、どちらのシナリオでも作成される可能性がある一般的なものです。

### <a name="indexers-for-text-content"></a>テキスト コンテンツ用のインデクサー

インデクサーの本来の目的は、データ ソースのフィールドのテキストや数値のコンテンツに接続して読み取り、そのコンテンツを JSON ドキュメントとしてシリアル化し、インデックス付けのためにそれらのドキュメントを検索エンジンに渡すメカニズムを提供することによって、インデックスを読み込む複雑なプロセスを簡略化することでした。 これは今でも主要なユース ケースであり、この操作のために、次の例で定義されているプロパティを使用してインデクサーを作成する必要があります。

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

**`name`** 、 **`dataSourceName`** 、 **`targetIndexName`** の各プロパティは必須であり、インデクサーの作成方法によっては、インデクサーを実行する前に、データ ソースとインデックスの両方がサービスに既に存在している必要があります。 

**`parameters`** プロパティにより、ジョブ全体を失敗させる前に許容されるエラーの数など、実行時の動作が変更されます。 パラメーターは、ソース固有の動作を指定する方法でもあります。 たとえば、ソースが Blob Storage である場合、ファイル拡張子でフィルター処理を行うパラメーター設定できます: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }`。

**`field mappings`** プロパティは、取得元と格納先のフィールドで名前または型が異なる場合に、それらのフィールドを明示的にマップするために使用されます。 他のプロパティ (示されていません) は、[スケジュールの指定](search-howto-schedule-indexers.md)、無効状態でのインデクサーの作成、または保存データを補助的に暗号化するための[暗号化キー](search-security-manage-encryption-keys.md)の指定に使用されます。

### <a name="indexers-for-ai-indexing"></a>AI インデックス付け用のインデクサー

インデクサーは、検索サービスが送信要求を行うためのメカニズムであるため、AI エンリッチメントをサポートするように拡張されて、このユース ケースを実装するためのインフラストラクチャとオブジェクトが追加されました。

上記のプロパティとパラメーターはすべて、AI エンリッチメントを実行するインデクサーに適用されます。 次のプロパティは AI エンリッチメントに固有のものです: **`skillSets`** 、 **`outputFieldMappings`** 、 **`cache`** (プレビューと REST のみ)。 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

AI エンリッチメントはこの記事の範囲には含まれません。 詳細については、最初に次の記事を参照してください: [AI エンリッチメント](cognitive-search-concept-intro.md)、[Azure Cognitive Search でのスキルセット](cognitive-search-working-with-skillsets.md)、[スキルセットの作成 (REST)](/rest/api/searchservice/create-skillset) に関する各ページ。

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>インデクサー クライアントを選択してインデクサーを作成する

リモート検索サービスでインデクサーを作成する準備ができたら、Azure portal や Postman のようなツールの形式での検索クライアント、またはインデクサー クライアントをインスタンス化するコードが必要になります。 早期の開発と概念実証のテストには、Azure portal または REST API をお勧めします。

### <a name="permissions"></a>アクセス許可

状態や定義の GET 要求など、インデクサーに関連するすべての操作には、[管理者 API キー](search-security-api-keys.md)が要求に必要です。

### <a name="limits"></a>制限

作成できるオブジェクトの数が、すべての[サービス レベルで制限](search-limits-quotas-capacity.md#indexer-limits)されています。 Free レベルで実験している場合、使用できるオブジェクトは各種類につき 3 つだけ、インデクサー処理の時間は 2 分だけ (スキルセットの処理は含みません) です。

### <a name="use-azure-portal-to-create-an-indexer"></a>Azure portal を使用してインデクサーを作成する

ポータルには、インデクサーを作成するための 2 つのオプションが用意されています: [**データのインポート ウィザード**](search-import-data-portal.md)と、インデクサーの定義を指定するためのフィールドがある **[新しいインデクサー]** 。 ウィザードは、必要な要素がすべて作成される点が他とは異なります。 他の方法を使用する場合は、データ ソースとインデックスを事前に定義しておく必要があります。

次のスクリーンショットは、ポータルでこれらの機能が見つかる場所を示したものです。 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="hotels のインデクサー" border="true":::

### <a name="use-a-rest-client"></a>REST クライアントを使用する

Postman と Visual Studio Code (Azure Cognitive Search 用の拡張機能を備えているもの) はどちらも、インデクサー クライアントとして機能できます。 どちらのツールを使用しても、検索サービスに接続し、インデクサーと他のオブジェクトを作成する要求を送信できます。 REST クライアントを使用してオブジェクトを作成する方法がわかるチュートリアルと例が多数提供されています。 

各クライアントの詳細については、最初に次のいずれかの記事を参照してください。

+ [REST と Postman を使用して検索インデックスを作成する](search-get-started-rest.md)
+ [Visual Studio Code と Azure Cognitive Search の使用を開始する](search-get-started-vs-code.md)

インデクサー要求を整理する方法については、[インデクサー操作 (REST)](/rest/api/searchservice/Indexer-operations) に関するページを参照してください。

### <a name="use-an-sdk"></a>SDK を使用する

Cognitive Search の場合、一般公開される機能は Azure SDK によって実装されています。 そのため、任意の SDK を使用してインデクサー関連のオブジェクトを作成できます。 それらのすべてで、インデクサーと関連オブジェクト (スキルセットなど) を作成するためのメソッドを提供する **SearchIndexerClient** が実装されています。

| Azure SDK | Client | 例 |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [インデクサー](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>インデクサーを実行する

サービスでインデクサーを作成すると、インデクサーは自動的に実行されます。 この時点で、データ ソース接続エラー、フィールド マッピングの問題、またはスキルセットの問題があるかどうかがわかります。 

インデクサーを実行するには、いくつかの方法があります。

+ [インデクサー作成](/rest/api/searchservice/create-indexer)または[インデクサー更新](/rest/api/searchservice/update-indexer)の HTTP 要求を送信して定義を追加または変更し、インデクサーを実行します。

+ [インデクサー実行](/rest/api/searchservice/run-indexer)の HTTP 要求を送信して、定義を変更せずにインデクサーを実行します。

+ 作成、更新、または実行のために SearchIndexerClient メソッドを呼び出すプログラムを実行します。

> [!NOTE]
> 作成されたインデクサーがすぐに実行されないようにするには、インデクサーの定義に **`disabled=true`** を含めます。

または、インデクサーの[スケジュール](search-howto-schedule-indexers.md)を設定し、定期的に処理を呼び出します。 

通常、スケジュール設定された処理を行う場合は、変更されたコンテンツのインクリメンタルなインデックス付けも必要になります。 変更検出ロジックは、ソース プラットフォームに組み込まれている機能です。 BLOB コンテナーでの変更は、インデクサーによって自動的に検出されます。 他のデータ ソースでの変更検出の利用に関するガイダンスについては、特定のデータ ソースのインデクサーのドキュメントを参照してください。

+ [Azure SQL データベース](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="know-your-data"></a>データについて理解する

インデクサーで想定されている表形式の行セットでは、各行がインデックスでの完全または部分的な検索ドキュメントになります。 多くの場合、行と結果の検索ドキュメントの間には一対一の対応があり、行セット内のすべてのフィールドが各ドキュメントに完全に設定されます。 ただし、たとえば複数のインデクサーや方法を使用してインデックスを作成する場合など、インデクサーを使用してドキュメントの一部だけを生成することもできます。 

リレーショナル データを行セットにフラット化するには、SQL ビューを作成するか、親レコードと子レコードが同じ行で返されるクエリを作成する必要があります。 たとえば、組み込まれているホテル サンプルのデータセットは 50 レコード (ホテルごとに 1 つ) の SQL データベースであり、関連テーブルの部屋レコードにリンクされています。 集合データを行セットにフラット化するクエリにより、すべての部屋情報が各ホテル レコードの JSON ドキュメントに埋め込まれます。 埋め込まれる部屋情報は、**FOR JSON AUTO** 句を使用するクエリによって生成されます。 この手法の詳細については、「[埋め込みの JSON を返すクエリを定義する](index-sql-relational-data.md#define-a-query-that-returns-embedded-json)」を参照してください。 これは 1 つの例にすぎません。同じ効果を得られる他の方法を見つけることができます。

フラット化されたデータに加えて、検索可能なデータのみをプルすることが重要です。 検索可能なデータは英数字です。 Cognitive Search を使用すると、どのような形式でもバイナリ データを検索することはできませんが、画像ファイル内のテキスト記述を抽出して推測し ([AI エンリッチメント](cognitive-search-concept-intro.md)に関するページを参照)、検索可能なコンテンツを作成することはできます。 同様に、AI エンリッチメントを使用すると、大規模なテキストを自然言語モデルで分析して、構造や関連情報を発見し、検索ドキュメントに追加できる新しいコンテンツを生成することができます。

インデクサーによってデータの問題が解決されない場合は、他の形式のデータ クレンジングや操作が必要になることがあります。 詳細については、お使いの [Azure データベース製品](/azure/?product=databases)の製品ドキュメントを参照してください。

## <a name="know-your-index"></a>インデックスについて理解する

インデクサーにより、インデックス付けのために検索ドキュメントが検索エンジンに渡されることを思い出してください。 実行動作を決定するプロパティがインデクサーにあるのと同様に、インデックス スキーマには、文字列にインデックスを付ける方法に大きく影響するプロパティがあります (文字列のみが分析およびトークン化されます)。 アナライザーの割り当てによっては、インデックス付けされる文字列が渡されたものと異なる場合があります。 [テキストの分析 (REST)](/rest/api/searchservice/test-analyzer) を使用して、アナライザーの影響を評価することができます。 アナライザーの詳細については、[テキスト処理のためのアナライザー](search-analyzers.md)に関するページを参照してください。

インデクサーとインデックスのやり取りの方法に関しては、インデクサーによりフィールドの名前と型のみがチェックされます。 受信したコンテンツがインデックス内の対応する検索フィールドについて正しいことを確認する検証ステップはありません。 検証ステップとしては、ドキュメント全体または選択したフィールドを返すクエリを、設定されたインデックスに対して実行できます。 インデックスの内容に対するクエリの実行の詳細については、[基本的なクエリの作成](search-query-create.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

+ [インデクサーのスケジュール設定](search-howto-schedule-indexers.md)
+ [フィールド マッピングの定義](search-indexer-field-mappings.md)
+ [インデクサーの状態を監視する](search-howto-monitor-indexers.md)
+ [マネージド ID を使用して接続する](search-howto-managed-identities-data-sources.md)