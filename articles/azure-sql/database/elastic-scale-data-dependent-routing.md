---
title: データ依存ルーティング
description: データ依存ルーティング (Azure SQL Database のシャード化されたデータベースの機能) のために .NET アプリで ShardMapManager クラスを使う方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 60e8b4b21a9e62279cd0eccfabbbed680183e2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92787025"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-an-appropriate-database"></a>データ依存ルーティングを使用して、クエリを適切なデータベースにルーティングする
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

**データ依存ルーティング** は、クエリ内のデータを使用して、要求を適切なデータベースにルーティングできる機能です。 データ依存ルーティングは、シャード化されたデータベースを操作するときの基本的なパターンです。 特にシャーディング キーがクエリの一部でない場合は、要求コンテキストを使用して要求をルーティングすることもできます。 データ依存ルーティングを使用したアプリケーションで、特定のクエリまたはトランザクションがそれぞれアクセスするデータベースは、要求ごとに 1 つに制限されています。 Azure SQL Database エラスティック ツールでは、このルーティングは **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) クラスで実行します。

アプリケーションは、シャード化された環境の各種データ スライスに関連付けられた、さまざまな接続文字列または DB の場所を追跡する必要はありません。 代わりに [Shard Map Manager](elastic-scale-shard-map-management.md) が正しいデータベースへの接続処理を必要に応じて管理します。 通常、このキーは、データベース要求の基本的なパラメーターである、*customer_id*、*tenant_id*、*date_key*、または他の特定の識別子です。

詳細については、「[データ依存型ルーティングを使用した SQL Server のスケールアウト](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10))」を参照してください。

## <a name="download-the-client-library"></a>クライアント ライブラリのダウンロード

ダウンロードの対象:

* Java バージョンのライブラリは、[Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools) をご覧ください。
* .NET バージョンのライブラリは、[NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) をご覧ください。

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>データ依存ルーティング アプリケーションでの ShardMapManager の使用

アプリケーションは、ファクトリ呼び出し **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) を使用して、初期化中に **ShardMapManager** をインスタンス化する必要があります。 この例では、**ShardMapManager** と、これが含んでいる特定の **ShardMap** の両方が初期化されます。 また、GetSqlShardMapManager メソッドと GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap)、[.NET](/previous-versions/azure/dn824173(v=azure.100))) メソッドを使用しています。

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>シャード マップの取得には最小限のアクセス許可を持つ資格情報を使用する

アプリケーションでシャード マップ自体を操作しない場合は、ファクトリ メソッドで使用される資格情報には、**グローバル シャード マップ** データベースに対する読み取り専用アクセス許可を付与します。 これらの資格情報は、通常、シャード マップ マネージャーへの接続で使用される資格情報とは異なります。 「 [Elastic Database クライアント ライブラリへのアクセスに使用する資格情報](elastic-scale-manage-credentials.md)」も参照してください。

## <a name="call-the-openconnectionforkey-method"></a>OpenConnectionForKey メソッドを呼び出す

**ShardMap.OpenConnectionForKey メソッド** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) は、**key** パラメーターの値に基づいて、適切なデータベースへのコマンド発行の準備が整った接続を返します。 シャード情報は、**ShardMapManager** によってアプリケーションにキャッシュされるため、これらの要求では通常は、**グローバル シャード マップ** データベースに対するルックアップは行われません。

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* **key** パラメーターは、要求に対する適切なデータベースを特定するためのシャード マップへのルックアップ キーとして使用されます。
* **connectionString** は、必要な接続を行うためのユーザーの資格情報のみを渡すために使用されます。 この *connectionString* には、データベース名またはサーバー名は含まれません。メソッドがデータベースとサーバーを特定するために **ShardMap** を使用するためです。
* 分割やマージの操作の結果、シャード マップが変更されている可能性や、行が他のデータベースに移動している可能性のある環境では、**connectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) を **ConnectionOptions.Validate** に設定する必要があります。 この検証では、接続要求がアプリケーションに配信される前に、ターゲット データベースにあるローカルのシャード マップ (グローバル シャード マップではない) に対する簡単なクエリが実行されます。

ローカルのシャード マップの検証が失敗した (キャッシュが正しくないことが示された) 場合は、シャード マップ マネージャーがグローバル シャード マップに対してクエリを実行し、ルックアップの正しい値を新しく取得します。その後、キャッシュを更新してから適切なデータベース接続を取得して戻します。

**ConnectionOptions.None** を使用するのは、アプリケーションがオンラインの間にシャード マッピングの変更が想定されない場合のみです。 その場合、キャッシュされた値は常に正しいと見なすことができ、ターゲット データベースに対する今後のラウンドトリップ検証呼び出しを安全にスキップすることができます。 これによりデータベース トラフィックを軽減できます。 また、 **connectionOptions** は、一定期間内にシャーディングの変更が想定されるかどうかを示すために構成ファイルの値で設定することもできます。  

この例では、**customerShardMap** という名前のオブジェクト **ShardMap** を使用した、整数キー **CustomerID** の値を使用しています。  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

**OpenConnectionForKey** メソッドは、正しいデータベースに対し、既に開いている接続を新たに返します。 この方法で接続した場合も引き続き接続プーリングの利点を最大限に活用できます。

アプリケーションが非同期プログラミングを使用する場合は、**OpenConnectionForKeyAsync method** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) も使用できます。

## <a name="integrating-with-transient-fault-handling"></a>一時的な障害処理との統合

クラウドでのデータ アクセス アプリケーション開発のベスト プラクティスは、一時的な障害をアプリケーションで確実に捕捉し、エラーがスローされる前に操作を確実にリトライすることです。 クラウド アプリケーションにおける一時的な障害処理の詳細については、一時的な障害処理 ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling)、[.NET](/previous-versions/msp-n-p/dn440719(v=pandp.60))) に関するページをご覧ください。

一時的な障害処理は、データ依存ルーティングのパターンと自然に共存できます。 そのための主な要件は、データ依存ルーティング接続を取得した **using** ブロックを含めたデータ アクセス要求全体をリトライすることです。 前の例は次のように書き換えることができます。

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>例 - 一時的な障害処理機能を伴うデータ依存ルーティング

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

一時的な障害処理の実装に必要なパッケージは、エラスティック データベースのサンプル アプリケーションのビルド時に自動的にダウンロードされます。

## <a name="transactional-consistency"></a>トランザクションの整合性

トランザクションのプロパティは、シャードにとってローカルなすべての操作で保証されています。 たとえば、データ依存ルーティングを通じて送信されるトランザクションは、接続するターゲット シャードの範囲内で実行されます。 現時点では、複数の接続を 1 つのトランザクションに登録するために提供された機能はありません。したがって、複数のシャードにまたがる操作にもトランザクション上の保証はありません。

## <a name="next-steps"></a>次のステップ

シャードをデタッチまたは再アタッチする方法については、「[RecoveryManager クラスを使用したシャード マップに関する問題の解決](elastic-database-recovery-manager.md)」をご覧ください。

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]