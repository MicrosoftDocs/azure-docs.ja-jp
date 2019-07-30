---
title: ソース データベースへの Azure Database Migration Service の接続に関連した既知の問題/エラーのトラブルシューティングに関する記事 | Microsoft Docs
description: ソース データベースへの Azure Database Migration Service の接続に関連した既知の問題/エラーをトラブルシューティングする方法について学習します。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462559"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>ソース データベースへの接続時の DMS エラーのトラブルシューティング

次の記事では、ソース データベースへの Azure Database Migration Service (DMS) の接続時に発生する可能性がある潜在的な問題に対処する方法について詳しく説明します。 以下の各セクションは特定の種類のソース データベースに関連しており、接続のトラブルシューティングを行う方法についての情報へのリンクおよび詳細と共に、発生する可能性のあるエラーがリストされています。

## <a name="sql-server"></a>SQL Server

ソースの SQL Server データベースへの接続に関する潜在的な問題と、それらの対処方法が以下の表に示されています。

| Error         | 原因とトラブルシューティングの詳細 |
| ------------- | ------------- |
| SQL 接続できませんでした。 SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからないかアクセスできません。 インスタンス名が正しいことと、SQL Server がリモート接続を許可するように構成されていることを確認してください。<br> | このエラーは、サービスでソース サーバーを見つけられない場合に発生します。 問題に対処する場合は、「[動的ポートまたは名前付きインスタンスを使用しているときのソース SQL Server への接続エラー](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)」を参照してください。 |
| **エラー 53** -1 - SQL 接続できませんでした。 (また、エラー コード 1、2、5、53、233、258、1225、11001 の場合)<br><br> | このエラーは、サービスでソース サーバーに接続できない場合に発生します。 問題に対処する場合は、以下のリソースを参照してからもう一度試します。 <br><br>  [接続の問題のトラブルシューティングを行うための対話型ユーザー ガイド](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [SQL Server から Azure SQL Database への移行の前提条件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [SQL Server から Azure SQL Database Managed Instance への移行の前提条件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **エラー 18456** - ログインできませんでした。<br> | このエラーは、サービスで指定された T-SQL 資格情報を使用して、ソース データベースに接続できない場合に発生します。 問題に対処するには、入力された資格情報を確認します。 また、「[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)」、あるいはこの表の下の注釈にリストされているトラブルシューティングのドキュメントを参照してから、もう一度試すことができます。 |
| 無効な AccountName 値 '{0}' が指定されました。 AccountName に必要な形式は、DomainName\UserName です<br> | このエラーは、ユーザーが Windows 認証を選択したが、無効な形式でユーザー名を指定した場合に発生します。 問題に対処するには、Windows 認証の正しい形式でユーザー名を指定するか、 **[SQL 認証]** を選択します。 |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

ソースの AWS RDS MySQL データベースへの接続に関する潜在的な問題と、それらの対処方法が以下の表に示されています。

| Error         | 原因とトラブルシューティングの詳細 |
| ------------- | ------------- |
| **エラー [2003]** [HY000] - 接続できませんでした。 エラー [HY000] [MySQL] [ODBC x.x(w) driver] '{server}' (10060) の MySQL サーバーに接続できません | このエラーは、MySQL ODBC ドライバーをソース サーバーに接続できない場合に発生します。 問題に対処する場合は、この表の下の注釈にリストされているトラブルシューティングのドキュメントを参照してから、もう一度試してください。<br> |
| **エラー [2005]** [HY000] - 接続できませんでした。 エラー [HY000] [MySQL] [ODBC x.x(w) driver] 不明な MySQL サーバーのホスト '{server}' | このエラーは、サービスで RDS のソース ホストを見つけられない場合に発生します。 この問題の原因として、リストされているソースが存在しないか、RDS インフラストラクチャに問題があることが考えられます。 問題に対処する場合は、この表の下の注釈にリストされているトラブルシューティングのドキュメントを参照してから、もう一度試してください。<br> |
| **エラー [1045]** [HY000] - 接続できませんでした。 エラー [HY000] [MySQL] [ODBC x.x(w) driver] ユーザー '{user}'@'{server}' に対してアクセスが拒否されました (パスワードを使用:はい) | このエラーは、資格情報が無効であるため、MySQL ODBC ドライバーをソース サーバーに接続できない場合に発生します。 入力した資格情報を確認します。 問題が解決しない場合は、そのソース コンピューターの資格情報が正しいことを確認します。 コンソールでのパスワードのリセットが必要になる場合があります。 それでも問題が発生する場合は、この表の下の注釈にリストされているトラブルシューティングのドキュメントを参照してから、もう一度試してください。<br> |
| **エラー [9002]** [HY000] - 接続できませんでした。 エラー [HY000] [MySQL] [ODBC x.x(w) driver] 接続文字列が正しくない可能性があります。 参照用のポータルにアクセスしてください。| このエラーは、接続文字列の問題により、接続に失敗している場合に発生します。 指定された接続文字列が有効であることを確認します。 問題に対処する場合は、この表の下の注釈にリストされているトラブルシューティングのドキュメントを参照してから、もう一度試してください。<br> |
| **バイナリ ログにエラーがあります。変数 binlog_format に値 '{value}' が指定されています。その値を 'row' に変更してください。** | このエラーは、バイナリ ログにエラーがある場合に発生します。変数 binlog_format の値が間違っています。 問題に対処するには、パラメーター グループの binlog_format を 'ROW' に変更してから、インスタンスを再起動します。 詳細については、「[バイナリ ログのオプションと変数](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html)」または [AWS RDS MySQL データベース ログ ファイルに関するドキュメント](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)を参照してください。<br> |

> [!NOTE]
> ソースの AWS RDS MySQL データベースへの接続に関する問題のトラブルシューティングについて詳しくは、以下のリソースを参照してください。
> * [Amazon RDS 接続に関する問題のトラブルシューティング](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Amazon RDS データベース インスタンスへの接続の問題を解決する方法を教えてください。](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

ソースの AWS RDS PostgreSQL データベースへの接続に関する潜在的な問題と、それらの対処方法が以下の表に示されています。

| Error         | 原因とトラブルシューティングの詳細 |
| ------------- | ------------- |
| **エラー [101]** [08001] - 接続できませんでした。 エラー [08001] タイムアウトしました。 | このエラーは、Postgres ドライバーをソース サーバーに接続できない場合に発生します。 問題に対処する場合は、この表の下の注釈にリストされているトラブルシューティングのドキュメントを参照してから、もう一度試してください。 |
| **エラー:パラメーター wal_level の値は '{value}' です。レプリケーションを許可するには、'logical' に変更してください。** | このエラーは、パラメーター wal_level の値が間違っている場合に発生します。 問題に対処するには、パラメーター グループの rds.logical_replication を 1 に変更してから、インスタンスを再起動します。 詳細については、[DMS を使用して Azure PostgreSQL に移行するための前提条件](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites)に関する記事、または「[Amazon RDS 上の PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)」を参照してください。 |

> [!NOTE]
> ソースの AWS RDS PostgreSQL データベースへの接続に関する問題のトラブルシューティングについて詳しくは、以下のリソースを参照してください。
> * [Amazon RDS 接続に関する問題のトラブルシューティング](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Amazon RDS データベース インスタンスへの接続の問題を解決する方法を教えてください。](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

ソースの AWS RDS SQL Server データベースへの接続に関する潜在的な問題と、それらの対処方法が以下の表に示されています。

| Error         | 原因とトラブルシューティングの詳細 |
| ------------- | ------------- |
| **エラー 53** -1 - SQL 接続できませんでした。 SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからないかアクセスできません。 インスタンス名が正しいことと、SQL Server がリモート接続を許可するように構成されていることを確認してください。 (プロバイダー:名前付きパイプ プロバイダー、エラー:40 - SQL Server への接続を開けませんでした | このエラーは、サービスでソース サーバーに接続できない場合に発生します。 問題に対処する場合は、この表の下の注釈にリストされているトラブルシューティングのドキュメントを参照してから、もう一度試してください。 |
| **エラー 18456** - ログインできませんでした。 ユーザー '{user}' はログインできませんでした | このエラーは、指定された T-SQL 資格情報を使用して、サービスをソース データベースに接続できない場合に発生します。 問題に対処するには、入力された資格情報を確認します。 また、「[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)」、あるいはこの表の下の注釈にリストされているトラブルシューティングのドキュメントを参照し、もう一度試すことができます。 |
| **エラー 87** - 接続文字列が無効です。 SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからないかアクセスできません。 インスタンス名が正しいことと、SQL Server がリモート接続を許可するように構成されていることを確認してください。 (プロバイダー:SQL ネットワーク インターフェイス。エラー: 25 - 接続文字列が無効です) | このエラーは、接続文字列が無効であるため、サービスをソース サーバーに接続できない場合に発生します。 問題に対処するには、指定された接続文字列を確認します。 問題が解決しない場合は、この表の下の注釈にリストされているトラブルシューティングのドキュメントを参照してから、もう一度試してください。 |
| **エラー - サーバー証明書は信頼されていません。** サーバーとの接続を正常に確立しましたが、ログイン中にエラーが発生しました。 (プロバイダー:SSL プロバイダー、エラー:0 - この証明書チェーンは、信頼されていない機関によって発行されました。) | このエラーは、使用された証明書が信頼されていない場合に発生します。 問題に対処するには、信頼できない証明書を見つけてから、サーバーに対してそれを有効にする必要があります。 また、接続中に信頼証明書のオプションを選択することもできます。 使用する証明書についてよく理解しており、それを信頼している場合にのみ、このアクションを実行します。 <br> 自己署名証明書を使用して暗号化されている SSL 接続のセキュリティは強力ではありません - 中間者攻撃の影響を受けやすくなります。 運用環境や、インターネットに接続されているサーバーでは、自己署名証明書を使用する SSL に依存しないでください。 <br> 詳細については、「[Microsoft SQL Server DB インスタンスでの SSL の使用](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html)」または[チュートリアル:DMS を使用した Azure への RDS SQL Server の移行](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)に関するページを参照してください。 |
| **エラー 300** - ユーザーには必要なアクセス許可がありません。 VIEW SERVER STATE 権限がオブジェクト '{server}' で拒否されました (データベース '{database}') | このエラーは、ユーザーに移行を行う権限がない場合に発生します。 問題に対処する場合は、[GRANT (サーバーの権限) - Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) に関するページ、または[チュートリアル:DMS を使用した Azure への RDS SQL Server の移行](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)に関するページを参照し、詳細を確認してください。 |

> [!NOTE]
> ソースの AWS RDS SQL Server への接続に関する問題のトラブルシューティングについて詳しくは、以下のリソースを参照してください。
>
> * [SQL Server への接続エラーを解決する](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Amazon RDS データベース インスタンスへの接続の問題を解決する方法を教えてください。](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>既知の問題

* [Azure SQL Database へのオンライン移行に関する既知の問題と移行の制限事項](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Azure Database for MySQL へのオンライン移行に関する既知の問題と移行の制限事項](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Azure Database for PostgreSQL へのオンライン移行に関する既知の問題と移行の制限事項](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>次の手順

* [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration) に関する記事を確認する。
* 「[Azure portal を使用して Azure Database for MySQL のサーバー パラメーターを構成する方法](https://docs.microsoft.com/azure/mysql/howto-server-parameters)」を確認する。
* 「[Azure Database Migration Service を使用するための前提条件の概要](https://docs.microsoft.com/azure/dms/pre-reqs)」を確認する。
* 「[Azure Database Migration Service の使用に関する FAQ](https://docs.microsoft.com/azure/dms/faq)」を確認する。
