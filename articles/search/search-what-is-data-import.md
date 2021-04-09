---
title: 検索インデックスでのインポートとデータ インジェスト
titleSuffix: Azure Cognitive Search
description: 外部データ ソースから Azure Cognitive Search のインデックスにデータの取り込みやアップロードを行います。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: aa44a27fa5bf6b7b4ea649e1a9b9a69ef8cd78d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049323"
---
# <a name="data-import-overview---azure-cognitive-search"></a>データ インポートの概要 - Azure Cognitive Search

Azure Cognitive Search では、[検索インデックス](search-what-is-an-index.md)に読み込まれて保存されたコンテンツに対してクエリが実行されます。 この記事では、インデックスを取り込む 2 つの基本的な手法について詳しく見ていきます。1 つは、プログラムを使用してデータをインデックスに "*プッシュ*" する方法、もう 1 つは、サポート対象データ ソースで [Azure Cognitive Search のインデクサー](search-indexer-overview.md)をポイントしてデータを "*プル*" する方法です。

どちらの方法も、目的は外部データ ソースから Azure Cognitive Search インデックスにデータを読み込むことです。 Azure Cognitive Search では空のインデックスを作成できますが、そのインデックスには、データをプッシュまたはプルするまでクエリを実行できません。

> [!NOTE]
> [AI エンリッチメント](cognitive-search-concept-intro.md)がソリューションの要件である場合は、プル モデル (インデクサー) を使用してインデックスを読み込む必要があります。 外部処理がサポートされるのは、インデクサーにアタッチされているスキルセットを使用する場合だけです。

## <a name="pushing-data-to-an-index"></a>インデックスにデータをプッシュする

プログラムで Azure Cognitive Search にデータを送信するプッシュ モデルは、最も柔軟なアプローチです。 まず、データ ソースの種類に関して制限がありません。 JSON ドキュメントから成るすべてのデータセットは、そこに含まれる各ドキュメントのフィールドが、インデックスのスキーマに定義されているフィールドにマッピングされていれば、Azure Cognitive Search インデックスにプッシュすることができます。 実行の頻度にも制限はありません。 インデックスには、必要に応じて何度でも変更をプッシュすることができます。 待機時間の要件が非常に厳しいアプリケーションの場合 (たとえば、検索操作を動的な在庫データベースと同期する必要がある場合) は、プッシュ モデルしか利用できません。

このアプローチでは、ドキュメントを個別に、またはバッチでアップロードできるため、プル モデルよりも柔軟です ("バッチごとに最大 1,000 個" と "16 MB" のうち、どちらか先に達した制限が適用されます)。 プッシュ モデルでは、データのある場所にかかわらず、ドキュメントを Azure Cognitive Search にアップロードすることもできます。

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Azure Cognitive Search インデックスにデータをプッシュする方法

次の API を使用して、1 つまたは複数のドキュメントをインデックスに読み込むことができます。

+ [ドキュメントの追加、更新、削除 (REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [IndexDocumentsAction class](/dotnet/api/azure.search.documents.models.indexdocumentsaction) または [IndexDocumentsBatch class](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 

現在では、ポータルを使用してデータをプッシュするためのツール サポートはありません。

各方法の概要については、「[Quickstart: PowerShell を使用した Azure Cognitive Search インデックスの作成](./search-get-started-powershell.md)」または[C# の クイック スタートの.NET SDK を使用した Azure Cognitive Search インデックスの作成](search-get-started-dotnet.md)に関する記事をご覧ください。

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>インデックス作成アクション: upload、merge、mergeOrUpload、delete

インデックス作成アクションの種類をドキュメントごとに制御できます。つまり、ドキュメントを全部アップロードするか、既存のドキュメント コンテンツとマージするか、または削除するかを指定できます。

REST API では、JSON 要求本文を利用して HTTP POST 要求を Azure Cognitive Search インデックスのエンドポイント URL に発行します。 "value" 配列内の各 JSON オブジェクトは、ドキュメントのキーを含み、インデックス作成アクションとしてドキュメント コンテンツの追加、更新、または削除のいずれかを指定します。 コード例については、[ドキュメントの読み込み](search-get-started-dotnet.md#load-documents)に関するセクションを参照してください。

.NET SDK では、データを `IndexBatch` オブジェクトにパッケージ化します。 `IndexBatch` は複数の `IndexAction` オブジェクトをカプセル化したものです。このオブジェクトにはそれぞれ、ドキュメント 1 つと、そのドキュメントへのアクションを Azure Cognitive Search に指示するプロパティが 1 つ含まれています。 コード例は、[ C# Quickstart](search-get-started-dotnet.md) を参照してください。


| @search.action | 説明 | 各ドキュメントに必要なフィールド | Notes |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |`upload` アクションは、ドキュメントが新しい場合は挿入され、存在する場合は更新/置換される "upsert" に似ています。 |キーのほか、定義するその他すべてのフィールド |既存のドキュメントを更新または置換する際に、要求で指定されていないフィールドは `null`に設定されます。 この処理は、フィールドが null 以外の値に設定されていた場合にも行われます。 |
| `merge` |指定されたフィールドで既存のドキュメントを更新します。 ドキュメントがインデックスに存在しない場合、マージは失敗します。 |キーのほか、定義するその他すべてのフィールド |マージで指定したすべてのフィールドは、ドキュメント内の既存のフィールドを置き換えます。 .NET SDK では、`DataType.Collection(DataType.String)` 型のフィールドがこれに含まれます。 REST API では、`Collection(Edm.String)` 型のフィールドがこれに含まれます。 たとえば、ドキュメントにフィールド `tags` があり、その値が `["budget"]` である場合、`tags` に値 `["economy", "pool"]` を指定してマージを実行すると、`tags` フィールドの最終的な値は `["economy", "pool"]` になります。 `["budget", "economy", "pool"]`にはなりません。 |
| `mergeOrUpload` |このアクションは、指定したキーを持つドキュメントがインデックスに既に存在する場合は、`merge` と同様の処理になります。 ドキュメントが存在しない場合は、 `upload` と同様の処理になり、新しいドキュメントが挿入されます。 |キーのほか、定義するその他すべてのフィールド |- |
| `delete` |インデックスから指定したドキュメントを削除します。 |キーのみ |指定したフィールドは、キー フィールド以外すべて無視されます。 ドキュメントから個々のフィールドを削除する場合は、代わりに `merge` を使用して、フィールドを明示的に null に設定します。 |

### <a name="formulate-your-query"></a>クエリの作成

[REST API を使用してインデックスを検索する](/rest/api/searchservice/Search-Documents)方法は 2 とおりあります。 その 1 つは、要求本文の JSON オブジェクトにクエリ パラメーターが定義された HTTP POST 要求を発行する方法です。 もう 1 つは、要求 URL にクエリ パラメーターが定義された HTTP GET 要求を発行する方法です。 POST の方が GET よりもクエリ パラメーターのサイズの[制限が緩やか](/rest/api/searchservice/Search-Documents)です。 そのため、GET の方が便利である特殊な状況を除いて、POST を使用することをお勧めします。

POST と GET のどちらについても、要求 URL で *サービス名*、*インデックス名*、および *API バージョン* を指定する必要があります。 

GET の場合は、URL の末尾の *クエリ文字列* でクエリ パラメーターを指定します。 この URL の形式については、以下を参照してください。

```http
    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06
```

POST の形式も同じですが、クエリ文字列のパラメーターで `api-version` を指定します。

## <a name="pulling-data-into-an-index"></a>インデックスへのデータのプル

プル モデルは、サポートされているデータ ソースをクロールし、データをインデックスに自動的にアップロードします。 Azure Cognitive Search では、この機能が "*インデクサー*" を使用して実装され、現時点ではこれらのプラットフォームで利用可能です。

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database、SQL Managed Instance、および Azure VM 上の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SharePoint Online (プレビュー)](search-howto-index-sharepoint-online.md)
+ [Azure Data Lake Storage Gen2 (プレビュー)](search-howto-index-azure-data-lake-storage.md)

インデクサーは、インデックスをデータ ソース (通常はテーブル、ビュー、または同等の構造体) に接続し、ソース フィールドをインデックスの同等のフィールドにマップします。 実行中、行セットが自動的に JSON に変換され、指定したインデックスに読み込まれます。 すべてのインデクサーはスケジューリングをサポートしているため、データの更新頻度を指定できます。 ほとんどのインデクサーは、変更の追跡を提供します (データ ソースでサポートされている場合)。 インデクサーは、新しいドキュメントを認識するだけでなく、既存のドキュメントの変更と削除を追跡するため、インデックス内のデータをアクティブに管理する必要がありません。

### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Azure Cognitive Search インデックスにデータをプルする方法

インデクサーの機能は、[Azure Portal](search-import-data-portal.md)、[REST API](/rest/api/searchservice/Indexer-operations)、および [.NET SDK](/dotnet/api/azure.search.documents.indexes.searchindexerclient) で公開されています。

ポータルを使用する利点は、通常、Azure Cognitive Search でソース データセットのメタデータを読み取って既定のインデックス スキーマを生成できることです。 生成されたインデックスは、インデックスが処理されるまでは変更可能です。インデックスが処理された後は、インデックスの再作成を必要としないスキーマの編集のみが許可されます。 加えようとしている変更がスキーマに直接影響する場合、インデックスを再構築する必要があります。 

## <a name="verify-data-import-with-search-explorer"></a>Search エクスプローラーを使用してデータのインポートを検証する

ドキュメントのアップロード時に事前チェックを実行する簡単な方法は、ポータルで **Search エクスプローラー** を使用することです。 エクスプローラーを使用すると、コードを記述することなくインデックスを照会できます。 検索エクスペリエンスは、既定の設定 ([単純構文](/rest/api/searchservice/simple-query-syntax-in-azure-search)、既定の [searchMode クエリ パラメーター](/rest/api/searchservice/search-documents)など) に基づきます。 結果は JSON で返されるため、ドキュメント全体を確認できます。

> [!TIP]
> 数多くの [Azure Cognitive Search コード サンプル](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)に埋め込みデータセットやすぐに利用できるデータセットが含まれているため、簡単に使い始めることができます。 ポータルには、("realestate-us-sample" という名前の) 小さな不動産データセットで構成されるサンプルのインデクサーとデータ ソースも用意されています。 サンプル データ ソースで事前構成済みのインデクサーを実行すると、インデックスが作成され、ドキュメントが読み込まれます。このドキュメントは、Search エクスプローラーまたは自分で作成したコードで照会できます。

## <a name="see-also"></a>関連項目

+ [インデクサーの概要](search-indexer-overview.md)
+ [ポータルのチュートリアル: インデックスの作成、読み込み、クエリ](search-get-started-portal.md)