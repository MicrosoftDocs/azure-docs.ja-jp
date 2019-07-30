---
title: インデックス作成時にデータ ソースをクロールするためのインデクサー - Azure Search
description: Azure SQL データベース、Azure Cosmos DB、または Azure Storage をクロールして検索可能なデータを抽出し、Azure Search インデックスを作成します。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6871c75f0af662762ffac6e7346d4999e55eb937
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303433"
---
# <a name="indexers-in-azure-search"></a>Azure Search のインデクサー

Azure Search の "*インデクサー*" は、検索可能なデータとメタデータを Azure 外部データ ソースから抽出し、インデックスとデータ ソース間のフィールドのマッピングに基づいてインデックスを作成するクローラーです。 この方法は、インデックスにデータを追加するコードを記述することなく、サービスがデータをプルするため、「プル モデル」と呼ばれることもあります。

インデクサーは、データ ソースの種類またはプラットフォームに基づきます。Azure 上の SQL Server、Cosmos DB、Azure Table Storage、Blob Storage を対象としたインデクサーがあります。 Blob Storage インデクサーには、BLOB コンテンツ タイプに固有のプロパティがあります。

データ取り込みの唯一の手段としてインデクサーを使用したり、インデクサーの使用を含む手法を組み合わせて使用したりして、インデックス内のフィールドの一部だけを読み込むことができます。

インデクサーは、オンデマンドで実行することも、5 分ごとに実行される定期的なデータ更新スケジュールで実行することもできます。 より頻繁に更新するには、Azure Search と外部データ ソースの両方のデータを同時に更新するプッシュ モデルが必要です。

## <a name="approaches-for-creating-and-managing-indexers"></a>インデクサーの作成と管理の方法

インデクサーの作成と管理は、次の方法で行うことができます。

* [Portal のデータのインポート ウィザード](search-import-data-portal.md)
* [サービス REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

最初は、新しいインデクサーがプレビュー機能として発表されます。 プレビュー機能は、まず API (REST および .NET) に導入され、その後、一般公開を経てポータルに統合されます。 新しいインデクサーを評価する場合は、コードの作成を検討してください。

## <a name="permissions"></a>アクセス許可

インデクサーに関連するすべての操作 (状態や定義の GET 要求を含む) には、[管理者 API キー](search-security-api-keys.md)が必要です。 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>サポートされるデータ ソース

インデクサーは、Azure 上のデータ ストアをクロールします。

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) 

> [!Note]
> [コグニティブ検索](cognitive-search-concept-intro.md)では Azure Table Storage はサポートされていません。
>

## <a name="basic-configuration-steps"></a>基本的な構成手順
インデクサーで実行できる機能は、データ ソースごとに異なります。 そのためインデクサーやデータ ソースの構成には、インデクサーの種類ごとに異なる点があります。 しかし基本的な成り立ちと要件は、すべてのインデクサーに共通です。 以降、すべてのインデクサーに共通の手順について取り上げます。

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する
インデクサーは、*データ ソース* オブジェクトからデータ ソース接続を取得します。 データ ソース定義では、接続文字列と、場合によっては資格情報が提供されます。 [データ ソースの作成](https://docs.microsoft.com/rest/api/searchservice/create-data-source) REST API または [DataSource クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource)を呼び出して、リソースを作成します。

データ ソースは、それを使用するインデクサーとは別に構成および管理されます。これは、1 つのデータ ソースを複数のインデクサーで使用して、一度に複数のインデックスを読み込めることを意味します。

### <a name="step-2-create-an-index"></a>手順 2:インデックスを作成する
インデクサーは、データ インジェストに関連したいくつかのタスクを自動化しますが通常、そこにはインデックスの作成は含まれていません。 前提条件として、定義済みのインデックスが必要です。加えてそのフィールドは、外部データ ソース内のフィールドと一致している必要があります。 各フィールドでは、名前とデータ型が一致する必要があります。 インデックス構築の詳細については、[インデックスの作成 (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) または [Index クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)に関するページを参照してください。 フィールドの関連付けについては、「[Azure Search インデクサーのフィールド マッピング](search-indexer-field-mappings.md)」を参照してください。

> [!Tip]
> インデクサーで自動的にインデックスを生成することはできませんが、ポータルにある**データのインポート**ウィザードを活用することができます。 ほとんどの場合、ウィザードで、ソース内の既存のメタデータからインデックス スキーマを推測し、仮のインデックス スキーマを得ることができます。ウィザードがアクティブである間は、このスキーマをインライン編集することが可能です。 サービスのインデックスが作成された後にポータルで行う編集は、主に新しいフィールドの追加に限定されます。 ウィザードは、あくまでインデックスの作成にご使用ください。インデックスの修正には適していません。 実践的なスキルを身に付けるには、[ポータルのチュートリアル](search-get-started-portal.md)をご覧ください。

### <a name="step-3-create-and-schedule-the-indexer"></a>手順 3:インデクサーを作成してスケジュールを設定する
インデクサー定義は、データの取り込みに関連するすべての要素をまとめたコンストラクトです。 必要な要素には、データ ソースとインデックスが含まれます。 省略可能な要素には、スケジュールとフィールド マッピングが含まれます。 フィールド マッピングは、ソース フィールドとインデックス フィールドが明らかに対応している場合にのみ、省略可能となります。 インデクサーは、データ ソースが同じサブスクリプションに属していれば、他のサービスのデータ ソースであっても参照できます。 インデクサー構築の詳細については、「 [Create Indexer (Azure Search REST API) (インデクサーの作成 (Azure Search REST API))](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)」を参照してください。

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>インデクサーのオンデマンド実行

インデックス作成はスケジュール設定するのが普通ですが、[Run コマンド](https://docs.microsoft.com/rest/api/searchservice/run-indexer)を使用して、インデクサーをオンデマンドで呼び出すことも可能です。

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> API の実行で正しい応答があった場合、インデクサーの呼び出しはスケジュール済みですが、実際の処理は非同期的に実行されます。 

インデクサーの状態は、ポータルか Get Indexer Status API を使用して監視できます。 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>インデクサーの状態の取得

インデクサーの現在の状態と実行の履歴は、[インデクサー状態の取得](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)コマンドを使用して取得できます。


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

応答には、全体的なインデクサーの状態、最後の (または実行中の) インデクサー呼び出し、およびインデクサー呼び出しの最近の履歴が含まれています。

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

実行履歴には、最近完了した 50 件の実行内容が含まれ、時系列の逆の順に並べられます (そのため、最近の実行内容が応答の最初に表示されます)。

## <a name="next-steps"></a>次の手順
基本的な概念の説明は以上です。次のステップとしてデータ ソースの種類ごとの要件とタスクを確認してください。

* [Azure SQL Database または Azure 仮想マシン上の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)
* [Azure Search BLOB インデクサーを使用した CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)
* [Azure Search BLOB インデクサーを使用した JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)
