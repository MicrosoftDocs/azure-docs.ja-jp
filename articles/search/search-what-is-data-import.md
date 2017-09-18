---
title: "Azure Search でのデータ アップロード | Microsoft Docs"
description: "Azure Search のインデックスにデータをアップロードする方法について説明します。"
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5a601b75ec67824e72d8736bc3c45f8e1231ca86
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="upload-data-to-azure-search"></a>Azure Search へのデータのアップロード
> [!div class="op_single_selector"]
> * [概要](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

インデックスにデータを設定する方法は 2 とおりあります。 1 つ目は、Azure Search [REST API](search-import-data-rest-api.md) または [.NET SDK](search-import-data-dotnet.md) を使用して、手動でデータをインデックスにプッシュする方法です。 2 つ目は、インデックスに[サポートされているデータ ソースを指定](search-indexer-overview.md)して、Azure Search によってデータが自動的にプルされるようにする方法です。

## <a name="push-data-to-an-index"></a>インデックスにデータをプッシュする
このアプローチでは、データを Azure Search にプログラムで送信し、検索に利用できるようにします。 待機時間の要件が非常に厳しいアプリケーションの場合 (たとえば、検索操作を動的な在庫データベースと同期する必要がある場合) は、プッシュ モデルしか利用できません。

インデックスにデータをプッシュするには、[REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) または [.NET SDK](search-import-data-dotnet.md) を使用することができます。 現在では、ポータルを使用してデータをプッシュするためのツール サポートはありません。

このアプローチでは、ドキュメントを個別に、またはバッチでアップロードできるため、プル モデルよりも柔軟です ("バッチごとに最大 1,000 個" と "16 MB" のうち、どちらか先に達した制限が適用されます)。 プッシュ モデルでは、データのある場所にかかわらず、ドキュメントを Azure Search にアップロードすることもできます。

Azure Search で認識されるデータ形式は JSON です。データセット内のすべてのドキュメントに、インデックス スキーマで定義されたフィールドにマップされるフィールドが存在する必要があります。 

## <a name="pull-data-into-an-index"></a>インデックスへのデータのプル
プル モデルは、サポートされているデータ ソースをクロールし、データをインデックスに自動的にアップロードします。 Azure Search では、この機能は "*インデクサー*" によって実装され、現時点では [Blob Storage](search-howto-indexing-azure-blob-storage.md)、[Table Storage](search-howto-indexing-azure-tables.md)、[Azure Cosmos DB](http://aka.ms/documentdb-search-indexer)、[Azure SQL Database、Azure VM 上の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) で利用可能です。 

インデクサーは、インデックスをデータ ソース (通常はテーブル、ビュー、または同等の構造体) に接続し、ソース フィールドをインデックスの同等のフィールドにマップします。 実行中、行セットが自動的に JSON に変換され、指定したインデックスに読み込まれます。 すべてのインデクサーはスケジューリングをサポートしているため、データの更新頻度を指定できます。 ほとんどのインデクサーは、変更の追跡を提供します (データ ソースでサポートされている場合)。 インデクサーは、新しいドキュメントを認識するだけでなく、既存のドキュメントの変更と削除を追跡するため、インデックス内のデータをアクティブに管理する必要がありません。 

インデクサーの機能は、[Azure Portal](search-import-data-portal.md)、[REST API](/rest/api/searchservice/Indexer-operations)、および [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations) で公開されています。 

ポータルを使用する利点は、通常、Azure Search でソース データセットのメタデータを読み取って既定のインデックス スキーマを生成できることです。 生成されたインデックスは、インデックスが処理されるまでは変更可能です。インデックスが処理された後は、インデックスの再作成を必要としないスキーマの編集のみが許可されます。 加えようとしている変更がスキーマに直接影響する場合、インデックスを再構築する必要があります。 

インデックスが設定された後は、ポータルのコマンド バーの **Search エクスプローラー**を検証手順で使用できます。

## <a name="query-an-index-using-search-explorer"></a>Search エクスプローラーを使用したインデックスの照会

ドキュメントのアップロード時に事前チェックを実行する簡単な方法は、ポータルで **Search エクスプローラー**を使用することです。 エクスプローラーを使用すると、コードを記述することなくインデックスを照会できます。 検索エクスペリエンスは、既定の設定 ([単純構文](/rest/api/searchservice/simple-query-syntax-in-azure-search)、既定の [searchMode クエリ パラメーター](/rest/api/searchservice/search-documents)など) に基づきます。 結果は JSON で返されるため、ドキュメント全体を確認できます。

> [!TIP]
> 数多くの [Azure Search コード サンプル](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)に埋め込みデータセットやすぐに利用できるデータセットが含まれているため、簡単に使い始めることができます。 ポータルには、("realestate-us-sample" という名前の) 小さな不動産データセットで構成されるサンプルのインデクサーとデータ ソースも用意されています。 サンプル データ ソースで事前構成済みのインデクサーを実行すると、インデックスが作成され、ドキュメントが読み込まれます。このドキュメントは、Search エクスプローラーまたは自分で作成したコードで照会できます。
