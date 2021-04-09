---
title: Dapper でのエラスティック データベース クライアント ライブラリの使用
description: Dapper でのエラスティック データベース クライアント ライブラリの使用。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: d660e62ea293bd3cc377b95612cfaf41a9f1cd6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793366"
---
# <a name="using-the-elastic-database-client-library-with-dapper"></a>Dapper でのエラスティック データベース クライアント ライブラリの使用
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

このドキュメントは、Dapper を使用してアプリケーションを構築する開発者が、 [エラスティック データベース ツール](elastic-scale-introduction.md) を導入し、シャーディングを実装してデータ層をスケール アウトするアプリケーションを作成する場合に使用します。  このドキュメントでは、エラスティック データベース ツールと統合するために Dapper ベースのアプリケーションに加える必要がある変更点を示します。 ここでは、Dapper を使用してエラスティック データベース シャード管理とデータ依存ルーティングを構成する方法を重点的に説明します。 

**サンプル コード**:[Elastic database tools for Azure SQL Database - Dapper integration (Azure SQL Database 用エラスティック データベース ツール - Dapper の統合)](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f)。

**Dapper** と **DapperExtensions** を Azure SQL Database の Elastic Database クライアント ライブラリに統合することは容易です。 アプリケーションではデータ依存ルーティングを使用できます。そのためには、[クライアント ライブラリ](/previous-versions/azure/dn765902(v=azure.100))の [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) の呼び出しを使用するように新しい [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) オブジェクトを作成する操作と開く操作を変更します。 これにより、アプリケーションの変更が、新しい接続を作成して開く場所だけに制限されます。 

## <a name="dapper-overview"></a>Dapper の概要
**Dapper** はオブジェクト リレーショナル マッパーです。 Dapper は、アプリケーションの .NET オブジェクトをリレーショナル データベースに (またはその逆に) マップします。 サンプル コードの最初の部分は、エラスティック データベース クライアント ライブラリと Dapper ベースのアプリケーションを統合する方法を示します。 サンプル コードの 2 番目の部分は、Dapper と DapperExtensions の両方を使用する場合の統合方法を示します。  

Dapper のマッパー機能には、データベース接続に対する拡張メソッドが用意されています。このメソッドによって、データベースの実行または照会のための T-SQL ステートメントの送信が簡略化されます。 たとえば、Dapper を使用すると、**Execute** の呼び出しでの .NET オブジェクトと SQL ステートメントのパラメーター間のマッピングを容易に行うことができます。また、Dapper から **Query** の呼び出しを使用して、SQL クエリの結果を .NET オブジェクトに容易に組み込むことができます。 

DapperExtensions を使用する場合に SQL ステートメントを指定する必要はなくなりました。 データベース接続経由で使用する、**GetList** や **Insert** などの拡張メソッドでは SQL ステートメントがバックグラウンドで作成されます。

Dapper と DapperExtensions のもう 1 つの利点は、データベース接続の作成がアプリケーションによって制御されることです。 これにより、データベースに対するシャードレットのマッピングに基づいてデータベース接続を仲介するエラスティック データベース クライアント ライブラリとやり取りできるようになります。

Dapper アセンブリを入手するには、「 [Dapper dot net](https://www.nuget.org/packages/Dapper/)」をご覧ください。 Dapper の拡張機能については、「 [DapperExtensions](https://www.nuget.org/packages/DapperExtensions)」をご覧ください。

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>エラスティック データベース クライアント ライブラリの概要
Elastic Database ライブラリでは、*シャードレット* と呼ばれる、アプリケーション データのパーティションを定義して、データベースにマップし、*シャーディング キー* によって識別します。 任意の数のデータベースを使用して、シャードレットをそれらのデータベースに分散できます。 シャーディング キー値とデータベースとの間のマッピングは、ライブラリの API によって提供されるシャード マップによって格納されます。 この機能は、 **シャード マップの管理** と呼ばれます。 シャード マップは、シャーディング キーを格納する要求のデータベース接続用のためのブローカーとしても機能します。 この機能は、**データ依存ルーティング** と呼ばれます。

![シャード マップとデータ依存ルーティング][1]

シャード マップ マネージャーは、データベースでの同時シャードレット管理操作の実行中に発生する可能性があるシャードレット データの一貫性のないビューからユーザーを保護します。 これを実現するために、シャード マップは、ライブラリを使用して構築されたアプリケーションのデータベース接続を仲介します。 これにより、シャード管理操作がシャードレットに影響を与える可能性のあるときに、シャード マップ機能によってデータベース接続を自動的に強制終了できます。 

従来の方法で Dapper 用の接続を作成するのではなく、[OpenConnectionForKey](/previous-versions/azure/dn824099(v=azure.100))メソッドを使用する必要があります。 これにより、シャード間でのデータの移動時に、すべての検証が行われ、接続が適切に管理されます。

### <a name="requirements-for-dapper-integration"></a>Dapper の統合の要件
エラスティック データベース クライアント ライブラリと Dapper API の両方を使用する場合は、次のプロパティを維持することを想定します。

* **スケールアウト**:アプリケーションの容量要求に合わせて必要に応じてシャード化されたアプリケーションのデータ層のデータベースを追加または削除します。 
* **整合性**:アプリケーションはシャーディングを使用してスケールアウトされるため、データ依存ルーティングを実行する必要があります。 そのためには、ライブラリのデータ依存ルーティング機能を使用します。 具体的には、破損や誤ったクエリ結果を回避するために、シャード マップ マネージャーを通じて仲介された接続を提供して、検証と一貫性を維持します。 これにより、たとえば、Split/Merge API を使用して特定のシャードレットを別のシャードに移動中の場合は、そのシャードレットに対する接続が拒否または停止されます。
* **オブジェクト マッピング**:Dapper が提供するマッピングの利便性を保ち、アプリケーションと基になるデータベース構造でクラス間の変換を行います。 

次のセクションでは、**Dapper** と **DapperExtensions** に基づくこれらのアプリケーションの要件のガイダンスを示します。

## <a name="technical-guidance"></a>技術ガイダンス
### <a name="data-dependent-routing-with-dapper"></a>Dapper を使用したデータ依存ルーティング
Dapper では、通常、基になるデータベースへの接続を作成して開くのはアプリケーションです。 アプリケーションで型 T が指定されている場合、Dapper はクエリ結果を型 T の .NET コレクションとして返します。Dapper は、T-SQL の結果行から型 T のオブジェクトへのマッピングを実行します。同様に、Dapper は、データ操作言語 (DML) ステートメント用に .NET オブジェクトを SQL 値またはパラメーターにマップします。 Dapper は、ADO .NET SQL クライアント ライブラリの通常の [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) オブジェクトに対して拡張メソッドを使用してこの機能を提供します。 DDR 用の Elastic Scale API から返される SQL 接続も通常の [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) オブジェクトです。 これにより、クライアント ライブラリの DDR API から返される型を経由して Dapper の拡張機能を直接使用できます (この型も単純な SQL クライアント接続であるため)。

これらの手順によって、Dapper 用のエラスティック データベース クライアント ライブラリが仲介する接続を容易に使用できます。

次のコード例 (付属のサンプルからの抜粋) は、適切なシャードへの接続を仲介するために、アプリケーションがライブラリに対してシャーディング キーを提供するアプローチを示しています。   

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1,
                     connectionString: connStrBldr.ConnectionString,
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }
```

[OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) API を呼び出すと、SQL Client 接続を作成する既定の操作と開く既定の操作が置き換えられます。 [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) の呼び出しは、データ依存ルーティングに必要な次の引数を受け取ります。 

* データ依存ルーティング インターフェイスにアクセスするためのシャード マップ
* シャードレットを識別するためのシャーディング キー
* シャードに接続するための資格情報 (ユーザー名とパスワード)

シャード マップ オブジェクトにより、特定のシャーディング キーのシャードレットを保持するシャードへの接続が作成されます。 また、エラスティック データベース クライアント API は接続にタグ付けして、その一貫性を保証します。 [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) の呼び出しでは通常の SQL クライアント接続オブジェクトが返されるため、Dapper からの後続の **Execute** 拡張メソッドの呼び出しは Dapper の標準の手法に従います。

クエリの使用方法はまったく同じです。最初に、クライアント API から [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) を使用して接続を開きます。 次に、通常の Dapper 拡張メソッドを使用して、SQL クエリの結果を .NET オブジェクトにマップします。

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate ))
    {
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT *
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }
```

DDR 接続を含む **using** ブロックのスコープは、tenantId1 が保持されている 1 つのシャードに対する、そのブロック内のすべてのデータベース操作であることに注目してください。 このクエリが返すのは、現在のシャードに格納されているブログのみです。他のシャードに格納されているブログは返されません。 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Dapper と DapperExtensions を使用したデータ依存ルーティング
Dapper には、データベース アプリケーションの開発時にデータベースからさらなる利便性と抽象化を実現する追加の拡張機能のエコシステムが用意されています。 DapperExtensions はその一例です。 

アプリケーションで DapperExtensions を使用しても、データベース接続の作成と管理の方法は変わりません。 接続を開く操作は引き続きアプリケーションが担当し、通常の SQL クライアント接続オブジェクトが拡張メソッドによって使用されます。 既に説明したように、 [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) を利用できます。 次のコード サンプルに示すとおり、変更されたのは、ユーザーが T-SQL ステートメントを記述する必要がなくなった点のみです。

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }
```

クエリ用のコード サンプルを次に示します。 

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }
```

### <a name="handling-transient-faults"></a>一時的エラーの処理
Microsoft Patterns & Practices チームでは、「[Transient Fault Handling Application Block (一時的な障害処理アプリケーション ブロック)](/previous-versions/msp-n-p/hh680934(v=pandp.50))」を公開しています。これは、クラウドでの実行時によく発生する一時的なエラー状態をアプリケーション開発者が軽減する際に役立ちます。 詳細については、「[Perseverance, Secret of All Triumphs:Using the Transient Fault Handling Application Block (成功のための耐力と秘密: 一時的な障害処理アプリケーション ブロック)](/previous-versions/msp-n-p/dn440719(v=pandp.60))」を参照してください。

次のコード サンプルでは、一時的エラーのライブラリを使用して一時的エラーを防ぎます。 

```csharp
    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });
```

上記のコードの **SqlDatabaseUtils.SqlRetryPolicy** は **SqlDatabaseTransientErrorDetectionStrategy** として定義され、再試行回数が 10 回、再試行間の待機時間が 5 秒に設定されています。 トランザクションを使用する場合は、一時的エラーの発生時に再試行のスコープがトランザクションの先頭に戻ることをご確認ください。

## <a name="limitations"></a>制限事項
このドキュメントで説明した方法には、いくつかの制限事項があります。

* このドキュメントのサンプル コードには、複数のシャードにおけるスキーマの管理方法を示していません。
* 要求を受け取った場合、要求によって提供されたシャーディング キーで識別される 1 つのシャード内にそのすべてのデータベース処理が含まれると見なします。 ただし、この仮定が常に正しいとは限りません。たとえば、シャーディング キーを使用可能にできないことがあります。 これに対処するために、[MultiShardQuery クラス](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardexception)が Elastic Database クライアント ライブラリに含まれています。 このクラスは、複数のシャードに対してクエリを実行するための接続の抽象化を実装します。 Dapper と組み合わせた MultiShardQuery の使用方法については、このドキュメントの範囲を超えているため省略します。

## <a name="conclusion"></a>まとめ
Dapper と DapperExtensions を使用するアプリケーションは、Azure SQL Database のエラスティック データベース ツールのメリットを簡単に活用できます。 このドキュメントで説明した手順に従って、Elastic Database クライアント ライブラリの [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) の呼び出しを使用するように新しい [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) オブジェクトを作成する操作と開く操作を変更することにより、これらのアプリケーションでツールの機能をデータ依存ルーティングに利用できるようになります。 これにより、アプリケーションに対して必要な変更が、新しい接続を作成して開く場所だけに制限されます。 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-working-with-dapper/dapperimage1.png