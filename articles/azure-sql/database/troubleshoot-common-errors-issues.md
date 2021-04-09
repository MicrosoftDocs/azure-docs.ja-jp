---
title: Azure SQL Database との接続に関する一般的な問題のトラブルシューティング
description: Azure SQL Database の接続の問題をトラブルシューティングし、Azure SQL Database または Azure SQL Managed Instance 固有のその他の問題を解決する手順について説明します
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2021
ms.openlocfilehash: ec61f2c67576d6e144d8d4bb7e8ecaaa157db0a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233374"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database および Azure SQL Managed Instance の接続に関する問題とその他のエラーのトラブルシューティング
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database または Azure SQL Managed Instance への接続に失敗すると、エラー メッセージが表示されます。 これらの接続の問題は、再構成、ファイアウォールの設定、接続のタイムアウト、正しくないログイン情報、または[アプリケーション設計](develop-overview.md)プロセスの間にベスト プラクティスと設計ガイドラインが適用されなかったことが原因で発生する可能性があります。 また、一部の Azure SQL Database または SQL Managed Instance リソースの上限に達した場合、これ以上接続できなくなります。

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>一時的な障害のエラー メッセージ (40197、40613、その他)

Azure インフラストラクチャには、SQL Database サービス内で負荷の大きいワークロードが生じた場合に、サーバーを動的に再構成する機能があります。  この動的な動作によって、クライアント プログラムがデータベースやインスタンスへの接続を失うことがあります。 この種のエラーは、 *「一時的な障害」* と呼ばれます。 データベースの再構成イベントは、計画されたイベント (ソフトウェアのアップグレードなど) または計画されていないイベント (プロセスのクラッシュ、負荷分散など) が原因で発生します。 通常、ほとんどの再構成イベントは一時的であり、長くて 1 分もかかりませんが、 これらのイベントは、大規模なトランザクションによる実行時間の長い復旧など、場合によっては、完了に時間がかかることがあります。 次の表では、SQL Database に接続するときにアプリケーションが受け取る可能性のあるさまざまな一時的エラーの一覧を示します

### <a name="list-of-transient-fault-error-codes"></a>一時的な障害のエラー コードの一覧

| エラー コード | 重大度 | 説明 |
| ---:| ---:|:--- |
| 4060 |16 |ログインで要求されたデータベース "%.&#x2a;ls" を開くことができません。 ログインに失敗しました。 詳細については、[エラー 4000 から 4999](/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999) を参照してください。|
| 40197 |17 |要求の処理中にサービスでエラーが発生しました。 再試行してください。 エラー コード %d。<br/><br/>ソフトウェアやハードウェアのアップグレード、ハードウェアの障害、その他フェールオーバーに関する問題によってサービスがダウンしたときに、このエラーが発生します。 障害の種類や発生したフェールオーバーに関する詳細な情報は、エラー 40197 のメッセージに埋め込まれたエラー コード (%d) から得られます。 エラー 40197 のメッセージ内に埋め込まれているエラー コードは、40020、40143、40166、40540 などです。<br/><br/>再接続すると、自動的にデータベースの正常なコピーに接続されます。 アプリケーションでエラー 40197 をキャッチし、メッセージに埋め込まれているエラー コード (%d) をログに記録してトラブルシューティングに備えたうえで、リソースが復旧して接続が再度確立されるまで SQL Database への再接続を試みる必要があります。 詳細については、「[一時エラー](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults)」を参照してください。|
| 40501 |20 |サービスは現在ビジー状態です。 10 秒後に要求を再試行してください。 インシデント ID: %ls。 コード: %d。 詳細については、次を参照してください。 <br/>&bull; &nbsp;[論理 SQL サーバー リソースの制限](resource-limits-logical-server.md)<br/>&bull; &nbsp;[単一データベースに関する DTU ベースの制限](service-tiers-dtu.md)<br/>&bull; &nbsp;[エラスティック プールに関する DTU ベースの制限](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[単一データベースに関する仮想コアベースの制限](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[エラスティック プールに関する仮想コアベースの制限](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL Managed Instance のリソースの制限](../managed-instance/resource-limits.md)。|
| 40613 |17 |サーバー '%.&#x2a;ls' のデータベース '%.&#x2a;ls' は現在使用できません。 後で接続を再試行してください。 問題が解決しない場合は、'%.&#x2a;ls' のセッション トレース ID を控えてカスタマー サポートに問い合わせてください。<br/><br/> このエラーは、データベースに対して専用管理者接続 (DAC) が既に確立されている場合に発生する可能性があります。 詳細については、「[一時エラー](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults)」を参照してください。|
| 49918 |16 |要求を処理できません。 要求を処理するリソースが十分ではありません。<br/><br/>サービスは現在ビジー状態です。 後で要求を再試行してください。 詳細については、次を参照してください。 <br/>&bull; &nbsp;[論理 SQL サーバー リソースの制限](resource-limits-logical-server.md)<br/>&bull; &nbsp;[単一データベースに関する DTU ベースの制限](service-tiers-dtu.md)<br/>&bull; &nbsp;[エラスティック プールに関する DTU ベースの制限](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[単一データベースに関する仮想コアベースの制限](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[エラスティック プールに関する仮想コアベースの制限](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL Managed Instance のリソースの制限](../managed-instance/resource-limits.md)。 |
| 49919 |16 |要求を処理、作成、更新できません。 サブスクリプション "%ld" に対して進行中の作成または更新操作が多すぎます。<br/><br/>サービスが、サブスクリプションまたはサーバーに対する複数の作成または更新要求の処理でビジ―状態です。 現在、要求はリソースの最適化のためにブロックされています。 クエリ [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) を実行して保留中の操作を確認します。 保留中の作成要求または更新要求が完了するまで待つか、いずれかの保留中の要求を削除して後で要求を再試行します。 詳細については、次を参照してください。 <br/>&bull; &nbsp;[論理 SQL サーバー リソースの制限](resource-limits-logical-server.md)<br/>&bull; &nbsp;[単一データベースに関する DTU ベースの制限](service-tiers-dtu.md)<br/>&bull; &nbsp;[エラスティック プールに関する DTU ベースの制限](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[単一データベースに関する仮想コアベースの制限](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[エラスティック プールに関する仮想コアベースの制限](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL Managed Instance のリソースの制限](../managed-instance/resource-limits.md)。 |
| 49920 |16 |要求を処理できません。 サブスクリプション "%ld" に対して進行中の操作が多すぎます。<br/><br/>サービスが、このサブスクリプションに対する複数の要求の処理でビジー状態です。 現在、要求はリソースの最適化のためにブロックされています。 クエリ [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) を実行して操作の状態を確認します。 保留中の要求が完了するまで待つか、いずれかの保留中の要求を削除して後で要求を再試行します。 詳細については、次を参照してください。 <br/>&bull; &nbsp;[論理 SQL サーバー リソースの制限](resource-limits-logical-server.md)<br/>&bull; &nbsp;[単一データベースに関する DTU ベースの制限](service-tiers-dtu.md)<br/>&bull; &nbsp;[エラスティック プールに関する DTU ベースの制限](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[単一データベースに関する仮想コアベースの制限](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[エラスティック プールに関する仮想コアベースの制限](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL Managed Instance のリソースの制限](../managed-instance/resource-limits.md)。 |
| 4221 |16 |'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING' を長時間待機しているため、read-secondary へのログインに失敗しました。 レプリカのリサイクル時に実行中だったトランザクションに行のバージョンがないため、レプリカはログインに使用できません。 この問題を解決するには、プライマリ レプリカのアクティブ トランザクションをロール バックするか、コミットします。 プライマリ上の長い書き込みトランザクションを避けることでこの状態が発生することを最小限に抑えられます。 |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>一時的な接続の問題を解決する手順

1. アプリケーションによって報告されたエラーで発生している既知の障害については、 [Microsoft Azure サービス ダッシュボード](https://azure.microsoft.com/status) を参照してください。
2. Azure SQL Database など、クラウド サービスに接続するアプリケーションは、定期的な再構成イベントを想定し、アプリケーション エラーをユーザーに示すのではなく、再試行ロジックを実装してこれらのエラーを処理します。
3. データベースがリソースの制限に近づくと、一時的な接続の問題に見える場合があります。 [リソース制限](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached)に関するページを参照してください。
4. 接続の問題が解消されない場合、アプリケーションでのエラーの継続時間が 60 秒を超えた場合、または 1 日にエラーが複数回発生した場合は、 **Azure サポート** サイトの [[サポートの要求]](https://azure.microsoft.com/support/options) を選択して、サポート要求を送信してください。

#### <a name="implementing-retry-logic"></a>再試行ロジックの実装

クライアント プログラムに再試行ロジックを含めて、一時障害に自動的に修復する時間を与えた後、接続の再確立を試行できるようにすることをお勧めします。  最初に再試行する前に、5 秒間待つことをお勧めします。 5 秒未満で再試行すると、クラウド サービスに過度の負荷がかかるおそれがあります。 再試行するたびに、待ち時間を比例して、最大 60 秒まで長くする必要があります。

再試行ロジックのコード例については、以下のページを参照してください。

- [ADO.NET で SQL に弾性的に接続する](/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [PHP で SQL に弾性的に接続する](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

アプリケーションでの一時的エラーの処理の詳細については、「[SQL Database への一時的な接続エラーのトラブルシューティング](troubleshoot-common-connectivity-issues.md)」をご覧ください

ADO.NET を使用するクライアントの *ブロック期間* については、「[接続プール (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)」をご覧ください。

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>サーバーへの接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました

この問題は、アプリケーションでサーバーに接続できない場合に発生します。

この問題を解決するには、「[一般的な接続に関する問題を修正するための手順](#steps-to-fix-common-connection-issues)」セクションの手順を (示されている順に) 試してください。

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>サーバー/インスタンスが見つからなかったか、アクセスできませんでした (エラー 26、40、10053)

### <a name="error-26-error-locating-server-specified"></a>エラー 26:指定されたサーバーの位置を特定しているときにエラーが発生しました

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>エラー 40:サーバーへの接続を開けませんでした

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>エラー 10053:サーバーから結果を受信しているときに、トランスポート レベルのエラーが発生しました

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

これらの問題は、アプリケーションでサーバーに接続できない場合に発生します。

これらの問題を解決するには、「[一般的な接続に関する問題を修正するための手順](#steps-to-fix-common-connection-issues)」セクションの手順を (示されている順に) 試してください。

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>ファイアウォールの問題のため、サーバーに接続できません

### <a name="error-40615-cannot-connect-to--servername-"></a>エラー 40615: < servername > に接続できません

この問題を解決するには、[Azure portal 経由で SQL Database のファイアウォール設定を構成します](firewall-configure.md)。

### <a name="error-5-cannot-connect-to--servername-"></a>エラー 5:< servername > に接続できません

この問題を解決するには、クライアントとインターネット間のすべてのファイアウォールで、送信接続用にポート 1433 が開いていることを確認します。

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>サーバーにログインできません (エラー 18456、40531)

### <a name="login-failed-for-user--user-name-"></a>ユーザー '< User name >' はログインできませんでした

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

この問題を解決するには、サービス管理者に連絡し、有効なユーザー名とパスワードを提供するよう依頼してください。

通常、サービス管理者は、次の手順を使用してログイン資格情報を追加します。

1. SQL Server Management Studio (SSMS) を使用して、サーバーにログインします。
2. master データベースで次の SQL クエリを実行して、ログイン名が無効になっているかどうかを確認します。

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins;
   ```

3. 対応する名前が無効になっている場合は、次のステートメントを使用して有効にします。

   ```sql
   ALTER LOGIN <User name> ENABLE;
   ```

4. SQL ログイン ユーザー名が存在しない場合は、次の SQL クエリを編集して実行し、新しい SQL ログインを作成します。

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = '<password, sysname, Change_Password>';
   GO
   ```

5. SSMS オブジェクト エクスプローラーで、 **[データベース]** を展開します。
6. ユーザーにアクセス許可を付与するデータベースを選択します。
7. **[セキュリティ]** を右クリックし、 **[New]\(新規\)** 、 **[ユーザー]** を選択します。
8. プレースホルダーを含む生成されたスクリプトでは、次の SQL クエリを編集して実行します。

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>;
   GO

   -- Add user to the database owner role
   EXEC sp_addrolemember N'db_owner', N'<user_name, sysname, user_name>';
   GO
   ```

   `sp_addrolemember` を使用して、特定のユーザーを特定のデータベース ロールにマップすることもできます。

   > [!NOTE]
   > Azure SQL Database では、データベース ロールのメンバーシップを管理するために、より新しい [ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql) 構文を検討してください。  

詳細については、[Azure SQL Database でのデータベースとログインの管理](./logins-create-manage.md)に関するページを参照してください。

## <a name="connection-timeout-expired-errors"></a>接続のタイムアウト エラー

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904):接続がタイムアウトしました

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904):タイムアウトに達しました

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException:基になるプロバイダーがオープンで失敗しました

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>< server name > に接続できません

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

これらの例外は、接続またはクエリの問題が原因で発生する可能性があります。 このエラーが接続の問題によって発生したことを確認する場合は、「[エラーの原因が接続の問題かどうかを確認する](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)」を参照してください。

接続のタイムアウトは、アプリケーションでサーバーに接続できないために発生します。 この問題を解決するには、「[一般的な接続に関する問題を修正するための手順](#steps-to-fix-common-connection-issues)」セクションの手順を (示されている順に) 試してください。

## <a name="resource-governance-errors"></a>リソース ガバナンス エラー

### <a name="error-10928-resource-id-d"></a>エラー 10928: リソース ID: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

この問題に対処するには、次のいずれかの方法を試してみてください。

- 実行時間の長いクエリがあるかどうかを確認します。

  > [!NOTE]
  > これは、問題が解決されない可能性のある最小限のアプローチです。 実行時間の長いまたはブロックしているクエリのトラブルシューティングの詳細については、「[Azure SQL Database のブロックの問題の概要と解決策](understand-resolve-blocking.md)」を参照してください。

1. 次の SQL クエリを実行して、[sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ビューを確認し、ブロックしている要求がないかどうかを調べます。

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

1. [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) 動的管理関数と問題のあるクエリの session_id を使用して、ヘッド ブロッカーの **入力バッファー** を特定します。次に例を示します。

   ```sql 
   SELECT * FROM sys.dm_exec_input_buffer (100,0);
   ```

1. ヘッド ブロッカー クエリを調整します。

ブロックしているクエリや実行時間の長いクエリに対応しているにもかかわらず、データベースが常に制限に達する場合は、より多くのリソースを含むエディション ([エディション](https://azure.microsoft.com/pricing/details/sql-database/)) にアップグレードすることを検討してください。

データベースの制限の詳細については、[SQL Database リソース制限](./resource-limits-logical-server.md)に関するページをご覧ください。

### <a name="error-10929-resource-id-1"></a>エラー 10929:リソース ID:1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>エラー 40501:サービスは現在ビジー状態です

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

これは、リソースの制限を超えていることを示すエンジン調整エラーです。

リソース制限の詳細については、[論理 SQL サーバーのリソース制限](./resource-limits-logical-server.md)に関するページをご覧ください。

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>エラー 40544:データベースのサイズ クォータに達しました

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

このエラーは、データベースのサイズ クォータに達したときに発生します。

次の手順は、問題を回避したり、追加オプションを提供したりするのに役立つ場合があります。

1. Azure portal のダッシュボードを使用して、データベースの現在のサイズを確認します。

   > [!NOTE]
   > 最も多くの領域を消費しているため、クリーンアップの候補となるテーブルを特定するには、次の SQL クエリを実行します。

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC;
   ```

2. 現在のサイズが、使用しているエディションでサポートされている最大サイズを超えていない場合は、ALTER DATABASE を使用して MAXSIZE の設定を増やすことができます。
3. データベースが、使用しているエディションでサポートされている最大サイズを既に超えている場合は、次の 1 つまたは複数の手順を試してください。

   - 通常のデータベース クリーンアップ アクティビティを実行します。 たとえば、truncate/delete を使用して不要なデータをクリーンアップしたり、SQL Server Integration Services (SSIS) または一括コピー プログラム (bcp) ユーティリティを使用してデータを移動したりします。
   - データをパーティション分割するか、データを削除するか、インデックスを削除してください。その他の解決方法についてはドキュメントを参照してください。
   - データベースのスケーリングについては、[単一データベースのリソースのスケーリング](./single-database-scale.md)に関する記事と、[エラスティック プールのリソースのスケーリング](./elastic-pool-scale.md)に関する記事を参照してください。

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>エラー 40549:トランザクションが長時間実行されているため、セッションを終了しました

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

このエラー メッセージが繰り返し表示される場合は、これらの手順に従って問題を解決してみてください。

1. sys.dm_exec_requests ビューを確認し、開いているセッションで total_elapsed_time 列の値が大きいものがないか調べます。 次の SQL スクリプトを実行して、この確認を行います。

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) 動的管理関数と問題のあるクエリの session_id を使用して、ヘッド ブロッカーの **入力バッファー** を特定します。次に例を示します。

   ```sql 
   SELECT * FROM sys.dm_exec_input_buffer (100,0);
   ```

3. クエリを調整します。

    > [!Note]
    > Azure SQL Database におけるブロッキングのトラブルシューティングの詳細については、「[Azure SQL Database のブロックの問題の概要と解決策](understand-resolve-blocking.md)」を参照してください。

また、クエリのバッチ処理も検討してください。 バッチ処理については、「[バッチ処理を使用して SQL Database アプリケーションのパフォーマンスを強化する方法](../performance-improve-use-batching.md)」を参照してください。

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>エラー 40551:TEMPDB の使用量が多すぎるため、セッションを終了しました

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

この問題を回避するには、次の手順に従ってください。

1. クエリを変更して、一時テーブル領域の使用量を減らします。
2. 不要になった一時オブジェクトを削除します。
3. テーブルを切り捨てるか、使用されていないテーブルを削除します。

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>エラー 40552:トランザクション ログの使用領域が多すぎるため、セッションを終了しました

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

この問題を解決するには、次の方法を試してください。

- この問題は、挿入、更新、または削除の各操作が原因で発生する可能性があります。
バッチ処理を実装したり、複数の小さなトランザクションに分割したりして、すぐに操作される行の数を減らしてみてください。
- この問題は、インデックスの再構築操作によって発生する可能性があります。 この問題を回避するには、テーブルで影響を受ける行の数 * (更新されるフィールドの平均サイズ (バイト単位) + 80) < 2 ギガバイト (GB) であることを確認します。

  > [!NOTE]
  > インデックスの再構築の場合は、更新されるフィールドの平均サイズを、平均インデックス サイズに置き換える必要があります。

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>エラー 40553:メモリの使用量が多すぎるため、セッションを終了しました

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

この問題を回避するには、クエリを最適化してみてください。

詳細なトラブルシューティング手順については、「[クラウドでクエリが正常に実行されているか](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)」を参照してください。

### <a name="table-of-additional-resource-governance-error-messages"></a>その他のリソース ガバナンス エラー メッセージの表

| エラー コード | 重大度 | 説明 |
| ---:| ---:|:--- |
| 10928 |20 |リソース ID: %d。 データベースの %s 制限の %d に達しました。 詳細については、「[単一データベースとプールされたデータベースに関する SQL Database のリソース制限](resource-limits-logical-server.md)」を参照してください。<br/><br/>リソース ID は、制限に達したリソースを示します。 ワーカー スレッドの場合、リソース ID = 1 となります。 セッションの場合、リソース ID = 2 です。<br/><br/>このエラーの詳細および解決方法については、 <br/>&bull; &nbsp;[論理 SQL サーバー リソースの制限](resource-limits-logical-server.md)<br/>&bull; &nbsp;[単一データベースに関する DTU ベースの制限](service-tiers-dtu.md)<br/>&bull; &nbsp;[エラスティック プールに関する DTU ベースの制限](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[単一データベースに関する仮想コアベースの制限](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[エラスティック プールに関する仮想コアベースの制限](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL Managed Instance のリソースの制限](../managed-instance/resource-limits.md)。 |
| 10929 |20 |リソース ID: %d。 %s の最低限保証は %d、最大値は %d 、データベースの現在の使用状況は %d です。 ただし、サーバーは現在ビジー状態であり、このデータベースの %d を超える要求をサポートできません。 リソース ID は、制限に達したリソースを示します。 ワーカー スレッドの場合、リソース ID = 1 となります。 セッションの場合、リソース ID = 2 です。 詳細については、次を参照してください。 <br/>&bull; &nbsp;[論理 SQL サーバー リソースの制限](resource-limits-logical-server.md)<br/>&bull; &nbsp;[単一データベースに関する DTU ベースの制限](service-tiers-dtu.md)<br/>&bull; &nbsp;[エラスティック プールに関する DTU ベースの制限](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[単一データベースに関する仮想コアベースの制限](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[エラスティック プールに関する仮想コアベースの制限](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL Managed Instance のリソースの制限](../managed-instance/resource-limits.md)。 <br/>その他の場合は、後でもう一度やり直してください。 |
| 40544 |20 |データベースのサイズ クォータに達しました。 データをパーティション分割するか、データを削除するか、インデックスを削除してください。その他の解決方法についてはドキュメントを参照してください。 データベースのスケーリングについては、[単一データベースのリソースのスケーリング](single-database-scale.md)に関する記事と、[エラスティック プールのリソースのスケーリング](elastic-pool-scale.md)に関する記事を参照してください。|
| 40549 |16 |トランザクションが長時間実行されているため、セッションを終了しました。 トランザクションを短くしてください。 バッチ処理については、「[バッチ処理を使用して SQL Database アプリケーションのパフォーマンスを強化する方法](../performance-improve-use-batching.md)」を参照してください。|
| 40550 |16 |取得したロックの数が多すぎるため、セッションを終了しました。 1 つのトランザクションで読み取る行または変更する行の数を減らしてください。 バッチ処理については、「[バッチ処理を使用して SQL Database アプリケーションのパフォーマンスを強化する方法](../performance-improve-use-batching.md)」を参照してください。|
| 40551 |16 |`TEMPDB` の使用領域が多すぎるため、セッションを終了しました。 クエリを変更して一時テーブルの使用領域を減らしてください。<br/><br/>一時オブジェクトを使用している場合は、セッションで不要となった一時オブジェクトを削除して `TEMPDB` データベースの領域を節約してください。 SQL Database での tempdb の使用については、「[SQL Database の Tempdb データベース](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)」を参照してください。|
| 40552 |16 |トランザクション ログの使用領域が多すぎるため、セッションを終了しました。 1 回のトランザクションで変更する行を減らしてください。 バッチ処理については、「[バッチ処理を使用して SQL Database アプリケーションのパフォーマンスを強化する方法](../performance-improve-use-batching.md)」を参照してください。<br/><br/>`bcp.exe` ユーティリティまたは `System.Data.SqlClient.SqlBulkCopy` クラスを使用して一括挿入を実行する場合は、1 回のトランザクションでサーバーにコピーされる行数を `-b batchsize` オプションまたは `BatchSize` オプションで制限してください。 `ALTER INDEX` ステートメントでインデックスを再構築する場合は、`REBUILD WITH ONLINE = ON` オプションの使用を検討してください。 仮想コア購入モデルでのトランザクション ログ サイズについては、次を参照してください。 <br/>&bull; &nbsp;[単一データベースに関する仮想コアベースの制限](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[エラスティック プールに関する仮想コアベースの制限](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL Managed Instance のリソースの制限](../managed-instance/resource-limits.md)。|
| 40553 |16 |メモリの使用量が多すぎるため、セッションを終了しました。 クエリを変更して、処理する行を減らしてください。<br/><br/>Transact-SQL コード内の `ORDER BY` 操作と `GROUP BY` 操作の数を減らすことで、クエリのメモリ要件を抑えられます。 データベースのスケーリングについては、[単一データベースのリソースのスケーリング](single-database-scale.md)に関する記事と、[エラスティック プールのリソースのスケーリング](elastic-pool-scale.md)に関する記事を参照してください。|

## <a name="elastic-pool-errors"></a>エラスティック プールのエラー

次のエラーは、エラスティック プールの作成と使用に関連しています。

| エラー コード | 重大度 | 説明 | 是正措置 |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |エラスティック プールが、その記憶域の上限に達しました。 エラスティック プールの記憶域の使用率が (%d) MB を超えることはできません。 エラスティック プールの記憶域が上限に達したときに、データベースにデータを書き込もうとしています。 リソース制限については、以下を参照してください。 <br/>&bull; &nbsp;[エラスティック プールに関する DTU ベースの制限](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[エラスティック プールに関する仮想コアベースの制限](resource-limits-vcore-elastic-pools.md) <br/> |可能であれば、エラスティック プールの DTU を増やすかストレージを追加して、その記憶域の上限を上げることを検討するか、エラスティック プール内の個々のデータベースで使用される記憶域を減らすか、あるいはエラスティック プールからデータベースを削除してください。 エラスティック プールのスケーリングについては、[エラスティック プールのリソースのスケーリング](elastic-pool-scale.md)に関する記事を参照してください。 データベースから未使用領域を削除する方法の詳細については、「[Azure SQL Database でデータベースのファイル領域を管理する](file-space-manage.md)」を参照してください。|
| 10929 | 16 |%s の最低限保証は %d、最大値は %d 、データベースの現在の使用状況は %d です。 ただし、サーバーは現在ビジー状態であり、このデータベースの %d を超える要求をサポートできません。 リソース制限については、以下を参照してください。 <br/>&bull; &nbsp;[エラスティック プールに関する DTU ベースの制限](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[エラスティック プールに関する仮想コアベースの制限](resource-limits-vcore-elastic-pools.md) <br/> その他の場合は、後でもう一度やり直してください。 データベースあたりの DTU/仮想コア最小値、データベースあたりの DTU/仮想コア最大値。 エラスティック プール内のすべてのデータベース間での同時実行ワーカー (要求) の合計数が、プールの制限を超えようとしました。 |可能であれば、エラスティック プールの DTU または仮想コアを増やしてワーカーの上限を上げることを検討するか、エラスティック プールからデータベースを削除してください。 |
| 40844 | 16 |サーバー '%ls' のデータベース '%ls' は、エラスティック プールの '%ls' エディションのデータベースであり、連続コピー リレーションシップを持つことはできません。  |該当なし |
| 40857 | 16 |サーバー '%ls ' のエラスティック プールが見つかりませんでした。エラスティック プール名は ' %ls ' です。 指定されたエラスティック プールが、指定されたサーバー内にありません。 | 有効なエラスティック プール名を指定してください。 |
| 40858 | 16 |エラスティック プール '%ls' は サーバー '%ls' に既に存在します。 指定されたエラスティック プールは、指定されたサーバー内に既に存在します。 | 新しいエラスティック プール名を指定してください。 |
| 40859 | 16 |エラスティック プールでは、サービス階層 '%ls' はサポートされていません。 指定されたサービス階層は、エラスティック プールのプロビジョニングにはサポートされていません。 |正しいエディションを指定するか、既定のサービス階層を使用する場合はサービス階層を空のままにしてください。 |
| 40860 | 16 |エラスティック プール '%ls' とサービス目標 '%ls' の組み合わせが正しくありません。 エラスティック プールとサービス レベルは、リソース タイプが 'ElasticPool' に指定されている場合にのみ同時に指定できます。 |エラスティック プールとサービス レベルの適切な組み合わせを指定してください。 |
| 40861 | 16 |データベース エディション '%.*ls' は、エラスティック プールのサービス層 '%.ls' と同じにする必要があります*。 データベースのエディションがサービス階層と異なります。 |エラスティック プールのサービス階層と異なるデータベース エディションを指定しないでください。  データベースのエディションを指定する必要がないことにご注意ください。 |
| 40862 | 16 |エラスティック プールのサービス目標が指定されている場合は、エラスティック プール名を指定する必要があります。 エラスティック プールのサービス目標は、エラスティック プールを一意に識別することはできません。 |エラスティック プールのサービス目標を使用する場合は、エラスティック プール名を指定してください。 |
| 40864 | 16 |エラスティック プールの DTU は、サービス階層 '%.*ls' に対して (%d) DTU 以上である必要があります。 最小値を下回るエラスティック プールの DTU を設定しようとしています。 |エラスティック プールの DTU を最小値以上に設定し直してください。 |
| 40865 | 16 |エラスティック プールの DTU は、サービス階層 '%.*ls' に対して (%d) DTU を超えることはできません。 エラスティック プールの DTU を最大値を超える値に設定しようとしています。 |エラスティック プールの DTU を最大値を超えないように設定し直してください。 |
| 40867 | 16 |データベースあたりの DTU の最大値は、サービス階層 '%.*ls' (%d) 以上である必要があります。 サポートされている制限を下回るデータベースあたりの DTU 最大値を設定しようとしています。 | 目的の設定をサポートするエラスティック プールのサービス階層を使用することをご検討ください。 |
| 40868 | 16 |データベースあたりの DTU の最大値は、サービス階層 '%.*ls' に対して (%d) を超えることはできません。 サポートされている制限を超えるデータベースあたりの DTU 最大値を設定しようとしています。 | 目的の設定をサポートするエラスティック プールのサービス階層を使用することをご検討ください。 |
| 40870 | 16 |データベースあたりの DTU 最小値は、サービス階層 '%.*ls' に対して (%d) を超えることはできません。 サポートされている制限を超えるデータベースあたりの DTU 最小値を設定しようとしています。 | 目的の設定をサポートするエラスティック プールのサービス階層を使用することをご検討ください。 |
| 40873 | 16 |データベース数 (%d) と データベースあたりの DTU の最小値 (%d) は、エラスティック プールの DTU (%d) を超えることはできません。 エラスティック プールの DTU を超えるエラスティック プール内のデータベースあたりの DTU 最小値を指定しようとしています。 | エラスティック プールの DTU を増やすか、データベースあたりの DTU 最小値を減らす、またはエラスティック プール内のデータベース数を減らすことをご検討ください。 |
| 40877 | 16 |データベースが含まれていない場合を除いて、エラスティック プールを削除できません。 エラスティック プールに、1 つ以上のデータベースが含まれているため、削除できません。 |エラスティック プールからデータベースを削除して、エラスティック プールを削除してください。 |
| 40881 | 16 |エラスティック プール '%.*ls' のデータベース数が上限に達しました。  エラスティック プールのデータベース数上限は、(%d) DTU を含むエラスティック プールの場合、(%d) を超えることはできません。 エラスティック プールのデータベース数が上限に達したときに、データベースを作成またはエラスティック プールにデータベースを追加しようとしています。 | 可能であれば、エラスティック プールの DTU を増やして、データベースの上限を上げることを検討するか、エラスティック プールからデータベースを削除してください。 |
| 40889 | 16 |エラスティック プール '%.*ls' の DTU または記憶域の上限を下げることはできません。下げると、データベースに十分な記憶域スペースを提供できなくなるためです。 記憶域の使用率を下回るエラスティック プールの記憶域の上限を下げようとしています。 | エラスティック プール内の個々のデータベースの記憶域使用率を減らすことを検討するか、その DTU または記憶域の上限を下げるためにプールからデータベースを削除してください。 |
| 40891 | 16 |データベースあたりの DTU 最小値 (%d) は、データベースあたりの DTU 最大値 (%d) を超えることはできません。 データベースあたりの DTU 最大値を超えるデータベースあたりの DTU 最小値を設定しようとしています。 |データベースあたりの DTU の最小値がデータベースあたりの DTU 最大値を超えていないことをご確認ください。 |
| TBD | 16 |エラスティック プール内の個々のデータベースの記憶域サイズは、サービス階層のエラスティック プール '%.*ls' で許可されている最大サイズを超えることはできません。 データベースの最大サイズが、エラスティック プールのサービス階層によって許可されている最大サイズを超えています。 |データベースの最大サイズを、エラスティック プールのサービス階層によって許可されている最大サイズの制限内に設定してください。 |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>このログインで要求されたデータベース "master" を開けません。 ログインに失敗しました

この問題は、アカウントに master データベースにアクセスするアクセス許可がないために発生します。 しかし、既定では、SQL Server Management Studio (SSMS) で master データベースへの接続が試行されます。

この問題を解決するには、次の手順に従ってください。

1. SSMS のログイン画面で **[オプション]** を選択してから、 **[接続プロパティ]** を選択します。
2. **[データベースに接続]** フィールドで、既定のログイン データベースとしてユーザーの既定のデータベース名を入力し、 **[接続]** を選択します。

   ![接続のプロパティ](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>エラーの原因が接続の問題かどうかを確認する

接続の問題が原因でエラーが発生しているかどうかを確認するには、次のような接続を開くための呼び出しを示すフレームのスタック トレースを確認します (**SqlConnection** クラスへの参照に注意)。

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

クエリの問題によって例外がトリガーされた場合、次のような呼び出し履歴が表示されます (**SqlCommand** クラスへの参照に注意)。 このような場合は、[クエリを調整](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)します。

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

パフォーマンスの微調整に関する詳細なガイダンスについては、以下のリソースを参照してください。

- [Azure SQL のインデックスと統計情報を管理する方法](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [Azure SQL Database でのクエリのパフォーマンスを手動でチューニングする](./performance-guidance.md)
- [動的管理ビューを使用して Azure SQL Database のパフォーマンスを監視する](./monitoring-with-dmvs.md)
- [Azure SQL Database でクエリ ストアを運用する](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)

## <a name="steps-to-fix-common-connection-issues"></a>一般的な接続に関する問題を修正するための手順

1. アプリケーション サーバーで TCP/IP がクライアント プロトコルとして有効になっていることを確認します。 詳細については、「[クライアント プロトコルの構成](/sql/database-engine/configure-windows/configure-client-protocols)」を参照してください。 SQL ツールがインストールされていないアプリケーション サーバーでは、**cliconfg.exe** (SQL Server クライアント ネットワーク ユーティリティ) を実行して、TCP/IP が有効になっていることを確認します。
2. アプリケーションの接続文字列を調べ、正しく構成されていることを確認します。 たとえば、接続文字列で正しいポート (1433) と完全修飾サーバー名が指定されていることを確認します。
「[接続情報の取得](./connect-query-ssms.md#get-server-connection-information)」をご覧ください。
3. 接続タイムアウトの値を増やしてみます。 30 秒以上の接続タイムアウトを使用することをお勧めします。
4. [SQL Server Management Studio (SSMS)](./connect-query-ssms.md)、UDL ファイル、ping、または telnet を使用して、アプリケーション サーバーと Azure SQL Database 間の接続をテストします。 詳細については、[接続問題のトラブルシューティング](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)と[接続問題の診断](./troubleshoot-common-connectivity-issues.md#diagnostics)に関するページをご覧ください。

   > [!NOTE]
   > トラブルシューティングの手順として、別のクライアント コンピューターでの接続をテストすることもできます。

5. ベスト プラクティスとして、再試行ロジックが実施されていることを確認します。 再試行ロジックの詳細については、[SQL Database に関する一時的な障害と接続エラーのトラブルシューティング](./troubleshoot-common-connectivity-issues.md)についての記事を参照してください。

これらの手順で問題が解決しない場合は、さらにデータを収集してから、サポートに連絡してみてください。 アプリケーションがクラウド サービスの場合は、ログ記録を有効にします。 この手順により、エラーの UTC タイムスタンプが返されます。 さらに、SQL Database によりトレース ID が返されます。 [Microsoft カスタマー サポート サービス](https://azure.microsoft.com/support/options/)はこの情報を使用できます。

ログ記録を有効にする方法の詳細については、「[Azure App Service でのアプリの診断ログの有効化](../../app-service/troubleshoot-diagnostic-logs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database 接続アーキテクチャ](./connectivity-architecture.md)
- [Azure SQL Database と Azure Synapse Analytics のネットワーク アクセスの制御](./network-access-controls-overview.md)