---
title: SQL Server から SQL Managed Instance への移行に関する評価ルール
description: Azure SQL Managed Instance に移行する前に、対処する必要があるソース SQL Server インスタンスに関する問題を特定するための評価ルール。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: 760a6496ff297ae6328810589f780b430d55b18a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054151"
---
# <a name="assessment-rules-for-sql-server-to-sql-managed-instance-migration"></a>SQL Server から SQL Managed Instance への移行に関する評価ルール
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

移行ツールでは、SQL Server データベースを Azure SQL Managed Instance に移行する前に対処する必要がある問題を特定するためにいくつかの評価ルールを実行して、ソース SQL Server インスタンスを検証します。 

この記事では、SQL Server データベースを Azure SQL Managed Instance に移行する実現可能性を評価するために使用されるルールの一覧を示します。 

## <a name="analysiscommand-job"></a>AnalysisCommand ジョブ<a id="AnalysisCommandJob"></a>

**タイトル: AnalysisCommand ジョブ ステップは Azure SQL Managed Instance ではサポートされていません。**    
**カテゴリ**: 警告   


**説明**   
これは、Analysis Services コマンドを実行するジョブ ステップです。 AnalysisCommand ジョブ ステップは Azure SQL Managed Instance ではサポートされていません。

**推奨事項**     
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、Analysis Service コマンド ジョブ ステップを使用してすべてのジョブを表示し、ジョブ ステップまたは影響を受けるオブジェクトを削除できるかどうかを評価します。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL Managed Instance での SQL Server エージェントの相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>AnalysisQuery ジョブ<a id="AnalysisQueryJob"></a>

**タイトル: AnalysisQuery ジョブ ステップは Azure SQL Managed Instance ではサポートされていません。**    
**カテゴリ**: 警告   

**説明**   
これは、Analysis Services クエリを実行するジョブ ステップです。 AnalysisQuery ジョブ ステップは Azure SQL Managed Instance ではサポートされていません。


**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、Analysis Service クエリ ジョブ ステップを使用してすべてのジョブを表示し、ジョブ ステップまたは影響を受けるオブジェクトを削除できるかどうかを評価します。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL Managed Instance での SQL Server エージェントの相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>ファイルからのアセンブリ<a id="AssemblyFromFile"></a>

**タイトル: Azure SQL Managed Instance では、ファイル パラメーターが指定されている 'CREATE ASSEMBLY' および 'ALTER ASSEMBLY' はサポート対象外です。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Managed Instance では、ファイル共有または Windows フォルダーにアクセスできません。 Azure BLOB を参照しない BULK INSERT ステートメントの具体的な使用方法については、[影響を受けるオブジェクト] セクションを参照してください。 Azure Blob Storage をソースとしない 'BULK INSERT' 使用するオブジェクトは、Azure SQL Managed Instance への移行後は機能しません。


**推奨事項**   
Azure SQL Managed Instance に移行するときには、BULK INSERT ステートメントでローカル ファイルまたはファイル共有を使用している場合、代わりに Azure Blob Storage のファイルを使用するように変換する必要があります。 または、Azure 仮想マシン上の SQL Server に移行します。 

詳細情報: [Azure SQL Managed Instance での CLR の相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Bulk insert<a id="BulkInsert"></a>

**タイトル: Azure BLOB 以外のデータ ソースの BULK INSERT は、Azure SQL Managed Instance ではサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
Azure SQL Managed Instance では、ファイル共有または Windows フォルダーにアクセスできません。 Azure BLOB を参照しない BULK INSERT ステートメントの具体的な使用方法については、[影響を受けるオブジェクト] セクションを参照してください。 Azure Blob Storage をソースとしない 'BULK INSERT' 使用するオブジェクトは、Azure SQL Managed Instance への移行後は機能しません。


**推奨事項**   
Azure SQL Managed Instance に移行するときには、BULK INSERT ステートメントでローカル ファイルまたはファイル共有を使用している場合、代わりに Azure Blob Storage のファイルを使用するように変換する必要があります。

詳細情報: [Azure SQL Managed Instance での BULK INSERT と OPENROWSET の相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>CLR のセキュリティ<a id="ClrStrictSecurity"></a>

**タイトル: SAFE または EXTERNAL_ACCESS とマークされた CLR アセンブリは UNSAFE と見なされる**   
**カテゴリ**: 問題   

**説明**   
Azure SQL Managed Instance には CLR Strict Security モードが適用されます。 このモードは既定で有効になり、SAFE または EXTERNAL_ACCESS のいずれかのマークが付けられたユーザー定義の CLR アセンブリを含むデータベースで破壊的変更が発生します。


**推奨事項**   
CLR では、セキュリティ境界としてサポートされなくなった、.NET Framework のコード アクセス セキュリティ (CAS) が使用されます。 SQL Server 2017 (14.x) データベース エンジン以降では、CLR アセンブリのセキュリティを強化するために、clr strict security という `sp_configure` オプションが導入されています。 clr strict security は既定で有効になり、SAFE および EXTERNAL_ACCESS CLR アセンブリは UNSAFE とマークされている場合と同じように扱われます。 clr strict security が無効になっていると、PERMISSION_SET = SAFE で作成された CLR アセンブリで、外部のシステム リソースにアクセスし、アンマネージド コードを呼び出し、sysadmin 特権を取得できる場合があります。 厳密なセキュリティを有効にした場合、未署名のアセンブリの読み込みは失敗します。 また、データベースに SAFE または EXTERNAL_ACCESS アセンブリがある場合、RESTORE または ATTACH DATABASE ステートメントは完了できますが、アセンブリの読み込みに失敗するおそれがあります。 アセンブリを読み込むには、各アセンブリを変更または削除して再作成し、サーバーでの UNSAFE ASSEMBLY アクセス許可のある対応するログインを含む証明書または非対称キーで署名されるようにする必要があります。

詳細情報: [CLR の厳密なセキュリティ](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>COMPUTE 句<a id="ComputeClause"></a>

**タイトル: COMPUTE 句は廃止され、削除されました。**    
**カテゴリ**: 警告   

**説明**   
COMPUTE 句では、結果セットの最後に集計列として追加される総計が生成されます。 しかし、この句は Azure SQL Managed Instance ではサポートされなくなりました。



**推奨事項**   
代わりに ROLLUP 演算子を使用して、T-SQL モジュールを書き直す必要があります。 以下のコードは、COMPUTE を ROLLUP に置き換える方法を示しています。 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>CRYPTOGRAPHIC PROVIDER<a id="CryptographicProvider"></a>

**タイトル: Azure SQL Managed Instance ではサポートされない CREATE CRYPTOGRAPHIC PROVIDER または ALTER CRYPTOGRAPHIC PROVIDER の使用が検出されました。**    
**カテゴリ**: 問題   

**説明**   
Azure SQL Managed Instance では、ファイルにアクセスできないため、CRYPTOGRAPHIC PROVIDER ステートメントをサポートしていません。 CRYPTOGRAPHIC PROVIDER ステートメントの具体的な使用方法については、[影響を受けるオブジェクト] セクションを参照してください。 'CREATE CRYPTOGRAPHIC PROVIDER' または 'ALTER CRYPTOGRAPHIC PROVIDER' を使用するオブジェクトは、Azure SQL Managed Instance への移行後は正しく機能しません。


**推奨事項**   
'CREATE CRYPTOGRAPHIC PROVIDER' または 'ALTER CRYPTOGRAPHIC PROVIDER' を使用するオブジェクトを確認します。 そのようなオブジェクトが必須の場合、これらの機能の使用を削除してください。 または、Azure 仮想マシン上の SQL Server に移行します。 

詳細情報: [Azure SQL Managed Instance での暗号化サービス プロバイダーの相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>データベース互換<a id="DbCompatLevelLowerThan100"></a>

**タイトル: 100 未満のデータベース互換レベルはサポートされていません**   
**カテゴリ**: 警告   

**説明**   
データベース互換レベルは、SQL Server データベース エンジンのアップグレードを可能にし、それと同時に、アップグレード前と同じデータベース互換レベルを維持することで接続するアプリケーションの機能的な状態を保つという点で、データベースの最新化支援に有益なツールです。 Azure SQL Managed Instance では、100 未満の互換レベルはサポートされません。 互換レベルが 100 未満のデータベースを Azure SQL Managed Instance で復元すると、その互換レベルは 100 にアップグレードされます。 


**推奨事項**...Azure SQL Managed Instance でデータベース互換レベルを 100 にアップグレードしたとき、アプリケーションの機能が損なわれないか評価します。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL Managed Instance でサポートされている互換レベル ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>データベース プリンシパルの別名<a id="DatabasePrincipalAlias"></a>

**タイトル: SYS.DATABASE_PRINCIPAL_ALIASES は廃止され、削除されました。**    
**カテゴリ**: 問題   

**説明**   
Azure SQL Managed Instance では、SYS.DATABASE_PRINCIPAL_ALIASES は廃止され、削除されました。


**推奨事項**   
別名の代わりにロールを使用してください。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK オプション<a id="DisableDefCNSTCHK"></a>

**タイトル: SET オプション DISABLE_DEF_CNST_CHK は廃止され、削除されました。**    
**カテゴリ**: 問題   

**説明**   
Azure SQL Managed Instance では、SET オプション DISABLE_DEF_CNST_CHK は廃止され、削除されました。


詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW ヒント<a id="FastFirstRowHint"></a>

**タイトル: FASTFIRSTROW クエリ ヒントは廃止され、削除されました。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Managed Instance では、FASTFIRSTROW クエリ ヒントは廃止され、削除されました。


**推奨事項**   
FASTFIRSTROW クエリ ヒントの代わりに OPTION (FAST n) を使用します。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**タイトル: Filestream と Filetable は Azure SQL Managed Instance ではサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
テキスト ドキュメント、画像、動画などの構造化されていないデータを NTFS ファイル システムに格納できる Filestream 機能は、Azure SQL Managed Instance ではサポートされていません。 **このデータベースは、Filestream ファイルグループを含むバックアップを Azure SQL Managed Instance で復元できないため、移行できません。**


**推奨事項**   
構造化されていないファイルを Azure Blob Storage にアップロードし、これらのファイルに関するメタデータ (名前、種類、URL の場所、ストレージ キーなど) を Azure SQL Managed Instance に格納します。 Azure SQL Managed Instance 間で BLOB をストリーミングできるように、アプリケーションを再設計する必要がある場合があります。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [SQL Azure 間での BLOB のストリーミングに関するブログ](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>異種 MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**タイトル: SQL Server 以外のリモート サーバーを使用する BEGIN DISTRIBUTED TRANSACTION は、Azure SQL Managed Instance ではサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
リモート サーバーが SQL Server ではない場合、Transact SQL BEGIN DISTRIBUTED TRANSACTION によって開始され、Microsoft 分散トランザクション コーディネーター (MS DTC) で管理される分散トランザクションは、Azure SQL Managed Instance ではサポートされません。 


**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、BEGIN DISTRUBUTED TRANSACTION を使用するすべてのオブジェクトを確認します。 複数のインスタンス間での分散トランザクションがサポートされている (現在、プレビュー段階) Azure SQL Managed Instance に参加しているデータベースを移行することを検討してください。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL Managed Instance の複数のサーバーにまたがるトランザクション ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>同種 MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**タイトル: BEGIN DISTRIBUTED TRANSACTION は、Azure SQL Managed Instance の複数のサーバーにわたってサポートされています。**    
**カテゴリ**: 問題   

**説明**   
Transact SQL BEGIN DISTRIBUTED TRANSACTION によって開始され、Microsoft 分散トランザクション コーディネーター (MS DTC) で管理される分散トランザクションは、Azure SQL Managed Instance の複数のサーバーにわたってサポートされています。


**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、BEGIN DISTRUBUTED TRANSACTION を使用するすべてのオブジェクトを確認します。 複数のインスタンス間での分散トランザクションがサポートされている (現在、プレビュー段階) Azure SQL Managed Instance に参加しているデータベースを移行することを検討してください。 または、Azure 仮想マシン上の SQL Server に移行します。 

詳細情報: [Azure SQL Managed Instance の複数のサーバーにまたがるトランザクション](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>リンク サーバー (SQL 以外のプロバイダー)<a id="LinkedServerWithNonSQLProvider"></a>

**タイトル: SQL 以外のサーバー プロバイダーのリンク サーバーは Azure SQL Managed Instance ではサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
リンク サーバーを使用すると、SQL Server のインスタンス外の OLE DB データ ソースに対して、SQL Server データベース エンジンでコマンドを実行できます。 SQL 以外のサーバー プロバイダーのリンク サーバーは Azure SQL Managed Instance ではサポートされていません。 


**推奨事項**   
リモート サーバー プロバイダーが Oracle、Sybase などの SQL 以外のサーバーである場合、Azure SQL Managed Instance でリンク サーバー機能はサポートされません。 

リンク サーバーが必要なくなるようにするには、次のアクションが推奨されます。 
- リモートの SQL 以外のサーバーから依存データベースを特定し、これらを移行するデータベースに移動することを検討します。 
- SQL Managed Instance、SQL Database、Azure Synapse SQL、SQL Server インスタンスなど、サポートされているターゲットに依存データベースを移行します。 
- Azure SQL Managed Instance と Azure 仮想マシン上の SQL Server (SQL VM) の間にリンク サーバーを作成することを検討します。  その後、SQL VM から、Oracle や Sybase へのリンク サーバーを作成します。この方法には 2 つのホップが含まれますが、一時的な回避策として使用できます。  
- または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL Managed Instance でのリンク サーバーの相違点](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>マージ ジョブ<a id="MergeJob"></a>

**タイトル: マージ ジョブ ステップは Azure SQL Managed Instance ではサポートされていません。**    
**カテゴリ**: 警告   

**説明**   
これは、レプリケーション マージ エージェントをアクティブにするジョブ ステップです。 レプリケーション マージ エージェントは、データベース テーブルに保持された初期スナップショットをサブスクライバーに適用するユーティリティ実行可能ファイルです。 さらに、初期スナップショットの作成後にパブリッシャーで発生したデータの増分変更をマージし、ユーザーが構成したルールに従って、またはユーザーが作成したカスタム競合回避モジュールを使用して、競合を調整します。 マージ ジョブ ステップは Azure SQL Managed Instance ではサポートされていません。


**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、マージ ジョブ ステップを使用してすべてのジョブを表示し、ジョブ ステップまたは影響を受けるオブジェクトを削除できるかどうかを評価します。 または、Azure 仮想マシン上の SQL Server に移行します

詳細情報: [Azure SQL Managed Instance での SQL Server エージェントの相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>MI データベースのサイズ<a id="MIDatabaseSize<"></a>

**タイトル: Azure SQL Managed Instance では、8 TB を超えるデータベース サイズはサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
データベースのサイズがインスタンスの予約済み最大ストレージよりも大きくなっています。 **このデータベースは、サイズが許可されている制限を超えているため、移行用に選択できません。**


**推奨事項**   
データをアーカイブまたは圧縮できるか、あるいは複数のデータベースにシャード化できるかを評価します。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL Managed Instance のハードウェアの世代の特性 ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>MI のインスタンス サイズ<a id="MIInstanceSize<"></a>

**タイトル: Azure SQL Managed Instance でのインスタンス ストレージの最大サイズを 8 TB より大きくすることはできません。**    
**カテゴリ**: 警告   

**説明**   
すべてのデータベースのサイズが、インスタンスの予約済み最大ストレージよりも大きくなっています。  


**推奨事項**   
データベースを別の Azure SQL Managed Instance に移行するか、すべてのデータベースが同じインスタンス上に存在する必要がある場合は Azure 仮想マシン上の SQL Server に移行することを検討してください。 

詳細情報: [Azure SQL Managed Instance のハードウェアの世代の特性 ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>複数のログ ファイル<a id="MultipleLogFiles<"></a>

**タイトル: Azure SQL Managed Instance では複数のログ ファイルはサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
SQL Server を使用すると、データベースで複数のファイルにログを記録できます。 このデータベースには、Azure SQL Managed Instance でサポートされていない複数のログ ファイルがあります。 **このデータベースは、Azure SQL Managed Instance でバックアップを復元できないため、移行できません。 
**

**推奨事項**   
Azure SQL Managed Instance では、データベースごとに 1 つのログのみがサポートされます。 このデータベースを Azure に移行する前に、ログ ファイルの 1 つを除くすべてのログ ファイルを削除する必要があります。 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

詳細情報: [Azure SQL Managed Instance でサポートされていないデータベース オプション  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Next 列<a id="NextColumn"></a>

**タイトル: Azure SQL Managed Instance では、NEXT という名前のテーブルと列があると、エラーが発生します。**    
**カテゴリ**: 問題   

**説明**   
NEXT という名前のテーブルまたは列が検出されました。 Microsoft SQL Server で導入されたシーケンスでは、ANSI 標準の NEXT VALUE FOR 関数が使用されます。 テーブルまたは列の名前が NEXT で、列の別名として VALUE が指定されている場合や、ANSI 標準の AS が省略されている場合、作成されたステートメントによってエラーが発生する可能性があります。


**推奨事項**   
ステートメントを書き直し、テーブルまたは列の別名を指定するときに ANSI 標準の AS キーワードを使用してください。 たとえば、列の名前が NEXT で、その列に VALUE という別名が付けられている場合、クエリ SELECT NEXT VALUE FROM TABLE を使用するとエラーが発生するため、SELECT NEXT AS VALUE FROM TABLE に書き直す必要があります。 同様に、テーブルの名前が NEXT で、そのテーブルに VALUE という別名が付けられている場合、クエリ SELECT Col1 FROM NEXT VALUE を使用するとエラーが発生するため、SELECT Col1 FROM NEXT AS VALUE に書き直す必要があります。



## <a name="non-ansi-style-left-outer-join"></a>ANSI 以外のスタイルでの左外部結合<a id="NonANSILeftOuterJoinSyntax"></a>

**タイトル: ANSI 以外のスタイルでの左外部結合は廃止され、削除されました。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Managed Instance では、ANSI 以外のスタイルでの左外部結合は廃止され、削除されました。 


**推奨事項**   
ANSI 結合構文を使用してください。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>ANSI 以外のスタイルでの右外部結合<a id="NonANSIRightOuterJoinSyntax"></a>

**タイトル: ANSI 以外のスタイルでの右外部結合は廃止され、削除されました。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Managed Instance では、ANSI 以外のスタイルでの右外部結合は廃止され、削除されました。 



詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**推奨事項**   
ANSI 結合構文を使用してください。

## <a name="databases-exceed-100"></a>データベースの数が 100 を超えている <a id="NumDbExceeds100"></a>

**タイトル: Azure SQL Managed Instance では、インスタンスあたり最大 100 個のデータベースがサポートされます。**    
**カテゴリ**: 警告   

**説明**   
インスタンス ストレージ サイズの制限に達していない限り、Azure SQL Managed Instance でサポートされるデータベースの最大数は 100 です。



**推奨事項**   
データベースを別の Azure SQL Managed Instance に移行するか、すべてのデータベースが同じインスタンス上に存在する必要がある場合は Azure 仮想マシン上の SQL Server に移行することを検討してください。 

詳細情報: [Azure SQL Managed Instance のリソース制限](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (BLOB 以外のデータ ソース)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**タイトル: Azure SQL Managed Instance では、Azure Blob Storage 以外のデータ ソースでの一括操作で使用される OpenRowSet はサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
OPENROWSET では組み込みの BULK プロバイダーによる一括操作がサポートされ、ファイルのデータを行セットとして読み取り、返すことができます。 Azure Blob Storage 以外のデータ ソースでの OPENROWSET は、Azure SQL Managed Instance ではサポートされていません。 



**推奨事項**   
OPENROWSET 関数は、SQL Server インスタンス (マネージド、オンプレミス、または Virtual Machines 内) でのみ、クエリを実行するために使用できます。 プロバイダーとしてサポートされるのは、SQLNCLI、SQLNCLI11、および SQLOLEDB 値のみです。 したがって、推奨されるアクションは、リモートの SQL 以外のサーバーから依存データベースを特定し、これらを移行するデータベースに移動することを検討することです。 または、Azure 仮想マシン上の SQL Server に移行します

詳細情報: [Azure SQL Managed Instance での BULK INSERT と OPENROWSET の相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (SQL 以外のプロバイダー)<a id="OpenRowsetWithNonSQLProvider"></a>

**タイトル: Azure SQL Managed Instance では、SQL 以外のプロバイダーでの OpenRowSet はサポートされていません。**    
**カテゴリ**: 問題   

**説明**   
このメソッドは、リンク サーバー内のテーブルにアクセスする代わりに、OLE DB を使用してリモート データに接続しアクセスするアドホック メソッドです。 Azure SQL Managed Instance では、SQL 以外のプロバイダーでの OpenRowSet はサポートされていません。 



**推奨事項**   
OPENROWSET 関数は、SQL Server インスタンス (マネージド、オンプレミス、または Virtual Machines 内) でのみ、クエリを実行するために使用できます。 プロバイダーとしてサポートされるのは、SQLNCLI、SQLNCLI11、および SQLOLEDB 値のみです。 したがって、推奨されるアクションは、リモートの SQL 以外のサーバーから依存データベースを特定し、これらを移行するデータベースに移動することを検討することです。

詳細情報: [Azure SQL Managed Instance での BULK INSERT と OPENROWSET の相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>PowerShell ジョブ<a id="PowerShellJob"></a>

**タイトル: PowerShell ジョブ ステップは Azure SQL Managed Instance ではサポートされていません。**    
**カテゴリ**: 警告   

**説明**   
これは、PowerShell スクリプトを実行するジョブ ステップです。 PowerShell ジョブ ステップは Azure SQL Managed Instance ではサポートされていません。 



**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、PowerShell ジョブ ステップを使用してすべてのジョブを表示し、ジョブ ステップまたは影響を受けるオブジェクトを削除できるかどうかを評価します。  Azure Automation を使用できるかどうかを評価します。 または、Azure 仮想マシン上の SQL Server に移行します

詳細情報: [Azure SQL Managed Instance での SQL Server エージェントの相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>キュー リーダー ジョブ<a id="QueueReaderJob"></a>

**タイトル: キュー リーダー ジョブ ステップは Azure SQL Managed Instance ではサポートされていません。**    
**カテゴリ**: 警告   

**説明**   
これは、レプリケーション キュー リーダー エージェントをアクティブにするジョブ ステップです。 レプリケーション キュー リーダー エージェントは、Microsoft SQL Server キューまたは Microsoft メッセージ キューに格納されたメッセージを読み取ってから、これらのメッセージをパブリッシャーに適用する実行可能ファイルです。 キュー リーダー エージェントは、スナップショット、およびキュー更新を許可するトランザクション パブリケーションで使用されます。 キュー リーダー ジョブ ステップは Azure SQL Managed Instance ではサポートされていません。


**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、キュー リーダー ジョブ ステップを使用してすべてのジョブを表示し、ジョブ ステップまたは影響を受けるオブジェクトを削除できるかどうかを評価します。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL Managed Instance での SQL Server エージェントの相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**タイトル: レガシ スタイルの RAISERROR 呼び出しは、それに相当する最新のものに置き換える必要があります。**    
**カテゴリ**: 警告   

**説明**   
次の例のような RAISERROR 呼び出しは、コンマとかっこが含まれないため、レガシ スタイルと呼ばれます。 RAISERROR 50001 'これはテストです'。 Azure SQL Managed Instance では、RAISERROR を呼び出すこのメソッドは廃止され、削除されました。



**推奨事項**   
現在の RAISERROR 構文を使用してステートメントを書き直すか、最新のアプローチの `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` を使用できるかどうかを評価してください。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Service Broker<a id="ServiceBrokerWithNonLocalAddress"></a>

**タイトル: Azure SQL Managed Instance では、Service Broker 機能は部分的にサポートされています。**    
**カテゴリ**: 問題   

**説明**   
SQL Server Service Broker では、SQL Server データベース エンジンのメッセージングおよびキューイング アプリケーションをネイティブでサポートしています。 このデータベースでは、Azure SQL Managed Instance でサポートされていないクロスインスタンス Service Broker が有効になっています。 


**推奨事項**   
Azure SQL Managed Instance では、クロスインスタンス Service Broker はサポートされていません。つまり、アドレスがローカルではありません。 このデータベースを Azure に移行する前に、`ALTER DATABASE [database_name] SET DISABLE_BROKER` というコマンドを使用して Service Broker を無効にする必要があります。また、メッセージが SQL インスタンスに到着しないようにするために、Service Broker エンドポイントを削除または停止することが必要になる場合もあります。 データベースが Azure に移行されたら、Azure Service Bus の機能を調べ、Service Broker ではなく、クラウドベースの汎用メッセージング システムを実装できます。 または、Azure 仮想マシン上の SQL Server に移行します。 

詳細情報: [Azure SQL Managed Instance での Service Broker の相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**タイトル: SQL Mail は廃止されました。**    
**カテゴリ**: 警告   


**説明**   
Azure SQL Managed Instance では、SQL Mail は廃止され、削除されました。



**推奨事項**   
データベース メールを使用してください。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**タイトル: Azure SQL Managed Instance では使用できない削除されたシステム ストアド プロシージャを参照するステートメントが検出されました。**    
**カテゴリ**: 警告   

**説明**   
Azure SQL Managed Instance では、`sp_dboption`、`sp_addserver`、`sp_dropalias`、`sp_activedirectory_obj`、`sp_activedirectory_scp`、および `sp_activedirectory_start` のサポート対象外のシステムおよび拡張ストアド プロシージャは使用できません。 




**推奨事項**   
Azure SQL Managed Instance で削除されたサポート対象外のシステム プロシージャへの参照を削除します。

詳細情報: [SQL Server で廃止されたデータベース エンジンの機能](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Transact-SQL ジョブ<a id="TransactSqlJob"></a>

**タイトル: TSQL ジョブ ステップには、Azure SQL Managed Instance でサポートされていないコマンドが含まれています**   
**カテゴリ**: 警告   


**説明**   
これは、スケジュールされた時刻に TSQL スクリプトを実行するジョブ ステップです。 TSQL ジョブ ステップには、Azure SQL Managed Instance でサポートされていないサポート対象外のコマンドが含まれています。



**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、Azure SQL Managed Instance でサポートされていないコマンドを含むすべてのジョブを表示し、ジョブ ステップまたは影響を受けるオブジェクトを削除できるかどうかを評価します。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL Managed Instance での SQL Server エージェントの相違点 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>トレース フラグ<a id="TraceFlags"></a>

**タイトル: Azure SQL Managed Instance でサポートされていないトレース フラグが見つかりました**   
**カテゴリ**: 警告   


**説明**   
Azure SQL Managed Instance では、限られた数のグローバル トレース フラグのみがサポートされます。 セッション トレース フラグはサポートされていません。



**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、Azure SQL Managed Instance でサポートされていないすべてのトレース フラグを表示し、それらを削除できるかどうかを評価します。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [トレース フラグ](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Windows 認証<a id="WindowsAuthentication"></a>

**タイトル: Windows 認証 (統合セキュリティ) にマップされたデータベース ユーザーは、Azure SQL Managed Instance ではサポートされていません**   
**カテゴリ**: 警告   


**説明**   
Azure SQL Managed Instance では次の 2 種類の認証がサポートされます。 
- SQL 認証。ユーザー名とパスワードを使用します。
- Azure Active Directory 認証。Azure Active Directory で管理されている ID を使用します。管理および統合されたドメインの場合にサポートされます。 

Windows 認証 (統合セキュリティ) にマップされたデータベース ユーザーは、Azure SQL Managed Instance ではサポートされていません。 


**推奨事項**   
ローカル Active Directory を Azure Active Directory とフェデレーションします。 その後、Windows ID を同等の Azure Active Directory ID に置き換えることができます。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [SQL Managed Instance のセキュリティ機能](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**タイトル: xp_cmdshell は Azure SQL Managed Instance ではサポートされていません。**    
**カテゴリ**: 問題   


**説明**   
Windows コマンド シェルを生成し、実行用の文字列を渡す xp_cmdshell は、Azure SQL Managed Instance ではサポートされていません。 



**推奨事項**   
Azure Migrate の [影響を受けるオブジェクト] セクションを確認し、xp_cmdshell を使用してすべてのオブジェクトを確認し、xp_cmdshell または影響を受けるオブジェクトに対する参照を削除できるか評価します。 クラウドベースの自動化と構成サービスを提供する Azure Automation の調査を検討してください。 または、Azure 仮想マシン上の SQL Server に移行します。

詳細情報: [Azure SQL Managed Instance でのストアド プロシージャの相違点](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>次のステップ

Azure SQL Managed Instance への SQL Server の移行を開始するには、「[SQL Server から SQL Managed Instance へ - 移行ガイド](sql-server-to-managed-instance-guide.md)」を参照してください。

- さまざまなデータベースとデータの移行シナリオ、および特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールの一覧については、[データ移行のサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- Azure SQL Managed Instance の詳細については、次を参照してください。
   - [Azure SQL Managed Instance のサービス レベル](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server と Azure SQL Managed Instance の相違点](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 総保有コスト計算ツール](https://azure.microsoft.com/pricing/tco/calculator/) 


- クラウド移行のためのフレームワークと導入サイクルの詳細については、以下を参照してください
   -  [Azure 向けのクラウド導入フレームワーク](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- アプリケーション アクセス層を評価するには、「[Data Access Migration Toolkit (プレビュー)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)」を参照してください。
- データ アクセス レイヤーの A/B テストの実行方法について詳しくは、[Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) についてのページを参照してください。