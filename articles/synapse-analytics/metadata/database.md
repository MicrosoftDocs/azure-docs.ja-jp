---
title: 共有データベース
description: Azure Synapse Analytics は、サーバーレス Apache Spark プールでデータベースを作成することでサーバーレス SQL プール エンジンと SQL プール エンジンからアクセスできるようにする共有メタデータ モデルを提供します。
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 93602e522338166abac98c3e4a198e1aff392d21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934970"
---
# <a name="azure-synapse-analytics-shared-database"></a>Azure Synapse Analytics の共有データベース

Azure Synapse Analytics では、さまざまな計算ワークスペース エンジンがデータベースとテーブルを共有できます。 現時点では、Apache Spark プールに作成されたデータベースと Parquet テーブルは、サーバーレス SQL プール エンジンと自動的に共有されます。

Spark ジョブを使用して作成されたデータベースは、その同じ名前で、サーバーレス SQL プール エンジンも含め、ワークスペース内の現在および将来のすべての Spark プールから参照できるようになります。 サーバーレス SQL プールを使用して、このレプリケートされたデータベースにカスタム オブジェクト (外部テーブル、ビュー、プロシージャ) を直接追加することはできません。

Spark の既定のデータベース (`default` と呼ばれる) は、`default` と呼ばれるデータベースとしてサーバーレス SQL プール コンテキストにも表示されます。

データベースはサーバーレス SQL プールに非同期的に同期されるため、表示されるまでに遅延が発生します。

## <a name="manage-a-spark-created-database"></a>Spark で作成されたデータベースを管理する

Spark を使用して、Spark で作成されたデータベースを管理します。 たとえば、Spark プール ジョブを使用してこれを削除したり、Spark からその内部にテーブルを作成したりします。

サーバーレス SQL プールを使用して Spark で作成されたデータベースにオブジェクトを作成した場合、またはデータベースを削除しようとした場合、操作は成功します。 ただし、元の Spark データベースは変更されません。

## <a name="how-name-conflicts-are-handled"></a>名前の競合の処理方法

Spark データベースの名前が既存のサーバーレス SQL プール データベースの名前と競合している場合は、サーバーレス SQL プールで Spark データベースに対してサフィックスが追加されます。 サーバーレス SQL プールでのサフィックスは `_<workspace name>-ondemand-DefaultSparkConnector` です。

たとえば、`mydb` という名前の Spark データベースが Azure Synapse ワークスペース `myws` で作成され、その名前が付いたサーバーレス SQL プール データベースが既に存在する場合は、サーバーレス SQL プールの Spark データベースを `mydb_myws-ondemand-DefaultSparkConnector` という名前を使用して参照する必要があります。

> [!CAUTION]
> 注意:この動作に依存しないようにしてください。

## <a name="security-model"></a>セキュリティ モデル

Spark データベースおよびテーブルは、SQL エンジン内のそれらの同期された表現とともに、基になるストレージ レベルで保護されます。

データベースを作成するセキュリティ プリンシパルは、そのデータベースの所有者と見なされ、データベースとそのオブジェクトに対するすべての権限を持ちます。

ユーザーまたはセキュリティ グループなどのセキュリティ プリンシパルにデータベースへのアクセス権を付与するには、`warehouse` ディレクトリ内の基になるフォルダーおよびファイルに対して、適切な POSIX フォルダーおよびファイル アクセス許可を指定します。 

たとえば、あるセキュリティ プリンシパルがデータベース内のテーブルを読み取るには、`warehouse` ディレクトリ内のデータベース フォルダーから開始されるすべてのフォルダーで、そのセキュリティ プリンシパルに `X` および `R` のアクセス許可が割り当てられている必要があります。 また、ファイル (テーブルの基になるデータ ファイルなど) には `R` のアクセス許可が必要です。 

セキュリティ プリンシパルに、データベース内にオブジェクトを作成したり、オブジェクトを削除したりする権限が必要な場合は、`warehouse` フォルダー内のフォルダーとファイルに対する追加の `W` 権限が必要になります。

## <a name="examples"></a>例

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>サーバーレス SQL プールを使用して Spark データベースを作成して接続する

まず、ワークスペースに既に作成済みの Spark クラスターを使用して、`mytestdb` という名前の新しい Spark データベースを作成します。 たとえば、次の .NET for Spark ステートメントで Spark C# ノートブックを使用して、これを実現できます。

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

少し時間が経過すると、サーバーレス SQL プールからデータベースを確認できるようになります。 たとえば、サーバーレス SQL プールから次のステートメントを実行します。

```sql
SELECT * FROM sys.databases;
```

結果に `mytestdb` が含まれていることを確認します。

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics の共有メタデータについての詳細情報](overview.md)
- [Azure Synapse Analytics の共有メタデータ テーブルについての詳細情報](table.md)
