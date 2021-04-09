---
title: RLS とエラスティック データベース ツールを使用したマルチテナント アプリ
description: 行レベルのセキュリティを備えた弾力性データベース ツールを使用して、拡張性の高いデータ層を持つアプリケーションを構築します。
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6d753a90f2a4cb19c9f3933d007fb3d378af6d81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793213"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>弾力性データベース ツールと行レベルのセキュリティを使用したマルチテナント アプリケーション
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[エラスティック データベース ツール](elastic-scale-get-started.md)と[行レベルのセキュリティ (RLS)][rls] を組み合わせることで、Azure SQL Database を使用するマルチテナント アプリケーションのデータ層のスケーリングが可能になります。 これらのテクノロジの連携によって、データ層のスケーラビリティを高めたアプリケーションを効率よく作成することができます。 データ層では、マルチテナント シャードがサポートされ、**ADO.NET SqlClient** または **Entity Framework** が使用されます。 詳細については、「[Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](./saas-tenancy-app-design-patterns.md)」をご覧ください。

- **エラスティック データベース ツール** を使用すると、開発者は .NET ライブラリと Azure のサービス テンプレートを使用することにより、標準的なシャーディング手法に従ってデータ層をスケールアウトすることができます。 [Elastic Database クライアント ライブラリ][s-d-elastic-database-client-library]を使用してシャードを管理することは、一般的にシャーディングに関連するインフラストラクチャ タスクの多くを自動化および効率化するうえで役立ちます。
- **行レベル セキュリティ** を使用すると、開発者は、同じデータベースに複数のテナントのデータを安全に格納することができます。 クエリを実行しているテナントに属さない行は、RLS セキュリティ ポリシーによって除外されます。 フィルターのロジックをデータベース内に集約することで、メンテナンスが簡素化され、セキュリティ エラーのリスクが軽減されます。 これをクライアント コードだけで行ってセキュリティを確保しようとするとリスクが伴います。

これらの機能を組み合わせて使用することで、アプリケーションは、複数のテナントのデータを同じシャード データベースに格納することができます。 テナントでデータベースを共有すると、テナントあたりのコストが小さくなります。 しかも同じアプリケーションで、専用のシングル テナント シャードを利用できる有料オプションを Premium テナント向けに提供することができます。 シングル テナントとして分離させることの利点の 1 つとして、より確実なパフォーマンス保証が可能になることが挙げられます。 シングル テナント データベースでは、他のテナントとリソースの奪い合いになることがありません。

達成目標は、エラスティック データベース クライアント ライブラリの[データ依存ルーティング](elastic-scale-data-dependent-routing.md) API を使用してそれぞれのテナントが正しいシャード データベースに自動的に接続することです。 指定されたテナントの特定の TenantId 値を含んでいるシャードは 1 つだけです。 TenantId は "*シャーディング キー*" です。 接続の確立後、データベース内にある RLS のセキュリティ ポリシーによって、指定されたテナントからのアクセスが、その TenantId を含んだデータ行に制限されます。

> [!NOTE]
> テナント ID は、複数の列で構成される場合があります。 この記事では便宜上、単一列の TenantId を前提として説明します。

![Blogging app architecture][1]

## <a name="download-the-sample-project"></a>サンプル プロジェクトのダウンロード

### <a name="prerequisites"></a>前提条件

- Visual Studio (2012 以降) を使用します。
- Azure SQL Database に 3 つのデータベースを作成する
- サンプル プロジェクトをダウンロードします:[Elastic DB Tools for Azure SQL - Multi-Tenant Shards](https://go.microsoft.com/?linkid=9888163)
  - **Program.cs**

このプロジェクトでは、 [Azure SQL の弾力性 DB ツールの Entity Framework 統合](elastic-scale-use-entity-framework-applications-visual-studio.md) に関するページで説明したプロジェクトを、マルチテナント シャード データベースのサポートを追加して拡張します。 このプロジェクトでは、ブログや記事を作成するための単純なコンソール アプリケーションを作成します。 プロジェクトには、4 つのテナントのほか、2 つのマルチテナント シャード データベースが含まれます。 この構成は、上の図に示されています。

アプリケーションをビルドして実行します。 この実行により、エラスティック データベース ツールのシャード マップ マネージャーが起動され、次のテストが実行されます。

1. Entity Framework と LINQ を使用して、新しいブログを作成し、各テナントのすべてのブログを表示します。
2. ADO.NET SqlClient を使用して、テナントのすべてのブログを表示します。
3. 不適切なテナントへのブログの挿入を試みて、エラーがスローされることを確認します。

RLS はシャード データベースでまだ有効になっていないため、これらの各テストで問題点が明らかになります。テナントは、そのテナントに属さないブログを表示できます。また、アプリケーションは、不適切なテナントにブログを挿入できます。 この記事の残りの部分では、RLS によるテナントの分離を適用してこれらの問題を解決する方法について説明します。 次の 2 つの手順があります。

1. **アプリケーション層**:接続を開いた後に常に現在の TenantId を SESSION\_CONTEXT に設定するようにアプリケーション コードを変更します。 サンプル プロジェクトでは、あらかじめそのように TenantId が設定されています。
2. **データ層**:各シャード データベースで、SESSION\_CONTEXT に格納されている TenantId に基づいて行をフィルター処理するための RLS セキュリティ ポリシーを作成します。 ポリシーは、それぞれのシャード データベースに対して作成する必要があります。そうでないと、マルチテナント シャード内の行がフィルター処理されません。

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1.アプリケーション層:TenantId を SESSION\_CONTEXT に設定する

まず、エラスティック データベース クライアント ライブラリのデータ依存ルーティング API を使用してシャード データベースに接続します。 その場合でも、接続に使用している TenantId をアプリケーションからデータベースに伝える必要があります。 RLS セキュリティ ポリシーは、他のテナントに属している除外すべき行を TenantId によって判別します。 接続の [SESSION\_CONTEXT](/sql/t-sql/functions/session-context-transact-sql) に現在の TenantId を格納します。

SESSION\_CONTEXT の代わりに [CONTEXT\_INFO](/sql/t-sql/functions/context-info-transact-sql) を使用してもかまいません。 ただし、どちらかと言えば、SESSION\_CONTEXT の使用をお勧めします。 SESSION\_CONTEXT の方が使いやすいためです。これは、既定で NULL を返すほか、キーと値のペアをサポートします。

### <a name="entity-framework"></a>Entity Framework

Entity Framework を使用するアプリケーションの場合、最も簡単な方法として、「\_EF DbContext を使用したデータ依存ルーティング[」で説明されている ElasticScaleContext オーバーライドの中で SESSION](elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext)CONTEXT を設定します。 SESSION\_CONTEXT の TenantId をその接続に指定されている shardingKey に設定する SqlCommand を作成して実行します。 その後、データ依存ルーティングで仲介された接続を返します。 この方法では、SESSION\_CONTEXT を設定するコードを 1 回記述するだけで済みます。

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
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

これで、ElasticScaleContext が呼び出されるたびに、SESSION\_CONTEXT には、指定された TenantId が自動的に設定されます。

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
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

ADO.NET SqlClient を使用するアプリケーションの場合、ShardMap.OpenConnectionForKey メソッドのラッパー関数を作成します。 SESSION\_CONTEXT 内の TenantId を自動的に最新の TenantId に設定した後で接続を返すようなラッパーを作成しましょう。 SESSION\_CONTEXT が常に設定されるようにするには、このラッパー関数を使用して接続を開くだけです。

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
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

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2.データ層:行レベルのセキュリティ ポリシーを作成する

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>各テナントがアクセスできる行をフィルター選択するセキュリティ ポリシーを作成する

アプリケーションでクエリを実行する前に SESSION\_CONTEXT に現在の TenantId が設定されるようになったため、RLS セキュリティ ポリシーを使ってクエリをフィルター処理して、別の TenantId を持つ行を除外できます。

RLS は Transact-SQL で実装されています。 ユーザー定義の関数ではアクセス ロジックを定義し、セキュリティ ポリシーではこの関数を任意の数のテーブルにバインドします。 このプロジェクトでは、次のことを行います。

1. この関数は、アプリケーションがデータベースに接続されていることと、SESSION\_CONTEXT に格納されている TenantId が特定の行の TenantId と一致することを確認します。
    - 接続されているのは、他の SQL ユーザーではなく、アプリケーションです。

2. FILTER 述語を使用すると、TenantId のフィルターに該当する行は、SELECT、UPDATE、DELETE のクエリにパススルーできます。
    - BLOCK 述語は、フィルターに該当しない行を INSERT または UPDATE の対象から除外します。
    - SESSION\_CONTEXT を設定していない場合は、関数から NULL が返され、行を表示または挿入することはできません。

すべてのシャードで RLS を有効にするには、Visual Studio (SSDT)、SSMS、またはプロジェクトに含まれる PowerShell スクリプトを使用して、次の T-SQL を実行します。 また、[Elastic Database ジョブ](./elastic-jobs-overview.md)を使用している場合は、すべてのシャードでこの T-SQL の実行を自動化できます。

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> 複雑なプロジェクトでは、何百ものテーブルに述語を追加することが必要になる場合がありますが、これは冗長になる可能性があります。 セキュリティ ポリシーを自動的に生成して、スキーマ内のすべてのテーブルに述語を追加するヘルパー ストアド プロシージャが存在します。 詳細については、[ヘルパー スクリプトを使用してすべてのテーブルに行レベルのセキュリティを適用する](https://techcommunity.microsoft.com/t5/sql-server/apply-row-level-security-to-all-tables-helper-script/ba-p/384360)方法に関するブログ記事を参照してください。

ここでサンプル アプリケーションを再び実行すると、そのテナントに属している行だけが表示されます。 さらに、アプリケーションは、シャード データベースに現在接続されているテナント以外のテナントに属する行を挿入できません。 また、アプリから見える行であっても TenantId は一切更新できません。 アプリケーションがどちらかの操作を実行しようとすると、DbUpdateException が発生します。

後で新しいテーブルを追加する場合は、セキュリティ ポリシーを変更して、新しいテーブルに FILTER 述語と BLOCK 述語を追加します。

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>挿入用に TenantId を自動的に設定する既定の制約を追加する

各テーブルに既定の制約を設定し、行の挿入時に SESSION\_CONTEXT に現在格納されている値を TenantId に自動的に設定できます。 例を次に示します。

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

これで、アプリケーションが行を挿入するときに TenantId を指定する必要はありません。

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Entity Framework プロジェクトで既定の制約を使用する場合は、Entity Framework データ モデルに TenantId 列を "*含めない*" ことをお勧めします。 これは、Entity Framework のクエリでは既定値が自動的に指定され、T-SQL で作成された SESSION\_CONTEXT を使用する既定の制約がオーバーライドされるためです。
> たとえば、サンプル プロジェクトで既定の制約を使用するには、DataClasses.cs から TenantId を削除し (さらにパッケージ マネージャー コンソールで Add-Migration を実行し)、T-SQL を使ってフィールドがデータベース テーブルにのみ存在することを確認します。 これにより、データを挿入するときに Entity Framework によって不適切な既定値が自動的に指定されなくなります。

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(省略可能) "*スーパーユーザー*" がすべての行にアクセスできるようにする

一部のアプリケーションでは、すべての行にアクセスできる "*スーパーユーザー*" の作成が必要となる場合があります。 スーパーユーザーであれば、すべてのシャード上のテナントの境界を越えたレポート作成が可能です。 また、データベース間のテナント行の移動を伴う分割/マージ操作をシャードに対して実行することも、スーパーユーザーであれば可能です。

スーパーユーザーを有効にするには、各シャード データベースに新しい SQL ユーザー (この例では `superuser`) を作成します。 その後、このユーザーがすべての行にアクセスできるようにする新しいの述語関数を使用して、セキュリティ ポリシーを変更します。 そのような関数の例を次に示します。

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>メンテナンス

- **新しいシャードの追加**:すべての新しいシャードで RLS を有効にするための T-SQL スクリプトを実行します。この操作を行わないと、これらのシャードに対するクエリはフィルター処理されません。
- **新しいテーブルの追加**:新しいテーブルを作成するたびに、すべてのシャードのセキュリティ ポリシーに FILTER 述語と BLOCK 述語を追加します。 そうしないと、新しいテーブルに対するクエリはフィルター処理されません。 この追加は、DDL トリガーを使用して自動化できます。詳細については、[新しく作成したテーブルに自動的に行レベルのセキュリティを適用する方法に関するブログ記事](https://techcommunity.microsoft.com/t5/SQL-Server/Apply-Row-Level-Security-automatically-to-newly-created-tables/ba-p/384393)を参照してください。

## <a name="summary"></a>まとめ

弾力性データベース ツールと行レベルのセキュリティを組み合わせると、アプリケーションのデータ層をスケール アウトして、マルチテナントのシャードと単一テナントのシャードの両方をサポートできます。 マルチテナントのシャードは、データをより効率的に格納するために使用できます。 その効果は、多数のテナントにデータが数行しかない場合に発揮されます。 より厳しいパフォーマンス要件と分離要件を持つプレミアム テナントには、シングル テナント シャードで対応できます。 詳細については、「[行レベルのセキュリティ][rls]」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

- [Azure エラスティック プールの概要](elastic-pool-overview.md)
- [Azure SQL Database によるスケールアウト](elastic-scale-introduction.md)
- [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](./saas-tenancy-app-design-patterns.md)
- [Azure AD および OpenID Connect を使用したマルチテナント アプリでの認証](/azure/architecture/multitenant-identity/authenticate)
- [Tailspin Surveys アプリケーション](/azure/architecture/multitenant-identity/tailspin)

## <a name="questions-and-feature-requests"></a>質問と機能に関する要望

ご質問がある場合は、[SQL Database に関する Microsoft Q&A 質問ページ](/answers/topics/azure-sql-database.html)からお問い合わせください。 また、機能に関するご要望は、[SQL Database フィードバック フォーラム](https://feedback.azure.com/forums/217321-sql-database/)までお寄せください。

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: /sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]:elastic-database-client-library.md