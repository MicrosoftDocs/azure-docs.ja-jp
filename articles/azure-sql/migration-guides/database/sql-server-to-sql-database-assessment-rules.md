---
title: SQL Server から SQL Database に移行するための評価ルール
description: Azure SQL Database に移行する前に対処する必要があるソース SQL Server インスタンスの問題を特定する評価ルール。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: bf825572226bf5d7432fd3ad825f2f3a13355c53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054154"
---
# <a name="assessment-rules-for-sql-server-to-sql-database-migration"></a>SQL Server から SQL Database に移行するための評価ルール
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

移行ツールでは、お使いのソース SQL Server インスタンスを多数の評価ルールを実行して検証し、お使いの SQL Server データベースを Azure SQL Database に移行する前に、解決する必要のある問題を特定します。 

この記事では、お使いの SQL Server データベースを Azure SQL Database に移行できるかどうかの評価に使用するルールの一覧を示します。 


## <a name="bulk-insert"></a>BULK INSERT<a id="BulkInsert"></a>

**タイトル: Azure SQL Database では、Azure BLOB 以外のデータ ソースでの BULK INSERT はサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
Azure SQL Database は、ファイル共有または Windows フォルダーにはアクセスできません。 Azure BLOB を参照しない BULK INSERT ステートメントの具体的な使用方法については、[影響を受けるオブジェクト] セクションを参照してください。 Azure BLOB Storage をソースとしない 'BULK INSERT' されたオブジェクトは、Azure SQL Database に移行後機能しません。 


**推奨事項**   
Azure SQL Database に移行するときには、BULK INSERT ステートメントでローカル ファイルまたはファイル共有が使用されている場合、代わりに Azure BLOB Storage のファイルを使用するよう変換する必要があります。 または、Azure 仮想マシン上の SQL Server に移行します。

## <a name="compute-clause"></a>COMPUTE 句<a id="ComputeClause"></a>

**タイトル: COMPUTE 句は廃止され、削除されました。**    
**カテゴリ**: 警告   

**説明**   
COMPUTE 句では、結果セットの最後に集計列として追加される総計が生成されます。 ただし、この句は Azure SQL Database ではサポートされなくなりました。 


**推奨事項**   
代わりに ROLLUP 演算子を使用して、T-SQL モジュールを書き直す必要があります。 以下のコードは、COMPUTE を ROLLUP に置き換える方法を示しています。 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>CDC (変更データ キャプチャ)<a id="CDC"></a>

**タイトル: Azure SQL Database では、CDC (変更データ キャプチャ) はサポートされていません**   
**カテゴリ**: 問題   


**説明**   
Azure SQL Database では、CDC (変更データ キャプチャ) はサポートされていません。 代わりに Change Tracking を使用できるか評価します。  または、Azure SQL Managed Instance か、Azure Virtual Machines 上の SQL Server に移行します。 


**推奨事項**   
Azure SQL Database では、CDC (変更データ キャプチャ) はサポートされていません。 代わりに Change Tracking を使用できるかどうか評価するか、Azure SQL Managed Instance に移行することを検討します。

詳細情報: [Azure SQL で Change Tracking を有効にする](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>CLR アセンブリ<a id="ClrAssemblies"></a>

**タイトル: Azure SQL Database では SQL CLR アセンブリはサポートされていません**   
**カテゴリ**: 問題   


**説明**   
Azure SQL Database では、SQL CLR アセンブリはサポートされていません。 


**推奨事項**   
現在、Azure SQL Database でこれを実現する方法はありません。 代替の解決策として、Azure SQL Database でサポートされるアセンブリのみを使用するようアプリケーション コードとデータベースを変更することが推奨されます。 あるいは、Azure SQL Managed Instance、または Azure Virtual Machines 上の SQL Server に移行します

詳細情報: [SQL Database でサポートされていない Transact-SQL の相違点](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>CRYPTOGRAPHIC PROVIDER<a id="CryptographicProvider"></a>

**タイトル: Azure SQL Database でサポートされない CREATE CRYPTOGRAPHIC PROVIDER または ALTER CRYPTOGRAPHIC PROVIDER の使用が検出されました**   
**カテゴリ**: 問題   

**説明**   
Azure SQL Database では、ファイルにアクセスできない CRYPTOGRAPHIC PROVIDER ステートメントはサポートされていません。 CRYPTOGRAPHIC PROVIDER ステートメントの具体的な使用方法については、[影響を受けるオブジェクト] セクションを参照してください。 Azure SQL Database に移行後、`CREATE CRYPTOGRAPHIC PROVIDER` または `ALTER CRYPTOGRAPHIC PROVIDER` が使用されたオブジェクトは正常に動作しません。  


**推奨事項**   
`CREATE CRYPTOGRAPHIC PROVIDER` または `ALTER CRYPTOGRAPHIC PROVIDER` が使用されたオブジェクトを確認します。 そのようなオブジェクトが必須の場合、これらの機能の使用を削除してください。 または、Azure 仮想マシン上の SQL Server に移行します

## <a name="cross-database-references"></a>データベース間参照<a id="CrossDataseReferences"></a>

**タイトル: Azure SQL Database では、複数のデータベースにまたがるクエリはサポートされていません**   
**カテゴリ**: 問題   

**説明**   
このサーバー上のデータベースでは、Azure SQL Database ではサポートされない、複数のデータベースにまたがるクエリが使用されています。 


**推奨事項**   
Azure SQL Database では、複数のデータベースにまたがるクエリはサポートされていません。 次のアクションを推奨します。 
- Azure SQL Database に依存データベースを移行し、エラスティック データベース クエリ (現時点ではプレビュー) 機能を使用して、複数の Azure SQL データベースにクエリを実行します。 
- 依存データセットを他のデータベースから、移行するデータベースに移動します。 
- Azure SQL Managed Instance に移行します。
- Azure Virtual Machines 上の SQL Server に移行します。 

詳細情報: [Azure SQL Database のエラスティック データベース クエリ (プレビュー) を参照](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>データベース互換<a id="DbCompatLevelLowerThan100"></a>

**タイトル: Azure SQL Database では、100 未満の互換性レベルはサポートされていません。**    
**カテゴリ**: 警告   

**説明**   
データベース互換レベルは、SQL Server データベース エンジンのアップグレードを可能にし、それと同時に、アップグレード前と同じデータベース互換レベルを維持することで接続するアプリケーションの機能的な状態を保つという点で、データベースの最新化支援に有益なツールです。 Azure SQL Database では、100 未満の互換性レベルはサポートされていません。 


**推奨事項**   
Azure SQL Managed Instance でデータベース互換レベルを 100 にアップグレードしたとき、アプリケーションの機能が損なわれないか評価します。 または、Azure 仮想マシン上の SQL Server に移行します

## <a name="database-mail"></a>データベース メール<a id="DatabaseMail"></a>

**タイトル: Azure SQL Database では、データベース メールはサポートされていません。**    
**カテゴリ**: 警告   

**説明**   
このサーバーは、Azure SQL Database ではサポートされていないデータベース メール機能を使用しています。


**推奨事項**   
データベース メールをサポートする Azure SQL Managed Instance に移行することを検討します。  または、Azure Functions と Sendgrid を使用して Azure SQL Database でメール機能を実現することを検討します。

詳細情報: [Azure Functions のスクリプトを使用して Azure SQL Database から電子メールを送信する](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>データベース プリンシパルの別名<a id="DatabasePrincipalAlias"></a>

**タイトル: SYS.DATABASE_PRINCIPAL_ALIASES は廃止され、削除されました。**    
**カテゴリ**: 問題   

**説明**   
Azure SQL Database では、SYS.DATABASE_PRINCIPAL_ALIASES は廃止され、削除されました。  


**推奨事項**   
別名の代わりにロールを使用してください。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK オプション<a id="DisableDefCNSTCHK"></a>

**タイトル: SET オプション DISABLE_DEF_CNST_CHK は廃止され、削除されました。**    
**カテゴリ**: 問題   

**説明**   
Azure SQL Database で、SET オプション DISABLE_DEF_CNST_CHK は廃止され、削除されました。  


詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW ヒント<a id="FastFirstRowHint"></a>

**タイトル: FASTFIRSTROW クエリ ヒントは廃止され、削除されました。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Database で FASTFIRSTROW クエリ ヒントは廃止され、削除されました。  


**推奨事項**   
FASTFIRSTROW クエリ ヒントの代わりに OPTION (FAST n) を使用します。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**タイトル: Azure SQL Database では、FileStream はサポートされていません**   
**カテゴリ**: 問題   

**説明**   
Azure SQL Database では、NTFS ファイル システムに、テキスト ドキュメント、画像、動画などの構造化されていないデータを格納できる Filestream 機能はサポートされていません。 


**推奨事項**   
構造化されていないファイルは Azure Blob Storage にアップロードし、これらのファイルに関連するメタデータ (名前、種類、URL の場所、ストレージ キーなど) は Azure SQL Database に格納します。 Azure SQL Database との間で BLOB をストリーミングできるよう、お使いのアプリケーションを再設計する必要がある場合があります。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL との間での BLOB のストリーミングに関するブログ](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>リンク サーバー<a id="LinkedServer"></a>

**タイトル: Azure SQL Database では、リンク サーバー機能はサポートされていません**   
**カテゴリ**: 問題   

**説明**   
リンク サーバーを使用すると、SQL Server のインスタンス外の OLE DB データ ソースに対して、SQL Server データベース エンジンでコマンドを実行できます。 


**推奨事項**   
Azure SQL Database では、リンク サーバー機能はサポートされていません。 リンク サーバーが必要なくなるようにするには、次のアクションが推奨されます。 
- リモートの SQL サーバーから依存データベースを特定し、これらを移行するデータベースに移動することを検討します。 
- Azure に依存データベースを移行し、エラスティック データベース クエリ (プレビュー) 機能を使用して、複数の Azure SQL データベースにクエリを実行できるようにします。 

詳細情報: [Azure SQL Database エラスティック クエリ (プレビュー) を参照](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**タイトル: Azure SQL Database では、BEGIN DISTRIBUTED TRANSACTION はサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
Azure SQL Database では、Transact SQL BEGIN DISTRIBUTED TRANSACTION によって開始され、MSDTC (Microsoft 分散トランザクション コーディネーター) で管理される分散トランザクションはサポートされていません。  


**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、BEGIN DISTRUBUTED TRANSACTION を使用するすべてのオブジェクトを確認します。 複数のインスタンス間での分散トランザクションがサポートされている (現在、プレビュー段階) Azure SQL Managed Instance に参加しているデータベースを移行することを検討してください。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL Managed Instance の複数のサーバーにまたがるトランザクション](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (BULK)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**タイトル: Azure SQL Database では、Azure Blob Storage 以外のデータ ソースを使用した一括操作で使用される OpenRowSet はサポートされていません。**    
**カテゴリ**: 問題   

**説明** OPENROWSET では、ファイルのデータを読み込み、行セットとして返す、組み込みの BULK プロバイダーでの一括操作がサポートされています。 Azure SQL Database では、Azure Blob Storage 以外のデータ ソースでの OPENROWSET はサポートされていません。


**推奨事項**   
Azure SQL Database はファイル共有と Windows フォルダーにはアクセスできないので、Azure Blob Storage からファイルをインポートする必要があります。 そのため、OPENROWSET 関数でサポートされる BLOB の型は DATASOURCE のみとなります。 または、Azure 仮想マシン上の SQL Server に移行します

詳細情報: [SQL Database への移行時に Transact-SQL の相違点を解決する](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (プロバイダー)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**タイトル: Azure SQL Database では、OpenRowSet は SQL または SQL 以外のプロバイダーではサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
SQL または SQL 以外のプロバイダーでの OpenRowSet は、リンク サーバー内のテーブルにアクセスする代替手段であり、OLE DB を使用してリモート データに接続しアクセスする臨時の手段です。 Azure SQL Database では、OpenRowSet は SQL または SQL 以外のプロバイダーでサポートされていません。


**推奨事項**   
Azure SQL Database では、OPENROWSET を Azure BLOB Storage からのインポートのみでサポートしています。 または、Azure 仮想マシン上の SQL Server に移行します

詳細情報: [SQL Database への移行時に Transact-SQL の相違点を解決する](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>ANSI 以外での左外部結合<a id="NonANSILeftOuterJoinSyntax"></a>

**タイトル: ANSI 以外のスタイルでの左外部結合は廃止され、削除されました。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Database では、ANSI 以外のスタイルでの左外部結合は廃止され、削除されました。 


**推奨事項**   
ANSI 結合構文を使用してください。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>ANSI 以外での右外部結合<a id="NonANSIRightOuterJoinSyntax"></a>

**タイトル: ANSI 以外のスタイルでの右外部結合は廃止され、削除されました。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Database では、ANSI 以外のスタイルでの右外部結合は廃止され、削除されました。 


**推奨事項**   
ANSI 結合構文を使用してください。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Next 列<a id="NextColumn"></a>

**タイトル: Azure SQL Database では、NEXT という名前のテーブルと列があると、エラーが発生します。**    
**カテゴリ**: 問題   

**説明**   
NEXT という名前のテーブルまたは列が検出されました。 Microsoft SQL Server で導入されたシーケンスでは、ANSI 標準の NEXT VALUE FOR 関数が使用されます。 テーブルまたは列の名前が NEXT で、列の別名として VALUE が指定されている場合や、ANSI 標準の AS が省略されている場合、作成されたステートメントによってエラーが発生する可能性があります。  


**推奨事項**   
ステートメントを書き直し、テーブルまたは列の別名を指定するときに ANSI 標準の AS キーワードを使用してください。 たとえば、列の名前が NEXT で、その列に VALUE という別名が付けられている場合、クエリ `SELECT NEXT VALUE FROM TABLE` でエラーが発生するため、SELECT NEXT AS VALUE FROM TABLE に書き直す必要があります。 同様に、テーブルの名前が NEXT で、その別名が VALUE である場合、`SELECT Col1 FROM NEXT VALUE` のクエリでは、エラーが発生するので、`SELECT Col1 FROM NEXT AS VALUE` に書き直す必要があります。

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**タイトル: レガシ スタイルの RAISERROR 呼び出しは、それに相当する最新のものに置き換える必要があります。**    
**カテゴリ**: 警告   

**説明**   
次の例のような RAISERROR 呼び出しは、コンマとかっこが含まれないため、レガシ スタイルと呼ばれます。 `RAISERROR 50001 'this is a test'`. Azure SQL Database では、RAISERROR を呼び出すこのメソッドは廃止され、削除されました。 


**推奨事項**   
現在の RAISERROR 構文を使用してステートメントを書き直すか、最新のアプローチの `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` を使用できるかどうか評価します。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>サーバー監査<a id="ServerAudits"></a>

**タイトル: Azure SQL Database の監査機能をサーバー監査の変わりに使用します**   
**カテゴリ**: 警告   

**説明**   
Azure SQL Database では、サーバー監査はサポートされていません。


**推奨事項**   
サーバー監査の変わりに、Azure SQL Database の監査機能を検討します。  Azure SQL では、SQL Server よりも機能豊富な、監査がサポートされています。 Azure SQL Database では、データへのアクセス、スキーマ変更 (DDL)、データ変更 (DML)、アカウント、ロール、アクセス許可 (DCL、セキュリティ例外) など、さまざまなデータベース アクションとイベントを監査できます。 組織が Azure SQL Database Auditing を利用すると、データに対する更新やクエリなど、データベース内で発生するイベントや変更に関する分析情報を取得する能力が向上します。 または、Azure SQL Managed Instance、または Azure Virtual Machines 上の SQL Server に移行します。

詳細情報: [Azure SQL Database の監査 ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>サーバー資格情報<a id="ServerCredentials"></a>

**タイトル: Azure SQL Database では、サーバー スコープの資格情報はサポートされていません**   
**カテゴリ**: 警告   

**説明**   
資格情報とは、SQL Server 外のリソースへの接続に必要な認証情報 (資格情報) を含むレコードです。 Azure SQL Database ではデータベースの資格情報はサポートされていますが、SQL Server のスコープで作成されたものはサポートされていません。   


**推奨事項**   
Azure SQL Database では、データベース スコープの資格情報がサポートされています。 サーバー スコープの資格情報を、データベース スコープの資格情報に変換します。 あるいは、Azure SQL Managed Instance、または Azure Virtual Machines 上の SQL Server に移行します

詳細情報: [データベース スコープの資格情報を作成する](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**タイトル: Azure SQL Database では、Service Broker 機能はサポートされていません**   
**カテゴリ**: 問題   

**説明**   
SQL Server Service Broker では、SQL Server データベース エンジンのメッセージングおよびキューイング アプリケーションをネイティブでサポートしています。 Azure SQL Database では、Service Broker 機能はサポートされていません。


**推奨事項**   
Azure SQL Database では、Service Broker 機能はサポートされていません。 同じインスタンス内で Service Broker をサポートする Azure SQL Managed Instance に移行することを検討します。 または、Azure 仮想マシン上の SQL Server に移行します。 

## <a name="server-scoped-triggers"></a>サーバー スコープ トリガー<a id="ServerScopedTriggers"></a>

**タイトル: Azure SQL Database では、サーバー スコープのトリガーはサポートされていません**   
**カテゴリ**: 警告   

**説明**   
トリガーとは、データの挿入、削除、更新など、テーブルに対する特定のアクションに応答して実行される、特殊な種類のストアド プロシージャです。 Azure SQL Database では、サーバー スコープのトリガーはサポートされていません。 Azure SQL Database では、トリガーの次のオプションはサポートされていません。FOR LOGON、ENCRYPTION、WITH APPEND、NOT FOR REPLICATION、EXTERNAL NAME オプション (外部メソッドのサポートはありません)、ALL SERVER オプション (DDL トリガー)、LOGON イベントに対するトリガー (ログオン トリガー)。Azure SQL Database では、CLR トリガーはサポートされていません。


**推奨事項**   
代わりにデータベース レベルのトリガーを使用します。 あるいは、Azure SQL Managed Instance、または Azure Virtual Machines 上の SQL Server に移行します

詳細情報: [SQL Database への移行時に Transact-SQL の相違点を解決する](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>SQL エージェント ジョブ<a id="AgentJobs"></a>

**タイトル: Azure SQL Database では、SQL Server エージェントのジョブは利用できません**   
**カテゴリ**: 警告   

**説明**   
SQL Server エージェントは、SQL Server でジョブと呼ばれる、管理タスクをスケジュールに従って実行する Microsoft Windows のサービスです。 Azure SQL Database では、SQL Server エージェントのジョブは使用できません。 


**推奨事項** Azure SQL Database で SQL Server エージェント ジョブの代わりとなるエラスティック ジョブ (プレビュー) を使用します。 Azure SQL Database の自動で再試行され、最終的な完了が保証されているエラスティック データベース ジョブでは、複数のデータベースに対して T-SQL スクリプトを確実に実行できます。 または、Azure SQL Managed Instance、または Azure Virtual Machines 上の SQL Server に移行することを検討します。

詳細情報: [エラスティック データベース ジョブ (プレビュー) の概要](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>SQL Database のサイズ<a id="SQLDBDatabaseSize"></a>

**タイトル: Azure SQL Database では、100 TB を超えるデータベース サイズはサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
データベースのサイズが、サポートされる最大サイズの 100 TB を超えています。 


**推奨事項**   
データをアーカイブまたは圧縮できるか、または複数のデータベースにシャード化できるか評価します。 または、Azure 仮想マシン上の SQL Server に移行します。 

詳細情報: [仮想コアのリソースの制限](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**タイトル: SQL Mail は廃止されました。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Database で、SQL Mail は廃止され、削除されました。


**推奨事項**   
Azure SQL Managed Instance、または Azure Virtual Machines 上の SQL Server に移行し、データベース メールを使用することを検討します。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**タイトル: Azure SQL Database で使用できない削除されたシステム ストアド プロシージャを参照するステートメントが検出されました。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Database では、`sp_dboption`、`sp_addserver`、`sp_dropalias`、`sp_activedirectory_obj`、`sp_activedirectory_scp`、`sp_activedirectory_start` のサポート対象外のシステムおよび拡張ストアド プロシージャは使用できません。


**推奨事項**    
Azure SQL Database で削除されたサポート対象外のシステム プロシージャに対する参照を削除します。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>トレース フラグ<a id="TraceFlags"></a>

**タイトル: Azure SQL Database では、トレース フラグはサポートされていません**   
**カテゴリ**: 警告   

**説明**   
トレース フラグは、一時的にサーバー固有の特性を設定したり、特定の動作をオフにしたりするときに使用します。 トレース フラグは、パフォーマンスの問題を診断したり、ストアド プロシージャや複雑なシステムをデバッグするときによく使用されます。 Azure SQL Database では、トレース フラグはサポートされていません。 


**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、Azure SQL Database でサポートされていないすべてのトレース フラグを確認し、それらを削除できるかどうかを評価します。 また、限られた数のグローバル トレース フラグをサポートする Azure SQL Managed Instance や、Azure 仮想マシン上の SQL Server に移行することもできます。

詳細情報: [SQL Database への移行時に Transact-SQL の相違点を解決する](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Windows 認証<a id="WindowsAuthentication"></a>

**タイトル: Azure SQL Database では、Windows 認証 (統合セキュリティ) にマップされたデータベース ユーザーはサポートされていません。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Database では、次の 2 種類の認証がサポートされています。 
- SQL 認証: ユーザー名とパスワードを使用します 
- Azure Active Directory 認証: Azure Active Directory で管理される ID を使用し、管理および統合されたドメインでサポートされています。 

Azure SQL Database では、Windows 認証 (統合セキュリティ) にマップされたデータベース ユーザーはサポートされていません。 



**推奨事項**   
ローカル Active Directory を Azure Active Directory とフェデレーションします。 その後、Windows ID を同等の Azure Active Directory ID に置き換えることができます。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [SQL Database のセキュリティ機能](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**タイトル: Azure SQL Database では、xp_cmdshell はサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
Azure SQL Database では、Windows コマンド シェルを生成し、実行用の文字列を渡す xp_cmdshell はサポートされていません。 


**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、xp_cmdshell を使用してすべてのオブジェクトを確認し、xp_cmdshell または影響を受けるオブジェクトに対する参照を削除できるか評価します。 クラウドベースの自動化と構成サービスを提供する Azure Automation を調査することも検討します。 または、Azure 仮想マシン上の SQL Server に移行します。 

## <a name="next-steps"></a>次の手順

Azure SQL Database への SQL Server の移行を開始するには、[SQL Server から SQL Database への移行ガイド](sql-server-to-sql-database-guide.md)を参照してください。

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクに役立つ Microsoft とサードパーティ製のサービスとツールの一覧については、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- SQL Database の詳細については、以下を参照してください。
   - [Azure SQL Database の概要](../../database/sql-database-paas-overview.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 

- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- アプリケーション アクセス層を評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。
