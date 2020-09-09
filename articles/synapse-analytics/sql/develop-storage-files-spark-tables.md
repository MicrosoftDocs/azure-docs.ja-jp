---
title: SQL オンデマンド (プレビュー) で Apache Spark の外部テーブル定義を同期する
description: SQL オンデマンド (プレビュー) を使用して Spark テーブルに対してクエリを実行する方法の概要
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3e9f688a31d2847505e974ab6a1557aa6a7b2047
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046837"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>SQL オンデマンド (プレビュー) で Apache Spark for Azure Synapse の外部テーブル定義を同期する

SQL オンデマンド (プレビュー) では、Apache Spark for Azure Synapse プールからメタデータを自動的に同期することができます。 Spark プール (プレビュー) に存在するデータベースごとに、SQL オンデマンド データベースが作成されます。 

Parquet に基づく、Azure Storage に配置された各 Spark 外部テーブルに対して、SQL オンデマンド データベースに外部テーブルが作成されます。 そのため、Spark プールをシャットダウンしても、SQL オンデマンドから Spark 外部テーブルに対するクエリを引き続き実行できます。

テーブルが Spark でパーティション分割されている場合、ストレージ内のファイルはフォルダーごとに整理されます。 SQL オンデマンドでは、パーティション メタデータが使用され、クエリに関連するフォルダーとファイルのみが対象になります。

メタデータの同期は、Azure Synapse ワークスペースにプロビジョニングされた各 Spark プールに対して自動的に構成されます。 Spark 外部テーブルに対するクエリをすぐに開始できます。

Spark の Azure Storage に配置された各 Parquet 外部テーブルは、SQL オンデマンド データベースに対応する dbo スキーマの外部テーブルで表されます。 

Spark 外部テーブルのクエリの場合は、外部 [spark_table] を対象とするクエリを実行します。 次の例を実行する前に、ファイルが配置されている[ストレージ アカウントへの適切なアクセス許可](develop-storage-files-storage-access-control.md)を持っていることを確認してください。

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Spark 外部テーブルの列の追加、削除、または変更コマンドは、SQL オンデマンドの外部テーブルには反映されません。

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark データ型から SQL データ型へのマッピング

| Spark データ型 | SQL データ型               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | INT                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)* (JSON へ)** |
| MapType         | varchar(max)* (JSON へ)** |
| StructType      | varchar(max)* (JSON へ)** |

\* 使用される照合順序は Latin1_General_100_BIN2_UTF8 です。

** ArrayType、MapType、StructType は JSON として表されます。



## <a name="next-steps"></a>次のステップ

ストレージ アクセス制御の詳細については、[ストレージ アクセス制御](develop-storage-files-storage-access-control.md)に関する記事をご覧ください。
