---
title: インポート時にデータをクロールするためのインデクサー
titleSuffix: Azure Cognitive Search
description: Azure SQL Database、SQL Managed Instance、Azure Cosmos DB、または Azure ストレージをクロールして検索可能なデータを抽出し、Azure Cognitive Search インデックスを作成します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f93df91f87f8119a503f2f7c452b61e3af5924f8
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208792"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure Cognitive Search のインデクサー

Azure Cognitive Search の *インデクサー* は、検索可能なデータとメタデータを Azure 外部データ ソースから抽出し、インデックスとデータ ソース間のフィールドのマッピングに基づいてインデックスを作成するクローラーです。 この方法は、インデックスにデータを追加するコードを記述することなく、サービスがデータをプルするため、「プル モデル」と呼ばれることもあります。

インデクサーは、データ ソースの種類またはプラットフォームに基づきます。Azure 上の SQL Server、Cosmos DB、Azure Table Storage、Blob Storage を対象としたインデクサーがあります。 Blob Storage インデクサーには、BLOB コンテンツ タイプに固有のプロパティがあります。

データ取り込みの唯一の手段としてインデクサーを使用したり、インデクサーの使用を含む手法を組み合わせて使用したりして、インデックス内のフィールドの一部だけを読み込むことができます。

インデクサーは、オンデマンドで実行することも、5 分ごとに実行される定期的なデータ更新スケジュールで実行することもできます。 より頻繁に更新するには、Azure Cognitive Search と外部データ ソースの両方のデータを同時に更新するプッシュ モデルが必要です。

## <a name="approaches-for-creating-and-managing-indexers"></a>インデクサーの作成と管理の方法

インデクサーの作成と管理は、次の方法で行うことができます。

* [Portal のデータのインポート ウィザード](search-import-data-portal.md)
* [Service REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

最初は、新しいインデクサーがプレビュー機能として発表されます。 プレビュー機能は、まず API (REST および .NET) に導入され、その後、一般公開を経てポータルに統合されます。 新しいインデクサーを評価する場合は、コードの作成を検討してください。

## <a name="permissions"></a>アクセス許可

インデクサーに関連するすべての操作 (状態や定義の GET 要求を含む) には、[管理者 API キー](search-security-api-keys.md)が必要です。

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>サポートされるデータ ソース

インデクサーは、Azure 上のデータ ストアをクロールします。

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (プレビュー段階)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure SQL Database と SQL Managed Instance](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Virtual Machines における SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="indexer-stages"></a>インデクサーのステージ

最初の実行時に、インデックスが空の場合、テーブルまたはコンテナーで提供されるすべてのデータがインデクサーによって読み取られます。 その後の実行では、通常、変更されたデータのみがインデクサーによって検出され取得されます。 BLOB データの場合、変更の検出は自動で行われます。 Azure SQL や Cosmos DB などの他のデータ ソースで、変更の検出を有効にする必要があります。

インデクサーは、それによって取り込まれたドキュメントのそれぞれについて、ドキュメントの検索からインデックス作成のための最終的な検索エンジンの "ハンドオフ" にいたるまでの複数のステップを実装または調整します。 必要に応じて、インデクサーは、スキルセットが定義されていると想定して、スキルセットの実行と出力を促進するのにも役立ちます。

![インデクサーのステージ](./media/search-indexer-overview/indexer-stages.png "インデクサーのステージ")

### <a name="stage-1-document-cracking"></a>ステージ 1:ドキュメントの解読

ドキュメント解析は、ファイルを開いてコンテンツを抽出するプロセスです。 データ ソースの種類に応じて、インデックス付けが可能なコンテンツを抽出するために、インデクサーによってさまざまな操作の実行が試行されます。  

例 :  

* ドキュメントが [Azure SQL データ ソース](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)内のレコードの場合、インデクサーによってレコードの各フィールドが抽出されます。
* ドキュメントが [Azure Blob Storage データ ソース](search-howto-indexing-azure-blob-storage.md)内の PDF ファイルの場合、そのファイルのテキスト、イメージ、およびメタデータがインデクサーによって抽出されます。
* ドキュメントが [Cosmos DB データ ソース](search-howto-index-cosmosdb.md)内のレコードの場合、インデクサーによって Cosmos DB ドキュメントからフィールドとサブフィールドが抽出されます。

### <a name="stage-2-field-mappings"></a>ステージ 2:フィールド マッピング 

インデクサーによって、ソース フィールドからテキストが抽出され、インデックスまたはナレッジ ストアの送信先フィールドにそれが送信されます。 フィールド名と型が一致すると、パスは明確になります。 ただし、出力には異なる名前または型が必要な場合があります。その場合は、フィールドをマップする方法をインデクサーに指示する必要があります。 このステップは、ドキュメント解析の後、変換前に、インデクサーがソース ドキュメントから読み取るときに発生します。 [フィールド マッピング](search-indexer-field-mappings.md)を定義するときに、ソース フィールドの値は変更されずにそのまま送信先フィールドに送信されます。 フィールド マッピングは省略可能です。

### <a name="stage-3-skillset-execution"></a>ステージ 3:スキルセットの実行

スキルセットの実行は、組み込みまたはカスタムの AI 処理を呼び出す省略可能なステップです。 これは画像分析の形式で光学式文字認識 (OCR) に必要になる場合や、言語翻訳が必要な場合があります。 変換が何であれ、スキルセットの実行は、エンリッチメントが発生する場所です。 インデクサーがパイプラインの場合、[スキルセット](cognitive-search-defining-skillset.md)を "パイプライン内のパイプライン" として考えることができます。 スキルセットには、スキルと呼ばれる独自のステップのシーケンスがあります。

### <a name="stage-4-output-field-mappings"></a>ステージ 4: 出力フィールド マッピング

スキルセットの出力は、実際にはエンリッチされたドキュメントと呼ばれる情報のツリーです。 出力フィールド マッピングを使用すると、このツリーの部分を選択してインデックス内のフィールドにマップすることができます。 [出力フィールド マッピングを定義する](cognitive-search-output-field-mapping.md)方法について学習します。

ソースからターゲット フィールドに逐語的な値を関連付けるフィールド マッピングと同様に、出力フィールド マッピングによって、エンリッチされたドキュメント内の変換された値をインデックス内のターゲット フィールドに関連付ける方法がインデクサーに伝えられます。 省略可能と見なされるフィールド マッピングとは異なり、インデックスに存在する必要がある変換されたコンテンツに対しては、出力フィールド マッピングを常に定義する必要があります。

次の図は、インデクサーのステージ (ドキュメント解析、フィールド マッピング、スキルセットの実行、出力フィールド マッピング) のサンプル インデクサーの[デバッグ セッション](cognitive-search-debug-session.md)表現を示しています。

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="デバッグ セッションのサンプル" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>基本的な構成手順

インデクサーで実行できる機能は、データ ソースごとに異なります。 そのためインデクサーやデータ ソースの構成には、インデクサーの種類ごとに異なる点があります。 しかし基本的な成り立ちと要件は、すべてのインデクサーに共通です。 以降、すべてのインデクサーに共通の手順について取り上げます。

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する
インデクサーは、*データ ソース* オブジェクトからデータ ソース接続を取得します。 データ ソース定義では、接続文字列と、場合によっては資格情報が提供されます。 [データ ソースの作成](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API または [DataSource クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource)を呼び出して、リソースを作成します。

データ ソースは、それを使用するインデクサーとは別に構成および管理されます。これは、1 つのデータ ソースを複数のインデクサーで使用して、一度に複数のインデックスを読み込めることを意味します。

### <a name="step-2-create-an-index"></a>手順 2:インデックスを作成する
インデクサーは、データ インジェストに関連したいくつかのタスクを自動化しますが通常、そこにはインデックスの作成は含まれていません。 前提条件として、定義済みのインデックスが必要です。加えてそのフィールドは、外部データ ソース内のフィールドと一致している必要があります。 各フィールドでは、名前とデータ型が一致する必要があります。 インデックス構築の詳細については、「[インデックスの作成 (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index)」または「[Index クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)」を参照してください。 フィールドの関連付けについては、[Azure Cognitive Search インデクサーのフィールド マッピング](search-indexer-field-mappings.md)に関する記事を参照してください。

> [!Tip]
> インデクサーで自動的にインデックスを生成することはできませんが、ポータルにある**データのインポート**ウィザードを活用することができます。 ほとんどの場合、ウィザードで、ソース内の既存のメタデータからインデックス スキーマを推測し、仮のインデックス スキーマを得ることができます。ウィザードがアクティブである間は、このスキーマをインライン編集することが可能です。 サービスのインデックスが作成された後にポータルで行う編集は、主に新しいフィールドの追加に限定されます。 ウィザードは、あくまでインデックスの作成にご使用ください。インデックスの修正には適していません。 実践的なスキルを身に付けるには、[ポータルのチュートリアル](search-get-started-portal.md)をご覧ください。

### <a name="step-3-create-and-schedule-the-indexer"></a>手順 3:インデクサーを作成してスケジュールを設定する
インデクサー定義は、データの取り込みに関連するすべての要素をまとめたコンストラクトです。 必要な要素には、データ ソースとインデックスが含まれます。 省略可能な要素には、スケジュールとフィールド マッピングが含まれます。 フィールド マッピングは、ソース フィールドとインデックス フィールドが明らかに対応している場合にのみ、省略可能となります。 インデクサー構築の詳細については、「[インデクサーの作成 (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)」を参照してください。

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>インデクサーのオンデマンド実行

インデックス作成はスケジュール設定するのが普通ですが、[Run コマンド](https://docs.microsoft.com/rest/api/searchservice/run-indexer)を使用して、インデクサーをオンデマンドで呼び出すことも可能です。

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> API の実行で正しい応答があった場合、インデクサーの呼び出しはスケジュール済みですが、実際の処理は非同期的に実行されます。 

インデクサーの状態は、ポータルか Get Indexer Status API を使用して監視できます。 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>インデクサーの状態の取得

インデクサーの現在の状態と実行の履歴は、[インデクサー状態の取得](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)コマンドを使用して取得できます。

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

応答には、全体的なインデクサーの状態、最後の (または実行中の) インデクサー呼び出し、およびインデクサー呼び出しの最近の履歴が含まれています。

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

実行履歴には、最近完了した 50 件の実行内容が含まれ、時系列の逆の順に並べられます (そのため、最近の実行内容が応答の最初に表示されます)。

## <a name="next-steps"></a>次のステップ
基本的な概念の説明は以上です。次のステップとしてデータ ソースの種類ごとの要件とタスクを確認してください。

* [Azure SQL Database、SQL Managed Instance、または Azure 仮想マシン上の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Cognitive Search BLOB インデクサーを使用した CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)
* [Azure Cognitive Search BLOB インデクサーを使用した JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)
