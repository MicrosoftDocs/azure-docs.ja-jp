---
title: "弾力性データベース ツールと行レベルのセキュリティを使用したマルチテナント アプリケーション"
description: "弾力性データベース ツールと行レベルのセキュリティを使用して、Azure SQL Database にマルチテナントのシャードをサポートする拡張性の高いデータ層を持つアプリケーションを作成する方法について説明します。"
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
documentationcenter: 
manager: jhubbard
author: tmullaney
ms.assetid: e72d3cfe-e9be-4326-b776-9c6d96c0a18e
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: thmullan;torsteng
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: 19afc13a1715890316b34ed1128b594105c53c69
ms.lasthandoff: 01/13/2017


---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>弾力性データベース ツールと行レベルのセキュリティを使用したマルチテナント アプリケーション
[弾力性データベース ツール](sql-database-elastic-scale-get-started.md)と [Row-Level Security (RLS)](https://msdn.microsoft.com/library/dn765131) は、Azure SQL Database を使用するマルチテナント アプリケーションのデータ層を柔軟かつ効率的にスケーリングできる強力な機能セットを提供します。 詳細については、「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md) 」をご覧ください。 

この記事では、これらのテクノロジを使用して、**ADO.NET SqlClient** と **Entity Framework** を使用してマルチテナントのシャードをサポートする拡張性の高いデータ層を持つアプリケーションを構築する方法について説明します。  

* **弾力性データベース ツール** を使用すると、.NET ライブラリと Azure のサービス テンプレートのセットを使用して、業界標準のシャーディング手法に従ってアプリケーションのデータ層をスケール アウトすることができます。 弾力性データベース クライアント ライブラリを使用してシャードを管理することは、一般的にシャーディングに関連するインフラストラクチャ タスクの多くを自動化および効率化するうえで役立ちます。 
* **Row-Level Security** を使用すると、クエリを実行しているテナントに属していない行をセキュリティ ポリシーを使って除外して、複数のテナントのデータを同じデータベースに格納できます。 アプリケーションではなくデータベース内に RLS を使ってアクセス ロジックを一元化することにより、アプリケーションのコードベースが大きくなった際にも保守が容易になり、エラーのリスクが小さくなります。 RLS では [Azure SQL Database (V12)](sql-database-technical-overview.md) が必要です。 

これらの機能を使いながら、複数のテナントのデータを同じデータベースに格納することにより、コストの削減と効率性の向上というメリットがアプリケーションにもたらされます。 マルチテナントのシャードではテナント間でリソースが等しく分配されることが保証されないため、より厳密なパフォーマンスの保証を必要とする "プレミアム" テナント向けに、分離された単一テナントのシャードを柔軟に提供することもできます。  

簡単に言うと、弾力性データベース クライアント ライブラリの [データ依存型ルーティング](sql-database-elastic-scale-data-dependent-routing.md) API は、シャーディング キー (一般に、"TenantId") を含む正しいシャード データベースにテナントを自動的に接続します。 接続された後、テナントは、データベース内の RLS セキュリティ ポリシーに基づき、その TenantId が含まれる行にのみアクセスできます。 どの行がどのテナントに属しているかを示すための TenantId 列はすべてのテーブルに含まれていると見なされます。 

![Blogging app architecture][1]

## <a name="download-the-sample-project"></a>サンプル プロジェクトのダウンロード
### <a name="prerequisites"></a>前提条件
* Visual Studio (2012 以降) を使用します。 
* 3 つの Azure SQL データベースを作成します。 
* サンプル プロジェクトをダウンロードします: [Elastic DB Tools for Azure SQL - Multi-Tenant Shards](http://go.microsoft.com/?linkid=9888163)
  * **Program.cs** 

このプロジェクトでは、 [Azure SQL の弾力性 DB ツールの Entity Framework 統合](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) に関するページで説明したプロジェクトを、マルチテナント シャード データベースのサポートを追加して拡張します。 上の図に示すように、4 つのテナントと&2; つのマルチテナント シャード データベースを含む、ブログや投稿を作成するための簡単なコンソール アプリケーションを構築します。 

アプリケーションをビルドし、実行します。 弾力性データベース ツールのシャード マップ マネージャーが起動され、次のテストが実行されます。 

1. Entity Framework と LINQ を使用して、新しいブログを作成し、各テナントのすべてのブログを表示します。
2. ADO.NET SqlClient を使用して、テナントのすべてのブログを表示します。
3. 不適切なテナントへのブログの挿入を試みて、エラーがスローされることを確認します。  

RLS はシャード データベースでまだ有効になっていないため、これらの各テストで問題点が明らかになります。テナントは、そのテナントに属さないブログを表示できます。また、アプリケーションは、不適切なテナントにブログを挿入できます。 この記事の残りの部分では、RLS によるテナントの分離を適用してこれらの問題を解決する方法について説明します。 次の&2; つの手順が含まれます。 

1. **アプリケーション層**: アプリケーション コードを変更し、接続を開いた後で、常に現在の TenantId を SESSION_CONTEXT に設定します。 サンプル プロジェクトでは、この手順は既に完了しています。 
2. **データ層**: 各シャード データベースで、SESSION_CONTEXT に格納されている TenantId に基づいて行をフィルター処理するための RLS セキュリティ ポリシーを作成します。 この手順は、それぞれのシャード データベースに対して実行する必要があります。そうでないと、マルチテナント シャード内の行がフィルター選択されません。 

## <a name="step-1-application-tier-set-tenantid-in-the-sessioncontext"></a>ステップ 1) アプリケーション層: TenantId を SESSION_CONTEXT に設定する
弾力性データベース クライアント ライブラリのデータ依存型ルーティング API を使用してシャード データベースに接続した後、アプリケーションは、RLS セキュリティ ポリシーを使って他のテナントに属している行を除外できるように、その接続を使用している TenantId をデータベースに通知する必要があります。 この情報を渡すために推奨される方法は、[SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx) に対象の接続の現在の TenantId を格納することです。 (注: 代わりに [CONTEXT_INFO](https://msdn.microsoft.com/library/ms180125.aspx) を使用することもできます、使用方法が簡単、既定で NULL を返す、キーと値のペアをサポートするなどの理由で、SESSION_CONTEXT の方が優れています。)

### <a name="entity-framework"></a>Entity Framework
Entity Framework を使用するアプリケーションの場合、最も簡単な方法は、「[EF DbContext を使用したデータ依存ルーティング](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext)」で説明されている ElasticScaleContext オーバーライドの中で SESSION_CONTEXT を設定する方法です。 データ依存型ルーティングで仲介された接続を返す前に、SESSION_CONTEXT の "TenantId" をその接続に指定されている shardingKey に設定する SqlCommand を作成して実行します。 この方法では、SESSION_CONTEXT を設定するコードを&1; 回記述するだけで済みます。 

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data dependent routing. This call will open a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call will fail for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

これで、ElasticScaleContext が呼び出されるたびに、SESSION_CONTEXT が指定された TenantId に自動的に設定されます。 

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
    {     
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
        foreach (var item in query)     
        {       
            Console.WriteLine(item.Name);     
        }   
    } 
}); 
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient
ADO.NET SqlClient を使用したアプリケーションの場合、推奨される方法は、接続を返す前に SESSION_CONTEXT の "TenantId" を正しい TenantId に自動的に設定する ShardMap.OpenConnectionForKey() を囲むラッパー関数を作成する方法です。 SESSION_CONTEXT が常に設定されることを確認するには、このラッパー関数を使用して接続を開くだけです。

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs will be listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="step-2-data-tier-create-row-level-security-policy"></a>ステップ 2) データ層: 行レベルのセキュリティ ポリシーを作成する
### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>各テナントがアクセスできる行をフィルター選択するセキュリティ ポリシーを作成する
アプリケーションでクエリを実行する前に SESSION_CONTEXT が現在の TenantId に設定されるようになったため、RLS セキュリティ ポリシーを使ってクエリをフィルター選択して、別の TenantId を持つ行を除外できます。  

RLS は T-SQL で実装されています。ユーザー定義の関数を使用してアクセス ロジックを定義し、セキュリティ ポリシーを使ってこの関数を任意の数のテーブルにバインドします。 このプロジェクトでは、関数を使用して、(他の SQL ユーザーではなく) アプリケーションがデータベースに接続されていること、および SESSION_CONTEXT に格納されている "TenantId" が特定の行の TenantId と一致することを確認しているのみです。 フィルター述語は、SELECT、UPDATE、および DELETE クエリに対するフィルターを通過するこれらの条件を満たす行を許可します。また、ブロック述語は、これらの条件に違反する行を INSERT または UPDATE から除外します。 SESSION_CONTEXT を設定していない場合は、NULL が返され、行を表示または挿入することはできません。 

RLS を有効にするには、Visual Studio (SSDT)、SSMS、またはプロジェクトに含まれる PowerShell スクリプトを使用して、すべてのシャードで次の T-SQL を実行します (また、 [弾力性データベース ジョブ](sql-database-elastic-jobs-overview.md)を使用している場合は、これを使用してすべてのシャードでこの T-SQL の実行を自動化できます)。 

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult          
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [!TIP]
> 数百個のテーブルに述語を追加する必要があるより複雑なプロジェクトの場合は、セキュリティ ポリシーを自動的に生成するヘルパー ストアド プロシージャを使用して、スキーマ内のすべてのテーブルに述語を追加できます。 [ヘルパー スクリプトを使用してすべてのテーブルに行レベルのセキュリティを適用する](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script)方法に関するブログ記事を参照してください。  
> 
> 

ここでサンプル アプリケーションを再び実行すると、そのテナントに属している行だけが表示されます。 さらに、アプリケーションは、シャード データベースに現在接続されているテナント以外のテナントに属する行を挿入できず、TenantId が異なる表示可能な行を更新できません。 アプリケーションがどちらかの操作を実行しようとすると、DbUpdateException が生成されます。

後で新しいテーブルを追加する場合は、セキュリティ ポリシーを変更して、新しいテーブルにフィルター述語とブロック述語を追加します。 

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>挿入用に TenantId を自動的に設定する既定の制約を追加する
各テーブルに既定の制約を設定し、行を挿入するときに SESSION_CONTEXT に現在格納されている値を持つ TenantId を自動的に設定できます。 次に例を示します。 

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

これで、アプリケーションが行を挿入するときに TenantId を指定する必要はありません。 

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> Entity Framework プロジェクトで既定の制約を使用する場合は、Entity Framework データ モデルに TenantId 列を含めないことをお勧めします。 これは、Entity Framework のクエリでは既定値が自動的に指定され、T-SQL で作成された SESSION_CONTEXT を使用する既定の制約が上書きされるためです。 たとえば、サンプル プロジェクトで既定の制約を使用するには、DataClasses.cs から TenantId を削除し (さらにパッケージ マネージャー コンソールで Add-Migration を実行し)、T-SQL を使ってフィールドがデータベース テーブルにのみ存在することを確認します。 これにより、データを挿入するときに Entity Framework によって不適切な既定値が自動的に指定されなくなります。 
> 
> 

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(省略可能) すべての行にアクセスするには "superuser" を有効にします。
一部のアプリケーションは、すべてのシャード上のすべてのテナント間でレポート作成を有効にするために、またはテナントの行をデータベース間で移行する分割 / マージ操作をシャードで実行するために、すべての行にアクセスできる "superuser" を作成できます。 これを可能にするには、新しい SQL ユーザー (この例では "superuser") を各シャード データベースに作成してください。 そして、このユーザーがすべての行にアクセスできるようにする新しいの述語関数を使用して、セキュリティ ポリシーを変更してください。

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### <a name="maintenance"></a>メンテナンス
* **新しいシャードの追加**: すべての新しいシャードで RLS を有効にするための T-SQL スクリプトを実行する必要があります。この操作を行わないと、これらのシャードに対するクエリはフィルター処理されません。
* **新しいテーブルの追加**: 新しいテーブルを作成するたびに、すべてのシャードのセキュリティ ポリシーにフィルター述語とブロック述語を追加する必要があります。この操作を行わないと、新しいテーブルに対するクエリはフィルター処理されません。 この操作は、DDL トリガーを使用して自動化できます。詳細については、[新しく作成したテーブルに自動的に行レベルのセキュリティを適用する方法に関するブログ記事](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx)を参照してください。

## <a name="summary"></a>概要
弾力性データベース ツールと行レベルのセキュリティを組み合わせると、アプリケーションのデータ層をスケール アウトして、マルチテナントのシャードと単一テナントのシャードの両方をサポートできます。 マルチテナントのシャードは、データをより効率的に格納するために使用できます (特に、少数のデータ行を保持するテナントが多数ある場合)。一方、単一テナントのシャードは、より厳密なパフォーマンス要件と分離要件を持つ "プレミアム" テナントをサポートするために使用できます。  詳細については、「[行レベルのセキュリティ](https://msdn.microsoft.com/library/dn765131)」をご覧ください。 

## <a name="additional-resources"></a>その他のリソース
* [Azure エラスティック プールの概要](sql-database-elastic-pool.md)
* [Azure SQL Database によるスケールアウト](sql-database-elastic-scale-introduction.md)
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Azure AD および OpenID Connect を使用したマルチテナント アプリでの認証](../guidance/guidance-multitenant-identity-authenticate.md)
* [Tailspin Surveys アプリケーション](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>質問と機能に関する要望
質問がある場合は、[SQL Database のフォーラム](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)に投稿してください。機能に関するご要望は、[SQL Database に関するフィードバック フォーラム](https://feedback.azure.com/forums/217321-sql-database/)にお寄せください。

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->



