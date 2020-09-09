---
title: シャード化されたデータベースのクエリ実行
description: エラスティック データベース クライアント ライブラリを使用して複数のシャードを対象にクエリを実行します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: fe8b977d3385f8ef4e4ceaac4dde7c0ac6a79839
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84034633"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Elastic Database ツールを使用したマルチシャード クエリ
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="overview"></a>概要

[Elastic Database ツール](elastic-scale-introduction.md)を利用すれば、シャード化されたデータベース ソリューションを作成できます。 **マルチシャード クエリ実行**は、複数のシャードにまたがるクエリの実行が必要となるデータ収集/レポート作成などのタスクに使用されます (すべての操作を単一のシャード上で実行する[データ依存ルーティング](elastic-scale-data-dependent-routing.md)と比べてください)。

1. **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap))、**TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap))、または **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) メソッドを使用して、**RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) または **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) を取得します。 「[ShardMapManager の作成](elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)」と「[RangeShardMap または ListShardMap の取得](elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)」を参照してください。
2. **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)) オブジェクトを作成します。
3. **MultiShardStatement または MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) を作成します。
4. **CommandText プロパティ** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) を T-SQL コマンドに設定します。
5. **ExecuteQueryAsync または ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) メソッドを呼び出してコマンドを実行します。
6. **MultiShardResultSet または MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) クラスを使用して結果を表示します。

## <a name="example"></a>例

次のコードは、 **myShardMap** という名前の *ShardMap*を使用してマルチシャード クエリを実行する方法を示しています。

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

主な相違点は、マルチシャード接続の構築です。 **SqlConnection** が 1 つのデータベースに作用する一方、**MultiShardConnection** は入力として***シャードのコレクション***を受け取ります。 シャードのコレクションは、シャード マップから設定します。 次に、 **UNION ALL** セマンティクスを使用してシャードのコレクションに対するクエリを実行して、単一の全体的な結果を生成します。 必要に応じて、コマンドの **ExecutionOptions** プロパティを使用して、行の元になっているシャードの名前を出力に追加できます。

ここで、 **myShardMap.GetShards()** の呼び出しに注目してください。 このメソッドは、シャード マップからすべてのシャードを取得し、すべての関連データベースを対象にクエリを実行する簡単な方法を提供します。 マルチシャード クエリのためのシャードのコレクションは、 **myShardMap.GetShards()** 呼び出しから返されたコレクションに対して LINQ クエリを実行することによって、さらに絞り込むことができます。 部分的な結果のポリシーとの組み合わせにおいて、マルチシャード クエリ実行の現在の機能は、数十から最大で数百のシャードで適切に動作するように設計されています。

現在のマルチシャード クエリ実行には、クエリの対象となるシャードとシャードレットの検証が欠けているという制限があります。 データ依存ルーティングでは特定のシャードがシャード マップの一部であることが照会時に確認されますが、マルチシャード クエリ実行ではこのようなチェックは実行されません。 そのため、シャード マップから削除されたデータベースに対してマルチシャード クエリが実行される可能性があります。

## <a name="multi-shard-queries-and-split-merge-operations"></a>マルチシャード クエリと分割/マージ操作

マルチシャード クエリでは、照会されたデータベース上のシャードレットが進行中の分割/マージ操作に参加しているかどうかは確認されません。 (「[Elastic Database 分割/マージ ツールを使用したスケーリング](elastic-scale-overview-split-and-merge.md)」をご覧ください)。そのため、同じマルチシャード クエリ内で複数のデータベースに対して同じシャードレットの行が表示される不整合が発生する可能性があります。 これらの制限を考慮したうえで、マルチシャード クエリを実行するときは、進行中の分割/マージ操作とシャード マップへの変更をドレインすることを検討してください。

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]