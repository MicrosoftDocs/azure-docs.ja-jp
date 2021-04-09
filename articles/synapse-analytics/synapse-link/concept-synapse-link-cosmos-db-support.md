---
title: Azure Cosmos DB 用の Azure Synapse Link でサポートされる機能
description: Azure Cosmos DB 用の Azure Synapse Link で現在サポートされているアクションの一覧を把握する
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: acomet
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: a03a96f34861b84754de9d9ee4180100b58fcd37
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869227"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Azure Cosmos DB 用の Azure Synapse Link でサポートされる機能

この記事では、Azure Cosmos DB 用の Azure Synapse Link で現在サポートされている機能について説明します。

## <a name="azure-synapse-support"></a>Azure Synapse サポート

Azure Cosmos DB には、次の 2 種類のコンテナーがあります。
* HTAP コンテナー - Synapse Link が有効になっているコンテナー。 このコンテナーには、トランザクション ストアと分析ストアの両方があります。 
* OLTP コンテナー - Synapse Link が有効になっていないコンテナー。 このコンテナーにはトランザクション ストアのみが含まれており、分析ストアはありません。

> [!IMPORTANT]
> 現在、Azure Synapse Link for Azure Cosmos DB は、マネージド仮想ネットワークが有効になっていない Synapse ワークスペースでサポートされています。 

Synapse Link を有効にしなくても、Azure Cosmos DB コンテナーに接続できます。 このシナリオでは、トランザクション ストアに対して読み取り/書き込みのみを行うことができます。 Azure Cosmos DB 用の Synapse Link 内で現時点でサポートされている機能の一覧を次に示します。 

| カテゴリ              | 説明 |[Apache Spark プール](../sql/on-demand-workspace-overview.md) | [サーバーレス SQL プール](../sql/on-demand-workspace-overview.md) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **ランタイム サポート** |Azure Cosmos DB にアクセスするためにサポートされている Azure Synapse ランタイム| ✓ | ✓ |
| **Azure Cosmos DB API サポート** | サポートされている Azure Cosmos DB API の種類 | SQL / MongoDB | SQL / MongoDB |
| **Object**  |Azure Cosmos DB コンテナーを直接指す、作成できるテーブルなどのオブジェクト| データフレーム、ビュー、テーブル | 表示 |
| **読み取り**    | 読み取り可能な Azure Cosmos DB コンテナーのタイプ | OLTP / HTAP | HTAP  |
| **書き込み**   | Azure Synapse ランタイムを使用して Azure Cosmos DB コンテナーにデータを書き込むことができる | はい | いいえ |

* Spark から Azure Cosmos DB コンテナーにデータを書き込む場合、この処理は Azure Cosmos DB のトランザクション ストアを介して行われます。 これは要求ユニットを使用することによって、Azure Cosmos DB のトランザクション パフォーマンスに影響を与えます。
* 外部テーブルを介した専用 SQL プールの統合は現在、サポートされていません。
 
## <a name="supported-code-generated-actions-for-spark"></a>Spark に対してサポートされているコード生成アクション

| ジェスチャ              | 説明 |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **データフレームへの読み込み** |Spark データフレームへのデータの読み込みと読み取りを行います |✓| ✓ |
| **Spark テーブルの作成** |Azure Cosmos DB コンテナーを指すテーブルを作成します|✓| ✓ |
| **コンテナーへのデータフレームの書き込み** |コンテナーにデータを書き込みます|✓| ✓ |
| **コンテナーからのストリーミング データフレームの読み込み** |Azure Cosmos DB の変更フィードを使用してデータをストリーミングします|✓| ✓ |
| **コンテナーへのストリーミング データフレームの書き込み** |Azure Cosmos DB の変更フィードを使用してデータをストリーミングします|✓| ✓ |

## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>サーバーレス SQL プールに対してサポートされているコード生成アクション

| ジェスチャ              | 説明 |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **データの探索** |使い慣れた T-SQL 構文と自動スキーマ推論を使用してコンテナーからデータを探索する|X| ✓ |
| **ビューの作成と BI レポートの作成** |SQL ビューを作成して、サーバーレス SQL プールで BI 用コンテナーに直接アクセス可能 |X| ✓ |
| **Cosmos DB データおよびさまざまなデータ ソースの結合** | CETAS を使用して、Azure Blob Storage または Azure Data Lake Storage のデータと共に、Cosmos DB コンテナーからデータを読み取るクエリの結果を格納する |X| ✓ |

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB 用の Synapse Link に接続する](../quickstart-connect-synapse-link-cosmos-db.md)方法を確認する
* [Spark を使用して Cosmos DB 分析ストアにクエリを実行する方法を確認する](how-to-query-analytical-store-spark.md)