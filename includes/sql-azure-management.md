
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>SQL Server Management Studio を使用した Azure SQL Database の管理
SQL Server Management Studio (SSMS) を使用して Azure SQL Database の論理サーバーとデータベースを管理できます。 このトピックでは、SSMS を使用した一般的なタスクの手順について説明します。 始める前に、Azure SQL Database 論理サーバーとデータベースが既にある必要があります。 作業を開始するには、まず、[最初の Azure SQL Database の作成](../articles/sql-database/sql-database-get-started.md)に関するページを読みます。

Azure SQL Database を操作する際は、常に最新バージョンの SSMS を使用することをお勧めします。 これを取得するには、「[SQL Server Management Studio のダウンロード](https://msdn.microsoft.com/en-us/library/mt238290.aspx)」を参照してください。 

## <a name="connect-to-a-sql-database-logical-server"></a>SQL Database 論理サーバーに接続する
SQL データベースに接続するには、Azure 上のサーバー名が必要になります。 この情報を入手するには、ポータルへのサインインが必要になる場合があります

1. [Azure 管理ポータル](http://manage.windowsazure.com)にサインインします。
2. 左のウィンドウで、 **[SQL データベース]**をクリックします。
3. SQL データベースのホーム ページで、ページ最上部の **[サーバー]** をクリックし、自分のサブスクリプションに関連付けられている全サーバーの一覧を表示します。 接続先のサーバーの名前を探し、その名前をクリップボードにコピーします。
   
   次に、SQL データベース ファイアウォールを構成して、ローカル コンピューターからの接続を許可します。 それには、ローカル コンピューターの IP アドレスをファイアウォールの例外一覧に追加します。
4. SQL データベースのホーム ページで **[サーバー]** をクリックし、接続するサーバーをクリックします。
5. ページの上部にある **[構成]** をクリックします。
6. [現在のクライアント IP アドレス] の IP アドレスをコピーします。
7. [構成] ページの **[使用できる IP アドレス]** には、ルールの名前、IP アドレス範囲の開始値と終了値を指定できる 3 つのボックスがあります。 ルール名としては、コンピューター名などを入力します。 範囲の開始値および終了値としてコンピューターの IP アドレスを両方のボックスに入力し、表示されるチェック ボックスをオンにします。
   
   ルール名は一意である必要があります。 開発コンピューターで設定している場合は、[IP 範囲の開始] ボックスと [IP 範囲の終了] ボックスの両方に同じ IP アドレスを入力してもかまいません。 それ以外の場合は、組織の他のコンピューターからの接続に対応するために、より広い範囲の IP アドレスの入力が必要になる可能性があります。
8. ページの下部にある **[保存]** をクリックします。
   
    **注:** ファイアウォール設定の変更が反映されるまで 5 分程度かかる場合があります。
   
    これで Management Studio を使用して SQL データベースに接続する準備ができました。
9. タスク バーで、**[スタート]** をクリックし、**[すべてのプログラム]**、**[Microsoft SQL Server 2014]** の順にポイントして、**[SQL Server Management Studio]** をクリックします。
10. **[サーバーへの接続]**で、完全修飾サーバー名として *serverName*.database.windows.net を指定します。 Azure では、サーバー名は英数字から成る自動生成文字列です。
11. **[SQL Server 認証]**を選択します。
12. **[ログイン]** ボックスに、サーバーの作成時にポータルで指定した SQL Server 管理者ログインを入力します。
13. **[パスワード]** ボックスに、サーバーの作成時にポータルで指定したパスワードを入力します。
14. **[接続]** をクリックして接続を確立します。

最新の更新プログラムを適用した SQL Server 2014 SSMS では、Azure SQL データベースの作成や変更などのタスクのサポートが拡張されています。 また、Transact-SQL ステートメントを使用して、これらのタスクを実行することもできます。 次の手順では、こうしたステートメントの例を紹介します。 サポートされているコマンドの詳細など、SQL Database での Transact-SQL の使用方法の詳細については、「 [Transact-SQL リファレンス (データベース エンジン)](http://msdn.microsoft.com/library/bb510741.aspx)」をご覧ください。

## <a name="create-and-manage-azure-sql-databases"></a>Azure SQL データベースの作成と管理
**master** データベースに接続している間に、サーバーに新しいデータベースを作成したり、既存のデータベースを変更または削除したりできます。 以下の手順では、Management Studio を使用して一般的なデータベース管理タスクを実行する方法について説明します。 これらのタスクを実行するには、サーバーのセットアップ時に作成したサーバー レベルのプリンシパル ログインを使用して **master** データベースに接続している必要があります。

Management Studio でクエリ ウィンドウを開くには、[データベース] フォルダーを開き、**[システム データベース]** フォルダーを展開します。**[master]** を右クリックし、**[新しいクエリ]** をクリックします。

* 新しいデータベースを作成するには、 **CREATE DATABASE** ステートメントを使用します。 詳細については、「 [CREATE DATABASE (Azure SQL データベース)](https://msdn.microsoft.com/library/dn268335.aspx)」を参照してください。 次のステートメントは、 **myTestDB** という名前の新しいデータベースを作成し、既定の最大サイズが 250 GB の Standard S0 エディションのデータベースとして指定します。
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

**[実行]** をクリックしてクエリを実行します。

* データベースの名前とエディションを変更する場合など、既存のデータベースを変更するには、 **ALTER DATABASE** ステートメントを使用します。 詳細については、「 [ALTER DATABASE (Windows Azure SQL データベース)](https://msdn.microsoft.com/library/ms174269.aspx)」を参照してください。 次のステートメントは、前の手順で作成したデータベースのエディションを Standard S1 に変更します。
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* 既存のデータベースを削除するには、**DROP DATABASE** ステートメントを使用します。
  詳細については、「 [DROP DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms178613.aspx)」をご覧ください。 次のステートメントは、**myTestDB** データベースを削除します。ただし、次の手順でログインを作成する際に使用するので、ここでは削除しないでください。
  
      DROP DATABASE myTestBase;
* master データベースには、すべてのデータベースに関する詳細を表示する際に使用できる **sys.databases** ビューがあります。 既存のデータベースをすべて表示するには、次のステートメントを実行します。
  
      SELECT * FROM sys.databases;
* SQL Database では、 **USE** ステートメントを使用してデータベースを切り替えることはできません。 代わりに、ターゲット データベースに直接接続する必要があります。

> [!NOTE]
> データベースを作成または変更する Transact-SQL ステートメントの多くは、それ自体のバッチ内で実行する必要があり、他の Transact-SQL ステートメントと共にグループ化することはできません。 詳細については、上記のリンクでステートメント固有の情報を参照してください。
> 
> 

## <a name="create-and-manage-logins"></a>ログインを作成と管理する
**master** データベースでは、ログインと、どのログインにデータベースや他のログインを作成するためのアクセス許可があるかが追跡されます。 ログインを管理するには、サーバーのセットアップ時に作成したサーバー レベルのプリンシパル ログインを使用して **master** データベースに接続します。 **CREATE LOGIN**、**ALTER LOGIN**、または **DROP LOGIN** ステートメントを使用すると、サーバー全体でログインを管理する master データベースに対してクエリを実行できます。 詳細については、「 [Microsoft Azure SQL データベースにおけるデータベースとログインの管理](http://msdn.microsoft.com/library/azure/ee336235.aspx)」を参照してください。 

* 新しいサーバー レベル ログインを作成するには、 **CREATE LOGIN** ステートメントを使用します。 詳細については、「 [CREATE LOGIN (Windows Azure SQL データベース)](https://msdn.microsoft.com/library/ms189751.aspx)」を参照してください。 次のステートメントは、 **login1**という新しいログインを作成します。 **password1** を適切なパスワードに置き換えてください。
  
      CREATE LOGIN login1 WITH password='password1';
* データベース レベルのアクセス許可を付与するには、 **CREATE USER** ステートメントを使用します。 ログインは必ず **master** データベースに作成する必要がありますが、別のデータベースに接続するためのログインに対しては、そのデータベースで **CREATE USER** ステートメントを使用してデータベース レベルのアクセス許可を付与する必要があります。 詳細については、「 [CREATE USER (Azure SQL Database)](https://msdn.microsoft.com/library/ms173463.aspx)」を参照してください。 
* login1 に **myTestDB**というデータベースに対するアクセス許可を付与するには、次の手順に従います。
  
  1. オブジェクト エクスプローラーを更新して、作成した **myTestDB** データベースを表示させるには、オブジェクト エクスプローラーのサーバー名を右クリックして、**[最新の情報に更新]** をクリックします。  
     
     接続を閉じてある場合は、[ファイル] メニューの **[オブジェクト エクスプローラーを接続]** を選択して再接続できます。
  2. **myTestDB** データベースを右クリックし、**[新しいクエリ]** を選択します。
  3. myTestDB データベースに対して次のステートメントを実行して、サーバー レベルのログイン **login1** に対応する **login1User** という名前のデータベース ユーザーを作成します。
     
         CREATE USER login1User FROM LOGIN login1;
* このデータベースに対する適切なレベルのアクセス許可をユーザー アカウントに付与するには、**sp\_addrolemember** ストアド プロシージャを使用します。 詳細については、「[sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx)」をご覧ください。 次のステートメントは、**login1User** を **db\_datareader** ロールに追加することで、データベースに対する読み取り専用アクセス許可を **login1User** に付与します。
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* ログインのパスワードなど、既存のログインを必要に応じて変更するには、 **ALTER LOGIN** ステートメントを使用します。 詳細については、「 [ALTER LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189828.aspx)」をご覧ください。 **ALTER LOGIN** ステートメントは、**master** データベースに対して実行する必要があります。 そのデータベースに接続されているクエリ ウィンドウに切り替えてください。 
  
  次のステートメントは、ログイン **login1** を変更してパスワードをリセットします。
  **newPassword** を適切なパスワードに、**oldPassword** をログインの現在のパスワードに置き換えます。
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* 既存のログインを削除するには、 **DROP LOGIN** ステートメントを使用します。
  サーバー レベルでログインを削除すると、それに関連付けられているデータベース ユーザー アカウントも削除されます。 詳細については、「 [DROP DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms178613.aspx)」をご覧ください。 **DROP LOGIN** ステートメントは、**master** データベースに対して実行する必要があります。 次のステートメントは、ログイン **login1** を削除します。
  
      DROP LOGIN login1;
* master データベースには、ログインを表示する際に使用できる **sys.sql\_logins** ビューがあります。 既存のログインをすべて表示するには、次のステートメントを実行します。
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-viewsh2"></a>動的管理ビューを使用して SQL データベースを監視する</h2>
SQL データベースでは、個々のデータベースを監視するのに使用できる動的管理ビューがいくつかサポートされています。 以下に、これらのビューで取得できるモニター データの種類の例を示します。 すべての種類と詳しい使用例については、「 [動的管理ビューを使用した Azure SQL Database の監視](https://msdn.microsoft.com/library/azure/ff394114.aspx)」をご覧ください。

* 動的管理ビューに対してクエリを実行するには、 **VIEW DATABASE STATE** アクセス許可が必要です。 **VIEW DATABASE STATE** アクセス許可を特定のデータベース ユーザーに付与するには、サーバー レベルのプリンシプル ログインで管理するデータベースに接続し、データベースに対して次のステートメントを実行します。
  
      GRANT VIEW DATABASE STATE TO login1User;
* **sys.dm\_db\_partition\_stats** ビューを使用して、データベース サイズを計算します。 **sys.dm\_db\_partition\_stats** ビューは、データベース内のすべてのパーティションのページと行数情報を返します。これらを使ってデータベース サイズを計算できます。 次のクエリはデータベース サイズを MB 単位で返します。
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* データベースに関連付けられている内部タスクと現在のユーザー接続に関する情報を取得するには、**sys.dm\_exec\_connections** ビューと **sys.dm\_exec\_sessions** ビューを使用します。 次のクエリは現在の接続に関する情報を返します。
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* キャッシュされたクエリ プランの集計パフォーマンス統計を取得するには、**sys.dm\_exec\_query\_stats** ビューを使用します。 次のクエリは、平均 CPU 時間の上位 5 クエリに関する情報を返します。
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          MIN(query_stats.statement_text) AS "Statement Text"
      FROM
          (SELECT QS.*,
          SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          ((CASE statement_end_offset
              WHEN -1 THEN DATALENGTH(ST.text)
              ELSE QS.statement_end_offset END
                  - QS.statement_start_offset)/2) + 1) AS statement_text
           FROM sys.dm_exec_query_stats AS QS
           CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      GROUP BY query_stats.query_hash
      ORDER BY 2 DESC;



<!--HONumber=Jan17_HO3-->


