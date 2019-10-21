---
title: Microsoft Azure SQL Database の接続に関する問題のトラブルシューティング | Microsoft Docs
description: Microsoft Azure SQL Database の接続に関する問題のトラブルシューティング
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 3717b36e37f7d9cca206b5a9e8e3cded50393748
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975358"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>Microsoft Azure SQL Database の接続に関する問題のトラブルシューティング

Azure SQL Database への接続に失敗すると、エラー メッセージが表示されます。 接続の問題は、SQL Azure データベースの再構成、ファイアウォールの設定、接続タイムアウト、または間違ったログイン情報によって発生する場合があります。 また、一部の Azure SQL Database リソースの上限に達すると、Azure SQL Database に接続できなくなります。

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>エラー 40613: サーバー < y > のデータベース < x > は現在使用できません

**詳細なエラー**

``40613: Database <DBname> on server <server name> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '<Tracing ID>'.``

この問題を解決するには:

1. 既知の障害について、[Microsoft Azure サービスのダッシュボード](https://status.azure.com/status)を確認してください。 
2. 既知の障害がない場合は、[Microsoft Azure のサポート Web サイト](http://azure.microsoft.com/support/options)を参照して、サポート ケースを開きます。

詳細については、["サーバーのデータベースが現在利用できない" エラーのトラブルシューティング](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors)に関する記事を参照してください。

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました

この問題は、アプリケーションがサーバーに接続できないために発生します。

この問題を解決するには、以下のセクションにある、「**一般的な接続に関する問題を修正するための手順**」というタイトルの手順を (順番に) 試してください。

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>サーバーが見つからなかったか、サーバーにアクセスできませんでした (エラー26、40、10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>エラー 26:指定されたサーバーまたはインスタンスの位置を特定しているときにエラーが発生しました

**詳細なエラー**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>エラー 40:SQL Server への接続を開けませんでした

**詳細なエラー**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>エラー 10053:サーバーから結果を受信しているときに、トランスポート レベルのエラーが発生しました。

**詳細なエラー**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

これらの問題は、アプリケーションがサーバーに接続できないために発生します。

この問題を解決するには、以下のセクションにある、「**一般的な接続に関する問題を修正するための手順**」というタイトルの手順を (順番に) 試してください。

## <a name="cannot-connect-to--servername--due-to-firewall-issues"></a>ファイアウォールの問題のため < servername > に接続できません

### <a name="error-40615-cannot-connect-to--servername-"></a>エラー 40615: < servername > に接続できません

この問題を解決するには、[Azure portal を使用して SQL Database のファイアウォール設定を構成します。](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>エラー 5:< servername > に接続できません

この問題を解決するには、クライアントとインターネット間のすべてのファイアウォールで、送信接続用にポート1433 が開いていることを確認します。 

詳細については、[SQL Server アクセスを許可するための Windows ファイアウォールの構成](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)に関する記事を参照してください。

## <a name="unable-to-login-to-the-server-errors-18456-40531"></a>サーバーにログインできません (エラー18456、40531)

### <a name="login-failed-for-user--user-name-"></a>ユーザー '< User name >' はログインできませんでした

**詳細なエラー**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

この問題を解決するには、サービス管理者に連絡し、有効な SQL ユーザー名とパスワードを提供するよう依頼してください。

通常、サービス管理者は、次の手順を使用してログインを追加します。

1. SQL Server Management Studio (SSMS) を使用してサーバーにログインします。
2. 次の SQL クエリを使用して、ログイン名が無効になっていないかどうかを確認します。

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. 対応する名前が無効になっている場合は、次のステートメントを使用して有効にします。 

   ```
   Alter login <User name> enable
   ```

4. SQL ログイン ユーザー名が存在しない場合は、SSMS を使用して作成します。

   1. **[セキュリティ]** をダブルクリックして展開します。 
   2. **[ログイン]** を右クリックし、 **[新しいログイン]** を選択します。 
   3. プレースホルダーを含む生成されたスクリプトでは、次の SQL クエリを編集して、実行できます。
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. **[データベース]** をダブルクリックします。 
6. ユーザーにアクセス許可を付与する対象のデータベースを選択します。
7. **[セキュリティ]** をダブルクリックします。 
8. **[ユーザー]** を右クリックし、 **[新しいユーザー]** を選択します。 
9. プレースホルダーを含む生成されたスクリプトでは、次の SQL クエリを編集して、実行できます。 

   ```
   CREATE USER <user_name, sysname, user_name>          
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```
   
   > [!NOTE]
   > `sp_addrolemember` を使用して、特定のユーザーを特定のデータベース ロールにマップすることもできます。 

詳細については、[Azure SQL Database におけるデータベースとログインの管理](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)に関するページを参照してください。

## <a name="connection-timeout-expired-errors"></a>接続のタイムアウト エラー

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904):接続がタイムアウトしました。

**詳細なエラー**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904):タイムアウトに達しました。

**詳細なエラー**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException:基になるプロバイダーがオープンで失敗しました。

**詳細なエラー**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>< server name > に接続できません。

**詳細なエラー**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

これらの例外は、接続またはクエリの問題が原因で発生する可能性があります。 このエラーが接続の問題によるものであることを確認するには、「**エラーの原因が接続の問題かどうかを確認する**」というタイトルの以下のセクションを確認してください。

接続のタイムアウトは、アプリケーションがサーバーに接続できないために発生します。 この問題を解決するには、以下のセクションにある、「**一般的な接続に関する問題を修正するための手順**」というタイトルの手順を (順番に) 試してください。

## <a name="transient-errors-errors-40197-40545"></a>一時的なエラー (エラー40197、40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>エラー 40197:要求の処理中にサービスでエラーが発生しました。 もう一度実行してください。 エラー コード < code >

この問題は、バックエンドでの再構成またはフェールオーバー中に発生した一時的なエラーが原因で起こります。

この問題を解決するには、しばらく待ってから再試行してください。 問題が継続する場合を除き、サポート ケースは必要ありません。

ベスト プラクティスとして、再試行ロジックが実施されていることを確認します。 再試行ロジックの詳細については、[SQL Database への一時的な障害および接続エラーのトラブルシューティング](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)に関する記事を参照してください。

## <a name="connection-terminated-due-to-hitting-some-system-defined-limit"></a>システム定義の制限に達したため、接続が切断されました

### <a name="error-10928-resource-id-d"></a>エラー 10928: リソース ID: %d。

**詳細なエラー**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

この問題に対処するには、次のいずれかの方法を試してみてください。

* 実行時間の長いクエリがあるかどうかを確認します。

  > [!NOTE]
  > これは、必ずしも問題を解決するとは限らない、最小限のアプローチです。

  1. 次の SQL クエリを実行することにより、[sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ビューをチェックして、ブロックしている要求がないか確認します。

     ```
     SELECT * FROM dm_exec_requests
     ```

  2. ヘッド ブロッカーの**入力バッファー**を特定します。
  3. ヘッド ブロッカー クエリを調整します。

    詳細なトラブルシューティング手順については、「[クラウドでクエリが正常に実行されているか](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)」を参照してください。

* ブロックしているクエリや実行時間の長いクエリに対応しているにもかかわらず、データベースが常に制限に達する場合は、新しいプレビュー エディション ([Standard エディションや Premium エディションなど](https://azure.microsoft.com/pricing/details/sql-database/)) にアップグレードすることを検討してください。

SQL Database 価格オプションの詳細については、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/single/)」を参照してください。

動的管理ビューの詳細については、「[システム動的管理ビュー](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)」をご覧ください。

このエラー メッセージの詳細については、「[Azure SQL Database サーバーの SQL Database リソース制限](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)」を参照してください。

### <a name="error-10929-resource-id-1"></a>エラー 10929:リソース ID:1.

**詳細なエラー**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

このエラーの詳細については、「[SQL Database クライアント プログラムのエラー メッセージ](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)」を参照してください。

### <a name="error-40501-the-service-is-currently-busy"></a>エラー 40501:サービスは現在ビジー状態です

**詳細なエラー**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

これは、リソースの制限を超えていることを示すエンジン調整エラーです。

リソース制限の詳細については、[Database サーバーのリソース制限](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)に関する記事を参照してください。

### <a name="error-40544--the-database-has-reached-its-size-quota"></a>エラー 40544 :データベースのサイズ クォータに達しました

**詳細なエラー**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

このエラーは、データベースのサイズ クォータに達したときに発生します。

次の手順を使用すると、問題を回避したり、検討可能な追加オプションを選択したりすることができます。

1. Azure portal のダッシュボードを使用して、データベースの現在のサイズを確認します。

   > [!NOTE]
   > 最も多くの領域を消費しているテーブルとクリーンアップの候補となるテーブルを特定するには、次の SQL クエリを使用できます。

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. 現在のサイズが、使用しているエディションでサポートされている最大サイズを超えていない場合は、ALTER DATABASE を使用して MAXSIZE の設定を増やすことができます。 
3. データベースのサイズが、使用しているエディションでサポートされている最大サイズを既に超えている場合は、次のいずれかの手順を実行できます。
   1. 通常のデータベース クリーンアップ アクティビティを実行します (truncate/delete などを使用して不要なデータをクリーンアップしたり、SSIS や bcp などを使用してデータを外に移動します)。
   2. データをパーティション分割するか、データを削除するか、インデックスを削除してください。その他の解決方法についてはドキュメントを参照してください。 
   
   *  データベースのスケーリングについては、[単一データベースのリソースのスケーリング](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale)に関する記事と、[エラスティック プールのリソースのスケーリング](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)に関する記事を参照してください。

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>エラー 40549:トランザクションが長時間実行されているため、セッションを終了しました。

**詳細なエラー**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

このエラー メッセージが繰り返し表示される場合は、次の手順を実行してこの問題を解決してください。 

1. 次の SQL スクリプトを実行することにより、_exec_requests ビューをチェックして、開いているセッションで total_elapsed_time 列の値が大きいものがないか確認します。

   ```
   SELECT * FROM dm_exec_requests
   ```
2. 長時間実行されているクエリの入力バッファーを特定します。 
3. クエリを調整します。

また、クエリのバッチ処理も検討してください。 バッチ処理については、「[バッチ処理を使用して SQL Database アプリケーションのパフォーマンスを強化する方法](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)」を参照してください。

詳細なトラブルシューティング手順については、「[クラウドでクエリが正常に実行されているか](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)」を参照してください。

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>エラー 40551:TEMPDB の使用量が多すぎるため、セッションを終了しました。

**詳細なエラー**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

この問題を回避するには、次の手順に従ってください。

1. クエリを変更して、一時テーブル領域の使用量を減らします。 
2. 不要になった一時オブジェクトを削除します。 
3. テーブルを切り捨てるか、使用されていないテーブルを削除します。

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>エラー 40552:トランザクション ログの使用領域が多すぎるため、セッションを終了しました。

**詳細なエラー**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

この問題を解決するには、次の方法に従ってください。 

* この問題は、挿入、更新、または削除の各操作が原因で発生します。 バッチ処理を実装したり、複数の小さなトランザクションに分割したりして、すぐに操作される行の数を減らしてみてください。
* この問題は、インデックスの再構築操作によって発生します。 必ず次の式に従うようにしてください: テーブル内で影響を受ける行の数 * (更新されるフィールドの平均サイズ (バイト単位) + 80) < 2 GB

  > [!NOTE]
  > インデックスの再構築の場合は、更新されるフィールドの平均サイズを、平均インデックス サイズに置き換えてください。

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>エラー 40553:メモリの使用量が多すぎるため、セッションを終了しました。

**詳細なエラー**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

この問題を回避するには、クエリを最適化してみてください。

詳細なトラブルシューティング手順については、「[クラウドでクエリが正常に実行されているか](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)」を参照してください。


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>このログインで要求されたデータベース "master" を開けません。 ログインに失敗しました。

この問題は、アカウントに master データベースへのアクセス権限がないために発生します。 ただし、既定では、SQL Server Management Studio (SSMS) は master データベースへの接続を試みます。

この問題を解決するには、次の手順に従ってください。

1. SSMS のログイン画面で **[オプション]** をクリックし、次に **[接続プロパティ]** をクリックします。 
2. **[データベースに接続]** で、既定のログイン データベースとしてユーザーの既定のデータベース名を入力し、 **[接続]** をクリックします。

   ![cannot-open-database-master.png](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-due-to-a-connectivity-issue"></a>エラーの原因が接続の問題かどうかを確認する

接続の問題が原因でエラーが発生しているかどうかを確認するには、次のような接続を開くための呼び出しを示すフレームのスタック トレースを確認します (**SqlConnection** クラスへの参照に注意)。

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

クエリの問題が原因で例外が発生した場合、次のような呼び出し履歴が表示されます (**SqlCommand** クラスへの参照に注意)。 これらのシナリオでは、[クエリを調整します](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)。

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
パフォーマンスの細かい調整に関する詳細なガイダンスについては、以下を参照してください。

* [Azure SQL のインデックスと統計情報を管理する方法](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Azure SQL Database でのクエリのパフォーマンスを手動でチューニングする](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [動的管理ビューを使用して Azure SQL Database のパフォーマンスを監視する](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Azure SQL Database のクエリ ストアの動作](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>一般的な接続に関する問題を修正するための手順

1. アプリケーション サーバーで TCP IP がクライアント プロトコルとして有効になっていることを確認します。 詳細については、「[クライアント プロトコルの構成](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols)」を参照してください。 SQL Server ツールがインストールされていないアプリケーション サーバーでは、**cliconfg.exe** (SQL Server クライアント ネットワーク ユーティリティ) を実行して、TCP IP が有効になっていることを確認します。 
2. アプリケーションの接続文字列を確認して、正しく構成されていることを確認します。 たとえば、接続文字列で正しいポート (1433) と完全修飾サーバー名が指定されていることを確認します。
「[SQL Server の接続情報を取得する](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information)」を参照してください。
3. 接続**タイムアウト**を増やしてみます。 Microsoft では、30 秒以上の接続タイムアウトを使用することをお勧めしています。 
4. [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)、UDL ファイル、ping、および telnet を使用して、アプリケーション サーバーと Azure SQL データベース間の接続をテストします。 詳細については、[SQL Server の接続問題のトラブルシューティング](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)と[接続問題の診断](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)に関する記事を参照してください。

   > [!NOTE]
   > トラブルシューティングの手順として、別のクライアント コンピューターでの接続をテストしてみることもできます。

5. ベスト プラクティスとして、再試行ロジックが実施されていることを確認します。 再試行ロジックの詳細については、[SQL Database への一時的な障害および接続エラーのトラブルシューティング](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)に関する記事を参照してください。

前の手順で問題が解決しない場合は、さらに多くのデータを収集して、サポートに連絡してください。 アプリケーションがクラウド サービスの場合は、ログ記録を有効にします。 この手順により、エラーの UTC タイムスタンプが返されます。 さらに、SQL Azure によりトレース ID が返されます。 [Microsoft カスタマー サポート サービス](http://azure.microsoft.com/support/options/)はこの情報を使用できます。 

ログ記録を有効にする方法の詳細については、[Azure App Service での Web アプリの診断ログの有効化](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)に関する記事を参照してください。

**関連ドキュメント**

* [Azure SQL の接続アーキテクチャ](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [Azure SQL Database および Data Warehouse のネットワーク アクセスの制御](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)