---
title: バッチ処理を使用してアプリケーションのパフォーマンスを強化する方法
description: このトピックでは、データベースの操作をバッチ処理で行うことによって、Azure SQL Database アプリケーションと Azure SQL Managed Instance アプリケーションの速度とスケーラビリティが大幅に向上することを実証します。 紹介しているバッチ処理手法はすべての SQL データベースにたいして機能しますが、この記事では Azure に焦点を絞ります。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 07334d62cee94be8b5b8dd6188c1d6354c4d584b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792601"
---
# <a name="how-to-use-batching-to-improve-azure-sql-database-and-azure-sql-managed-instance-application-performance"></a>バッチ処理を使用して Azure SQL Database アプリケーションと Azure SQL Managed Instance アプリケーションのパフォーマンスを強化する方法
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と Azure SQL Managed Instance に対する操作は、バッチ処理で行うことによりアプリケーションのパフォーマンスとスケーラビリティを大幅に高めることができます。 その利点を理解するために、Azure SQL Database または Azure SQL Managed Instance のデータベースに対する要求を逐次処理で行った場合とバッチ処理で行った場合とを比較するサンプル テストの結果をこの記事の冒頭でいくつか取り上げます。 その後、Azure アプリケーションでバッチ処理を正しく使用するための手法、シナリオ、考慮事項について説明します。

## <a name="why-is-batching-important-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database と Azure SQL Managed Instance でバッチ処理が重要な理由

リモート サービスの呼び出しをバッチ処理で行う手法は、パフォーマンスとスケーラビリティを高める戦略としてよく知られています。 リモート サービスとの対話には、シリアル化、ネットワーク転送、逆シリアル化など固定的な処理コストが発生します。 ばらばらに存在する多数のトランザクションを 1 つのバッチにまとめることで、こうしたコストを最小限に抑えることができます。

この記事では、バッチ処理のさまざまな方法とシナリオを紹介します。 これらの戦略は、SQL Server を使用するオンプレミス アプリケーションでも重要ですが、Azure SQL Database と Azure SQL Managed Instance におけるバッチ処理の使用を強調する理由はいくつかあります。

* Azure SQL Database と Azure SQL Managed Instance にアクセスするときの方が、ネットワーク待機時間が大きいことが考えられます。特に、アクセスする Azure SQL Database または Azure SQL Managed Instance が同じ Microsoft Azure データセンターに存在しない場合、その傾向が強くなります。
* Azure SQL Database と Azure SQL Managed Instance のマルチテナント特性は、データ アクセス層の効率が、データベース全体のスケーラビリティと密接に関係することを意味しています。 設定されているクォータを使用量が超えた場合の対応として、Azure SQL Database と Azure SQL Managed Instance は、スループットを抑えたり、スロットル例外を発生させたりすることができます。 バッチ処理などによって効率を向上させると、こうした制限の範囲内で行える作業が増えます。
* 複数のデータベース (シャーディング) を使ったアーキテクチャでもバッチ処理は有効です。 この場合も、それぞれのデータベース ユニットとのやり取りにおける効率の良さが、全体的なスケーラビリティの重要な要因となります。

Azure SQL Database または Azure SQL Managed Instance を使用する利点の 1 つは、データベースのホストとなるサーバーを管理する必要がないということです。 しかし、このように管理されたインフラストラクチャの存在は、データベースの最適化に対するアプローチも変えなければならないことを意味します。 この場合、データベース ハードウェアやネットワーク インフラストラクチャの強化に頼ることはできません。 これらの環境は Microsoft Azure によって制御されます。 利用者側で制御できる主な領域は、アプリケーションと Azure SQL Database または Azure SQL Managed とのやり取りです。 バッチ処理は、そうした最適化手法の一つとなります。

この記事の冒頭では、Azure SQL Database または Azure SQL Managed を使った .NET アプリケーション用のさまざまなバッチ処理手法について検討します。 最後の 2 つのセクションでは、バッチ処理のガイドラインとシナリオを取り上げます。

## <a name="batching-strategies"></a>バッチ処理の戦略

### <a name="note-about-timing-results-in-this-article"></a>この記事に記載されている時間測定の結果について

> [!NOTE]
> 結果はベンチマークではなく、**相対的なパフォーマンス** を示すことを意図したものです。 計時結果は、10 回以上のテスト ランの平均値に基づいています。 空のテーブルへの挿入操作を計測対象としています。 これらのテストは、V12 未満で計測しました。V12 データベースで新しい [DTU サービス レベル](database/service-tiers-dtu.md)または[仮想コア サービス レベル](database/service-tiers-vcore.md)を使って計測した場合のスループットとは必ずしも対応しません。 それでもバッチ処理手法の相対的なメリットに大きな違いはないと考えられます。

### <a name="transactions"></a>トランザクション

バッチ処理を検証するための足掛かりとして、トランザクションに触れるのは奇妙に思えるかもしれません。 しかし、クライアント側でトランザクションを使用することにより、パフォーマンス向上につながるバッチ処理の効果がサーバー側に生まれます。 トランザクションはわずか数行のコードで追加できるので、シーケンシャルな操作のパフォーマンスを簡単に高めることができます。

次の C# コードには、単純なテーブルに対する一連の挿入操作と更新操作が記述されています。

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```

次の ADO.NET コードは、以上の操作を連続実行します。

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

このコードを最適化するにはどうすればよいでしょうか。一番の方法は、クライアント側でこれらの操作をバッチ処理する何らかの機構を実装することです。 このコードのパフォーマンスを高める簡単な方法があります。それは単純に一連の呼び出しをトランザクションの中に投入することです。 以下に示したのは、先ほどと同じ操作をトランザクションで行うコードです。

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

実際には、両方の例にトランザクションが使われています。 1 つ目の例では、個々の呼び出しがそれぞれ暗黙的なトランザクションとなっています。 2 つ目の例では、トランザクションを明示的に指定して、その中にすべての呼び出しを投入しています。 [write-ahead トランザクション ログ](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#WAL)のドキュメントによれば、トランザクションがコミットされたときにログ レコードがディスクにフラッシュされます。 したがって、より多くの呼び出しをトランザクションに含めることで、その分、トランザクションがコミットされるまでの間、トランザクション ログへの書き込みを先送りすることができます。 実質的に、サーバーのトランザクション ログに対する書き込みに関して、バッチ処理を有効にしたことになります。

以下の表は、いくつかのアドホック テストの結果です。 同じ連続挿入の実行結果をトランザクションを使った場合と使わなかった場合とで比較しています。 総合的な評価を行うために、テストは 2 種類行いました。1 つ目のテストは、ノート PC からリモートで Microsoft Azure 内のデータベースに対して実行しています。 2 つ目のテストは、同じ Microsoft Azure データセンター (米国西部) に存在するクラウド サービスとデータベースから実行しています。 以下の表は、トランザクションを使った場合と使わなかった場合の連続挿入にかかる時間をミリ秒単位で示しています。

**オンプレミスから Azure へ**:

| 操作 | トランザクションなし (ミリ秒) | トランザクションあり (ミリ秒) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure から Azure へ (同じデータ センター)** :

| 操作 | トランザクションなし (ミリ秒) | トランザクションあり (ミリ秒) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> 結果はベンチマークではありません。 「[この記事に記載されている時間測定の結果について](#note-about-timing-results-in-this-article)」をご覧ください。

前掲のテスト結果によれば、単一の操作をトランザクションに投入した場合、実際にはパフォーマンスが低下しています。 しかし、1 つのトランザクションに含める操作の数を増やすにつれ、パフォーマンスの向上が顕著に見られるようになりました。 すべての操作を同じ Microsoft Azure データセンター内で実行したときにも、パフォーマンスの違いが顕著に表れています。 Microsoft Azure データセンターの外部から Azure SQL Database または Azure SQL Managed を使用した場合に生じる待機時間の増加から、トランザクションを使用することで得られるパフォーマンスの向上が見えにくくなっています。

トランザクションを使うことによってパフォーマンスは向上しますが、それでも [トランザクションと接続のベスト プラクティス](/previous-versions/sql/sql-server-2008-r2/ms187484(v=sql.105))には従ってください。 トランザクションはできるだけ短く保ち、作業が完了したらデータベース接続を閉じるようにしてください。 前の例のように using ステートメントを使うことで、後続のコード ブロックの完了時に接続が確実にクローズされます。

先ほどの例からわかるように、ローカル トランザクションは、2 つの行で任意の ADO.NET コードに追加することができます。 挿入、更新、削除の操作を連続して行うコードは、トランザクションを導入することで手軽にパフォーマンスを高めることができます。 しかし処理速度を最大限に高めるために、クライアント側のバッチ処理 (テーブル値パラメーターなど) を活かしたコードの改善を検討してください。

ADO.NET におけるトランザクションの詳細については、 [ADO.NET のローカル トランザクション](/dotnet/framework/data/adonet/local-transactions)に関するページを参照してください。

### <a name="table-valued-parameters"></a>テーブル値パラメーター

テーブル値パラメーターは、Transact-SQL のステートメント、ストアド プロシージャ、関数の中で、ユーザー定義テーブル型をパラメーターとして使用する際に使用します。 これはクライアント側のバッチ処理手法で、テーブル値パラメーターに複数行のデータを含めて送信することができます。 テーブル値パラメーターを使用するにはまず、テーブル型を定義します。 次の Transact-SQL ステートメントは、 **MyTableType** という名前のテーブル型を作成しています。

```sql
    CREATE TYPE MyTableType AS TABLE
    ( mytext TEXT,
      num INT );
```

このテーブル型とまったく同じ名前と型を持つ **DataTable** をコード内で作成します。 テキスト クエリまたはストアド プロシージャを呼び出す際にパラメーターとして、この **DataTable** を渡します。 この手法を使った例を次に示します。

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

この例では、テーブル値パラメーター **\@TestTvp** からの行を **SqlCommand** オブジェクトで挿入しています。 このパラメーターに、先ほど作成した **DataTable** オブジェクトを **SqlCommand.Parameters.Add** メソッドで割り当てます。 これらの挿入操作を 1 回の呼び出しでバッチ処理すれば、挿入操作を逐次的に実行した場合と比べ、大幅にパフォーマンスを高めることができます。

先ほどの例をさらに改良するには、テキスト ベースのコマンドをストアド プロシージャに置き換えます。 次の Transact-SQL コマンドでは、 **SimpleTestTableType** テーブル値パラメーターを引数として受け取るストアド プロシージャを作成しています。

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows]
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num)
SELECT mytext, num FROM @TestTvp
END
GO
```

そのうえで、先ほどのコード例の **SqlCommand** オブジェクトの宣言を次のように変更します。

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

テーブル値パラメーターのパフォーマンスは、他のバッチ処理手法と同等か、優れている場合がほとんどです。 他の方法に比べて柔軟性が高いため、テーブル値パラメーターは広く利用されています。 たとえば、SQL 一括コピーなど、他の手法では、新しい行の挿入しか認められていません。 一方、テーブル値パラメーターを使用すると、どの行が更新で、どの行が挿入であるかを、ストアド プロシージャのロジックを使って調べることができます。 このテーブル型に変更を加え、行ごとの操作の種類 (挿入、更新、削除) を示す "Operation" 列を追加することもできます。

以下の表は、テーブル値パラメーターの使用に関するアドホック テストの結果です (単位はミリ秒)。

| 操作 | オンプレミスから Azure へ (ミリ秒) | 同じ Azure データセンター (ミリ秒) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> 結果はベンチマークではありません。 「[この記事に記載されている時間測定の結果について](#note-about-timing-results-in-this-article)」をご覧ください。

バッチ処理によるパフォーマンスの上昇が一目で確認できます。 1,000 件の操作を対象にした冒頭の逐次処理テストでは、データセンター外で 129 秒、データセンター内で 21 秒かかっていました。 それに対し、テーブル値パラメーターを使用した 1,000 件の操作には、データセンター外で 2.6 秒、データセンター内で 0.4 秒しかかかっていません。

テーブル値パラメーターの詳細については、 [テーブル値パラメーター](/sql/relational-databases/tables/use-table-valued-parameters-database-engine)に関するページを参照してください。

### <a name="sql-bulk-copy"></a>SQL 一括コピー

データベースに大量のデータを挿入する手段としては、SQL 一括コピーを使った方法もあります。 .NET アプリケーションから **SqlBulkCopy** クラスを使用して挿入操作を一括実行することができます。 **SqlBulkCopy** の機能は、コマンドライン ツールの **Bcp.exe** や Transact-SQL ステートメントの **BULK INSERT** に似ています。 次のコード例では、コピー元テーブル **DataTable** からコピー先テーブル MyTable に行を一括コピーしています。

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

テーブル値パラメーターよりも一括コピーの方が好ましいケースもいくつかあります。 [テーブル値パラメーター](/sql/relational-databases/tables/use-table-valued-parameters-database-engine)に関する記事でテーブル値パラメーターと BULK INSERT 操作の比較表を参照してください。

次の表は、 **SqlBulkCopy** を使ったバッチ処理のパフォーマンスを示すアドホック テストの結果です (単位はミリ秒)。

| 操作 | オンプレミスから Azure へ (ミリ秒) | 同じ Azure データセンター (ミリ秒) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> 結果はベンチマークではありません。 「[この記事に記載されている時間測定の結果について](#note-about-timing-results-in-this-article)」をご覧ください。

バッチ サイズが小さいときは、テーブル値パラメーターの方が **SqlBulkCopy** クラスよりも優れています。 ところが、1,000 行と 10,000 行のテストでは、テーブル値パラメーターより **SqlBulkCopy** の方が 12～31% 高速に実行されています。 特にバッチ処理を使わずに実行した場合と比べれば、テーブル値パラメーターと同様、 **SqlBulkCopy** も一括挿入の有効な手段となります。

ADO.NET での一括コピーの詳細については、[一括コピー操作](/dotnet/framework/data/adonet/sql/bulk-copy-operations-in-sql-server)に関するページを参照してください。

### <a name="multiple-row-parameterized-insert-statements"></a>複数行パラメーター化 INSERT ステートメント

バッチ サイズが小さいときは、複数行を挿入する包括的なパラメーター化 INSERT ステートメントを作成するのも一つの方法です。 その手法を次のコード例に示します。

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

この例の意図は基本的な概念の紹介です。 現実には、必要なエンティティをループで処理しながら、クエリ文字列とコマンド パラメーターを同時に構築するのが一般的です。 追加できるクエリ パラメーターの上限は 2,100 個です。そのため、この方法で処理できる行の総数には制限があります。

次の表は、このタイプの挿入ステートメントのパフォーマンスを示すアドホック テストの結果です (単位はミリ秒)。

| 操作 | テーブル値パラメーター (ミリ秒) | 単一ステートメントでの挿入 (ミリ秒) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> 結果はベンチマークではありません。 「[この記事に記載されている時間測定の結果について](#note-about-timing-results-in-this-article)」をご覧ください。

バッチ サイズが 100 行未満である場合は、このアプローチの方がわずかに高速です。 改善の幅は小さいものの、アプリケーションで想定される状況によっては、この手法で高い効果が期待できる場合もあります。

### <a name="dataadapter"></a>DataAdapter

**DataAdapter** クラスを使用すると、**DataSet** オブジェクトに変更を加えたうえで、その変更を各種の操作 (INSERT、UPDATE、DELETE) として送信することができます。 この方法で **DataAdapter** を使用する場合、それぞれの操作について個別に呼び出しが行われることに注意してください。 パフォーマンスを強化するためには、 **UpdateBatchSize** プロパティを使用して、一回のバッチで処理する操作の数を指定します。 詳細については、 [DataAdapter を使用したバッチ操作の実行](/dotnet/framework/data/adonet/performing-batch-operations-using-dataadapters)に関するページを参照してください。

### <a name="entity-framework"></a>Entity Framework

[Entity Framework Core](/ef/efcore-and-ef6/#saving-data) ではバッチ処理がサポートされます。

### <a name="xml"></a>XML

片手落ちにならないように、バッチ処理の戦略としての XML についても取り上げたいと思います。 ただし XML の使用には、他の手法に勝るメリットはなく、むしろ、デメリットがいくつかあります。 このアプローチは、テーブル値パラメーターと似ていますが、ストアド プロシージャに渡すのはユーザー定義テーブルではなく XML ファイルまたは XML 文字列です。 ストアド プロシージャが、そうして受け取ったコマンドを解析します。

このアプローチにはいくつかのデメリットがあります。

* XML の操作は煩雑でエラーが生じやすい。
* データベースで XML を解析する作業は CPU に負担がかかる。
* テーブル値パラメーターよりも低速である場合が多い。

以上の理由から、バッチ クエリに XML を使用することはお勧めできません。

## <a name="batching-considerations"></a>バッチ処理の考慮事項

Azure SQL Database アプリケーションと Azure SQL Managed Instance アプリケーションでバッチ処理を使用する場合の指針を以降のセクションで詳しく取り上げます。

### <a name="tradeoffs"></a>トレードオフ

アーキテクチャによっては、パフォーマンスと回復性のトレードオフがバッチ処理に伴うこともあります。 たとえば、使用中のロールが不意にダウンしたとします。 失ったデータが 1 行である場合、バッチ処理の対象となる未送信の行が大量に失われるより、その影響は小さくなります。 行をバッファーに蓄積してから特定の時間枠でデータベースに送信した方がリスクが大きいのです。

このトレードオフを踏まえて、バッチ処理の対象にする操作の種類を判断してください。 重要性の低いデータの方が、バッチ処理は積極的に行うことができます (バッチ サイズを大きくする、時間枠を長くする)。

### <a name="batch-size"></a>バッチ サイズ

私たちが行ったテストでは、大きなバッチを小さなまとまりに分割するメリットはほとんどありませんでした。 実際、このように細かく分割した場合、1 つの大きなバッチで送信するときに比べ、パフォーマンスが低下するケースも往々にしてありました。 たとえば、挿入する行が 1,000 件ある状況を考えてみましょう。 次の表は、テーブル値パラメーターを使って 1,000 行のデータを挿入するとき、バッチ サイズを小さく分割していったときにかかる時間を示しています。

| バッチ サイズ | イテレーション | テーブル値パラメーター (ミリ秒) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> 結果はベンチマークではありません。 「[この記事に記載されている時間測定の結果について](#note-about-timing-results-in-this-article)」をご覧ください。

最もパフォーマンスが高いのは、1,000 行を一度に送信したときであることがわかります。 ここには示していませんが、他のテストで、10,000 行を 2 つの 5,000 行ずつのバッチに分割したとき、わずかながらパフォーマンスが向上したケースもありました。 ただし、これらのテストに用いたテーブルのスキーマは比較的単純であるため、調査結果を検証するためには、実際のデータとバッチ サイズに基づいてテストを実行する必要があります。

もう 1 つ考慮すべき要因は、バッチ全体が大きくなりすぎると、Azure SQL Database または Azure SQL Managed Instance が処理を抑制し始め、バッチのコミットを拒否する可能性がある点です。 最善の結果を得るためには、実際のシナリオに基づいてテストし、理想的なバッチ サイズが存在するかどうかを判断してください。 バッチ サイズを実行時に構成できるようにし、パフォーマンスやエラーに応じてすばやく調整できるようにします。

最終的には、バッチのサイズとバッチ処理に伴うリスクとのバランスを図ることになります。 一過性のエラーやロールの障害が発生する場合は、操作を再試行することによる影響や、まとまったデータが失われることの重大性を考慮に入れてください。

### <a name="parallel-processing"></a>並列処理

バッチ サイズを小さくしながらも、複数のスレッドを使ってその処理を実行した場合はどうなるのでしょうか。 今回のテストの範囲では、やはり、バッチ サイズを小さくしてマルチスレッドで処理した場合、概して大きなバッチ サイズのまま実行したときよりもパフォーマンスが低くなりました。 以下のテストは、1,000 行を 1 回または複数回のバッチに分割し、並列処理で挿入したものです。 同時に処理するバッチを増やしていったとき、パフォーマンスが実際に低下するようすを示しています。

| バッチ サイズ [反復回数] | 2 スレッド (ミリ秒) | 4 スレッド (ミリ秒) | 6 スレッド (ミリ秒) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> 結果はベンチマークではありません。 「[この記事に記載されている時間測定の結果について](#note-about-timing-results-in-this-article)」をご覧ください。

並列処理に起因するパフォーマンスの低下には、いくつかの理由が考えられます。

* ネットワーク呼び出しが 1 回で済まず、同時に複数回発生する。
* 1 つのテーブルに対して複数の操作が実行されるために競合やブロックが生じやすい。
* マルチスレッド化に関連したオーバーヘッドが伴う。
* 並列処理のメリットよりも複数の接続を開くコストの方が大きい。

操作対象のテーブルやデータベースが複数存在する場合、並列処理によって多少パフォーマンスが向上する可能性はあります。 そのような場合は、データベースのシャーディングやフェデレーションが候補となります。 シャーディングでは、複数のデータベースを使用し、個々のデータベースに異なるデータをルーティングします。 小さなバッチをそれぞれ異なるデータベースに送れば、並列処理によって操作の効率が高まる可能性はあります。 しかし、解決策としてデータベース シャーディングを用いることが正当性の根拠になるほどのパフォーマンス向上を見込むことはできません。

設計によっては、バッチ サイズを小さくしたうえで並列実行することにより、負荷のかかったシステムにおける要求のスループットが向上する場合があります。 このケースでは、1 つの大きなバッチを処理した方が速いとしても、複数のバッチを並列に処理した方が効率的である可能性があります。

並列実行を使用する場合、最大数のワーカー スレッドを使うべきかどうか、よく検討してください。 スレッド数は少ない方が、競合が少なく実行時間が短い場合もあります。 また、対象データベースの接続とトランザクションの両方に関して、新たにかかる負荷も考慮する必要があります。

### <a name="related-performance-factors"></a>関連するパフォーマンスの要因

データベースのパフォーマンスに関する一般的な指針は、バッチ処理にも当てはまります。 たとえば、大きなプライマリ キーや多数の非クラスター化インデックスが存在するテーブルでは、挿入操作のパフォーマンスが低下します。

テーブル値パラメーターでストアド プロシージャを使っている場合、その冒頭で **SET NOCOUNT ON** をコマンドを使用してください。 通常、プロシージャで処理された行数が返されますが、このステートメントを記述することで、その出力を抑制することができます。 ただし、テストした限りでは、 **SET NOCOUNT ON** を使ったからといって、パフォーマンスが向上することも、低下することもありませんでした。 テストに使ったストアド プロシージャは、テーブル値パラメーターからの単一の **INSERT** コマンドを含んだ単純なものでした。 もっと複雑なストアド プロシージャであれば、このステートメントが有利に作用する可能性があります。 とはいえ、 **SET NOCOUNT ON** をストアド プロシージャに追加するだけで、自動的にパフォーマンスが向上するとは考えないでください。 その効果を理解するためには、 **SET NOCOUNT ON** ステートメントを記述した場合とそうでない場合とでストアド プロシージャをテストする必要があります。

## <a name="batching-scenarios"></a>バッチ処理のシナリオ

以降のセクションでは、アプリケーションのシナリオを 3 つ想定して、テーブル値パラメーターの使い方を説明します。 1 つ目のシナリオでは、バッファリングとバッチ処理の連携方法を紹介しています。 2 つ目は、マスター/詳細の操作を 1 回のストアド プロシージャ呼び出しで実行することによってパフォーマンスを高めるシナリオです。 最後のシナリオでは、"UPSERT" 操作におけるテーブル値パラメーターの使い方を紹介します。

### <a name="buffering"></a>Buffering

バッチ処理には明確な候補となる状況がいくつか存在しますが、処理の先送りによってバッチ処理を有効活用できるケースは少なくありません。 しかし処理を先送りすれば、予期しない障害が発生した場合にデータが失われる危険性もそれだけ大きくなります。 リスクを把握し、その影響を考慮することが大切です。

たとえば、個々のユーザーのナビゲーション履歴を追跡する Web アプリケーションがあるとします。 ページ要求があるたびに、アプリケーションがデータベースを呼び出し、ユーザーのページ ビューを記録します。 しかし、ユーザーのナビゲーション アクティビティをバッファリングし、そのデータをまとめてデータベースに送れば、パフォーマンスとスケーラビリティを高めることができます。 データベースの更新は、経過時間やバッファー サイズ、またはその両方によってトリガーすることができます。 たとえば、20 秒経過したときや、バッファーに格納された項目数が 1,000 個に達したときにバッチが処理されるようなルールを指定することができます。

以下のコード例は、監視クラスによって生成され、バッファーに格納されたイベントを [Reactive Extensions (Rx)](/previous-versions/dotnet/reactive-extensions/hh242985(v=vs.103)) を使って処理しています。 バッファーがいっぱいになるか指定した時間が経過したら、テーブル値パラメーターを使って、ユーザー データのバッチをデータベースに送信します。

このユーザー ナビゲーションの詳細をモデル化したものが次の NavHistoryData クラスです。 ユーザー ID、アクセス URL、アクセス時刻など基本的な情報は、このクラスによって保持されます。

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

データベースに格納するユーザー ナビゲーション データをバッファリングするのは、NavHistoryDataMonitor クラスの役割です。 このクラスには、RecordUserNavigationEntry というメソッドがあります。戻り値のないメソッドであり、その応答は **OnAdded** イベントを発生させることによって行います。 Rx を使用し、このイベントに基づいて、監視可能なコレクションを作成するコンストラクターのロジックを示したのが以下のコードです。 この監視可能なコレクションを Buffer メソッドでサブスクライブします。 このメソッドのオーバーロードを使用して、バッファーへの送信間隔を 20 秒または 1000 エントリに指定しています。

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

イベント ハンドラーは、バッファーに格納されたすべての項目を特定のテーブル値の型に変換し、バッチ処理のストアド プロシージャにその型を渡します。 以下のコードは、NavHistoryDataEventArgs クラスと NavHistoryDataMonitor クラスの定義全体を示したものです。

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

イベント ハンドラーは、バッファーに格納されたすべての項目を特定のテーブル値の型に変換し、バッチ処理のストアド プロシージャにその型を渡します。 以下のコードは、NavHistoryDataEventArgs クラスと NavHistoryDataMonitor クラスの定義全体を示したものです。

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

このバッファリング クラスを使用するアプリケーションは、NavHistoryDataMonitor の静的オブジェクトを作成することになります。 ユーザーがページにアクセスするたびに、アプリケーションから NavHistoryDataMonitor.RecordUserNavigationEntry メソッドを呼び出します。 それらのエントリをまとめてデータベースに送信する処理は、バッファリングのロジックによって行われます。

### <a name="master-detail"></a>マスター/詳細

テーブル値パラメーターは、単純な挿入シナリオには適しています。 しかし複数のテーブルが関係する一括挿入については、そう簡単にはいきません。 その典型的な例が "マスター/詳細" のシナリオです。 マスター テーブルは、プライマリ エンティティを識別します。 エンティティに関するその他のデータは、1 つまたは複数の詳細テーブルに格納されます。 この場合、詳細テーブルと一意のマスター エンティティとの関係が外部キー リレーションシップによって強制的に適用されます。 単純化した PurchaseOrder テーブルとそれに関連付けられた OrderDetail テーブルを考えてみます。 次の Transact-SQL では、4 つの列 (OrderID、OrderDate、CustomerID、Status) から成る PurchaseOrder テーブルが作成されます。

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder]
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

それぞれの注文は、少なくとも 1 つの商品購入を含んでいます。 この情報を PurchaseOrderDetail テーブルに記録します。 次の Transact-SQL では、5 つの列 (OrderID、OrderDetailID、ProductID、UnitPrice、OrderQty) から成る PurchaseOrderDetail テーブルが作成されます。

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED
( [OrderID] ASC, [OrderDetailID] ASC ))
```

PurchaseOrderDetail テーブルの OrderID 列は、PurchaseOrder テーブルの注文と対応関係にあることが必要です。 この制約を強制的に適用する外部キーの定義は次のようになります。

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

テーブル値パラメーターを使用するには、対象となる各テーブルについてユーザー定義テーブル型が必要です。

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

そのうえで、これらの型のテーブルを受け取るストアド プロシージャを定義します。 このプロシージャによって、アプリケーションは一連の注文と注文明細をローカルから 1 回の呼び出しでバッチ処理することができます。 次の Transact-SQL は、この注文処理の例に使用されるストアド プロシージャの宣言全体です。

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE (
SubmittedKey int,
ActualKey int,
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table,
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

この例では、新しく挿入された行の実際の OrderID 値が、ローカルに定義されている @IdentityLink テーブルに格納されます。 これらの注文 ID は、テーブル値パラメーターの @orders や @details にある一時的な OrderID 値とは異なります。 そのため、@orders パラメーターから得た OrderID の値と、PurchaseOrder テーブル内の新しい行に使用される実際の OrderID の値とが、@IdentityLink テーブルによって結び付けられます。 このステップの後は、外部キー制約を満たす実際の OrderID を持った注文明細の挿入処理が、@IdentityLink テーブルを介して効率的に実行されます。

このストアド プロシージャは、コードから使用することも、他の Transact-SQL の呼び出しから使用することもできます。 コード例については、この記事のテーブル値パラメーターのセクションを参照してください。 次の Transact-SQL は、sp_InsertOrdersBatch を呼び出しています。

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

この方法によって、1 から始まる一連の OrderID 値をそれぞれのバッチが使用できるようになります。 これらの一時的な OrderID 値はそのバッチ内の関係を表すものであり、実際の OrderID 値は挿入操作の時点で決まります。 先ほどの例で同じステートメントを繰り返し実行し、一意の注文をデータベースに生成することができます。 このため、このバッチ処理手法を用いるときは、注文の重複を防止するためのコードまたはデータベース ロジックを追加することを検討してください。

この例は、複雑なデータベース操作 (マスター/詳細操作など) であっても、テーブル値パラメーターを使用すればバッチ処理で対応できることを示しています。

### <a name="upsert"></a>UPSERT

バッチ処理のシナリオをもう 1 つ紹介します。既存の行の更新と新しい行の挿入を同時に実行するというものです。 この操作を "UPSERT" (update + insert) と呼ぶことがあります。 この作業には、INSERT と UPDATE を別々に呼び出して実行するよりも、MERGE ステートメントを使ったやり方が最適です。 MERGE ステートメントは、挿入と更新の両方の操作を 1 回の呼び出しで実行することができます。

テーブル値パラメーターと MERGE ステートメントとを組み合わせることによって、更新と挿入を実行できます。 たとえば、EmployeeID、FirstName、LastName、SocialSecurityNumber の列を含んだ単純な Employee テーブルがあるとします。

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED
([EmployeeID] ASC ))
```

この例では、SocialSecurityNumber が一意であることを利用して、複数の従業員の MERGE を実行できます。 まず、ユーザー定義テーブル型を作成します。

```sql
CREATE TYPE EmployeeTableType AS TABLE
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

次に、MERGE ステートメントで更新と挿入を行うコードを記述するか、ストアド プロシージャを作成します。 次の例では、EmployeeTableType 型のテーブル値パラメーター @employees に対して MERGE ステートメントを使っています。 @employees テーブルの内容は、ここには記載していません。

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees)
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN
UPDATE SET
target.FirstName = source.FirstName,
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

詳細については、MERGE ステートメントのドキュメントと例を参照してください。 INSERT 操作と UPDATE 操作を個別に使用した複数のステップから成るストアド プロシージャで同じ作業を実行することもできますが、MERGE ステートメントの方が効率的です。 直接 MERGE ステートメントを使った Transact-SQL の呼び出しをデータベース コードで作成することもできます。そうすれば、INSERT 用と UPDATE 用の 2 つのデータベース呼び出しは必要ありません。

## <a name="recommendation-summary"></a>推奨事項のまとめ

この記事で説明したバッチ処理の推奨事項は以下のとおりです。

* Azure SQL Database アプリケーションと Azure SQL Managed Instance アプリケーションのパフォーマンスとスケーラビリティを高めるには、バッファリングとバッチ処理を使用する。
* バッチ処理/バッファリングと回復性とのトレードオフを考える。 ロールの障害発生時、業務上重要なデータのバッチが未処理となっているときに、そのデータが一度に失われてしまうリスクの方が、バッチ処理によって得られるパフォーマンスのメリットよりも大きい可能性があります。
* 待機時間を軽減するために、データベースに対するすべての呼び出しをできるだけ単一のデータセンターの範囲内に維持する。
* 単一バッチによる処理手法を使う場合、パフォーマンスと柔軟性が最も高いのはテーブル値パラメーター。
* 挿入操作の処理速度を最大限に高めるには、以下に示す一般的なガイドラインに従いつつ、実際のシナリオに合わせてテストする。
  * 100 行未満の場合、単一のパラメーター化 INSERT コマンドを使用する。
  * 1,000 行未満の場合は、テーブル値パラメーターを使用する。
  * 1,000 行以上の場合は、SqlBulkCopy を使用する。
* 更新操作と削除操作に関しては、テーブル値パラメーターを使用する。テーブル パラメーター内の行ごとに適切な操作を判断するストアド プロシージャのロジックを使用します。
* バッチ サイズのガイドライン:
  * アプリケーションとビジネスの要件に見合った最大バッチ サイズを使用する。
  * バッチ サイズを大きくすることによるパフォーマンス上のメリットと、一時的または致命的な障害が発生することによって生じるリスクとのバランスを考える。 再試行を余儀なくされたり、まとまったデータが失われたりした場合の影響を考慮してください。
  * 最大バッチ サイズをテストして、Azure SQL Database または Azure SQL Managed Instance によって拒否されないことを確認する。
  * バッチ処理を制御する構成設定 (バッチ サイズ、バッファリングの時間枠など) を作成する。 これらの設定によって柔軟性を確保することができます。 運用環境でクラウド サービスをデプロイし直すことなく、バッチ処理の動作を変更することが可能です。
* 1 つのデータベース内の単一テーブルに対してはバッチの並列実行を回避する。 単一のバッチを分割して複数のワーカー スレッドに割り当てる場合、テストを実行して理想的なスレッド数を特定してください。 未指定のままスレッド数が限界を超えると、パフォーマンスが逆に低下します。
* その他さまざまなシナリオでバッチ処理を実装するための手段として、バッファー サイズや経過時間によって作動するバッファリングを検討する。

## <a name="next-steps"></a>次のステップ

この記事では、バッチ処理に関連したデータベースの設計とコーディングの手法によってアプリケーションのパフォーマンスとスケーラビリティを高める方法を重点的に取り上げました。 しかしこれは、全体的な戦略の中の一要素にすぎません。 パフォーマンスとスケーラビリティを高めるその他の方法については、[データベース パフォーマンス ガイダンス](database/performance-guidance.md)と[エラスティック データベース プールの価格およびパフォーマンスに関する考慮事項](database/elastic-pool-overview.md)をご覧ください。