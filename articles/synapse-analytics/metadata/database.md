---
title: 共有データベース
description: Azure Synapse Analytics は、Apache Spark でデータベースを作成することで SQL オンデマンド (プレビュー) エンジンと SQL プール エンジンからアクセスできるようにする共有メタデータ モデルを提供します。
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 409311594cc26680217948e2394420fdaea29024
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015375"
---
# <a name="azure-synapse-analytics-shared-database"></a>Azure Synapse Analytics の共有データベース

Azure Synapse Analytics では、さまざまな計算ワークスペース エンジンが、Spark プール (プレビュー) と SQL オンデマンド (プレビュー) エンジンの間でデータベースとテーブルを共有できます。

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Spark ジョブを使用して作成されたデータベースは、その同じ名前で、ワークスペース内の現在および将来のすべての Spark プール (プレビュー) と、SQL オンデマンド エンジンから参照できるようになります。

Spark の既定のデータベース (`default` と呼ばれる) は、`default` と呼ばれるデータベースとして SQL オンデマンド コンテキストにも表示されます。

データベースは SQL オンデマンドに非同期的に同期されるため、表示されるまでに遅延が発生します。

## <a name="manage-a-spark-created-database"></a>Spark で作成されたデータベースを管理する

Spark を使用して、Spark で作成されたデータベースを管理します。 たとえば、Spark プール ジョブを使用してこれを削除したり、Spark からその内部にテーブルを作成したりします。

SQL オンデマンドを使用して Spark で作成されたデータベースにオブジェクトを作成した場合、またはデータベースを削除しようとした場合、操作は成功します。 ただし、元の Spark データベースは変更されません。

## <a name="how-name-conflicts-are-handled"></a>名前の競合の処理方法

Spark データベースの名前が既存の SQL オンデマンド データベースの名前と競合している場合は、SQL オンデマンドで Spark データベースに対してサフィックスが追加されます。 SQL オンデマンドでのサフィックスは `_<workspace name>-ondemand-DefaultSparkConnector` です。

たとえば、`mydb` という名前の Spark データベースが Azure Synapse ワークスペース `myws` で作成され、その名前を持つ SQL オンデマンド データベースが既に存在する場合は、SQL オンデマンドの Spark データベースを `mydb_myws-ondemand-DefaultSparkConnector` という名前を使用して参照する必要があります。

> [!CAUTION]
> 注意:この動作に依存しないようにしてください。

## <a name="security-model"></a>セキュリティ モデル

Spark データベースおよびテーブルは、SQL エンジン内のそれらの同期された表現とともに、基になるストレージ レベルで保護されます。

データベースを作成するセキュリティ プリンシパルは、そのデータベースの所有者と見なされ、データベースとそのオブジェクトに対するすべての権限を持ちます。

ユーザーまたはセキュリティ グループなどのセキュリティ プリンシパルにデータベースへのアクセス権を付与するには、`warehouse` ディレクトリ内の基になるフォルダーおよびファイルに対して、適切な POSIX フォルダーおよびファイル アクセス許可を指定します。 

たとえば、あるセキュリティ プリンシパルがデータベース内のテーブルを読み取るには、`warehouse` ディレクトリ内のデータベース フォルダーから開始されるすべてのフォルダーで、そのセキュリティ プリンシパルに `X` および `R` のアクセス許可が割り当てられている必要があります。 また、ファイル (テーブルの基になるデータ ファイルなど) には `R` のアクセス許可が必要です。 

セキュリティ プリンシパルに、データベース内にオブジェクトを作成したり、オブジェクトを削除したりする権限が必要な場合は、`warehouse` フォルダー内のフォルダーとファイルに対する追加の `W` 権限が必要になります。

## <a name="examples"></a>例

### <a name="create-and-connect-to-spark-database-with-sql-on-demand"></a>SQL オンデマンドを使用して Spark データベースを作成して接続する

まず、ワークスペースに既に作成済みの Spark クラスターを使用して、`mytestdb` という名前の新しい Spark データベースを作成します。 たとえば、次の .NET for Spark ステートメントで Spark C# ノートブックを使用して、これを実現できます。

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

少し時間が経過すると、SQL オンデマンドからデータベースを確認できるようになります。 たとえば、SQL オンデマンドから次のステートメントを実行します。

```sql
SELECT * FROM sys.databases;
```

結果に `mytestdb` が含まれていることを確認します。

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics の共有メタデータについての詳細情報](overview.md)
- [Azure Synapse Analytics の共有メタデータ テーブルについての詳細情報](table.md)
