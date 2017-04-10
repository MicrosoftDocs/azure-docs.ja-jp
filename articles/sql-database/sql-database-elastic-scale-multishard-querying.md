---
title: "シャード化された Azure SQL Database をクエリする | Microsoft Docs"
description: "エラスティック データベース クライアント ライブラリを使用して複数のシャードを対象にクエリを実行します。"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: a4379c15-f213-4026-ab6f-a450ee9d5758
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2016
ms.author: torsteng
translationtype: Human Translation
ms.sourcegitcommit: 873df52da976597ddfaa8f777b90382bfa7723fc
ms.openlocfilehash: aa54ec3d982ea4422422bd4e520d2211aad57159


---
# <a name="multi-shard-querying"></a>マルチシャード クエリ実行
## <a name="overview"></a>Overview
[Elastic Database ツール](sql-database-elastic-scale-introduction.md)を利用すれば、シャード化されたデータベース ソリューションを作成できます。 **マルチシャード クエリ実行**は、複数のシャードにまたがるクエリの実行が必要となるデータ収集/レポート作成などのタスクに使用されます  (すべての操作を単一のシャード上で実行する[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)と比べてください)。 

1. [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)、[**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)、または [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) メソッドを使用して、[**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) または [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) を取得する。 「[**ShardMapManager の作成**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)」と「[**RangeShardMap または ListShardMap の取得**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)」をご覧ください。
2. **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)** オブジェクトを作成する。
3. **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)** を作成する。 
4. **[CommandText プロパティ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)**に T-SQL コマンドを設定する。
5. **[ExecuteReader メソッド](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**を呼び出してコマンドを実行する。
6. **[MultiShardDataReader クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**を使用して結果を表示する。 

## <a name="example"></a>例
次のコードは、 **myShardMap** という名前の *ShardMap*を使用してマルチシャード クエリを実行する方法を示しています。 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
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


主な相違点は、マルチシャード接続の構築です。 **SqlConnection** が単一のデータベースに作用する一方、**MultiShardConnection** は入力として***シャードのコレクション***を受け取ります。 シャードのコレクションは、シャード マップから設定します。 次に、 **UNION ALL** セマンティクスを使用してシャードのコレクションに対するクエリを実行して、単一の全体的な結果を生成します。 必要に応じて、コマンドの **ExecutionOptions** プロパティを使用して、行の元になっているシャードの名前を出力に追加できます。 

ここで、 **myShardMap.GetShards()**の呼び出しに注目してください。 このメソッドは、シャード マップからすべてのシャードを取得し、すべての関連データベースを対象にクエリを実行する簡単な方法を提供します。 マルチシャード クエリのためのシャードのコレクションは、 **myShardMap.GetShards()**呼び出しから返されたコレクションに対して LINQ クエリを実行することによって、さらに絞り込むことができます。 部分的な結果のポリシーとの組み合わせにおいて、マルチシャード クエリ実行の現在の機能は、数十から最大で数百のシャードで適切に動作するように設計されています。

現在のマルチシャード クエリ実行には、クエリの対象となるシャードとシャードレットの検証が欠けているという制限があります。 データ依存ルーティングでは特定のシャードがシャード マップの一部であることが照会時に確認されますが、マルチシャード クエリ実行ではこのようなチェックは実行されません。 そのため、シャード マップから削除されたデータベースに対してマルチシャード クエリが実行される可能性があります。

## <a name="multi-shard-queries-and-split-merge-operations"></a>マルチシャード クエリと分割/マージ操作
マルチシャード クエリでは、照会されたデータベース上のシャードレットが進行中の分割/マージ操作に参加しているかどうかは確認されません。 (「[Elastic Database 分割/マージ ツールを使用したスケーリング](sql-database-elastic-scale-overview-split-and-merge.md)」をご覧ください)。そのため、同じマルチシャード クエリ内で複数のデータベースに対して同じシャードレットの行が表示される不整合が発生する可能性があります。 これらの制限を考慮したうえで、マルチシャード クエリを実行するときは、進行中の分割/マージ操作とシャード マップへの変更をドレインすることを検討してください。

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>関連項目
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)** のクラスとメソッド。

[Elastic Database クライアント ライブラリ](sql-database-elastic-database-client-library.md)を使用してシャードを管理します。 ライブラリには、[Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) と呼ばれる名前空間があり、これを使用すると、1 つのクエリと結果を使用して複数のシャードを照会できます。 この名前空間により、シャードのコレクションに対するクエリ抽象化が提供されます。 また、多数のシャードを照会したときのエラーを処理するための代替実行ポリシー (具体的には、部分的な結果) も提供されます。  




<!--HONumber=Jan17_HO4-->


