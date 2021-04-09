---
title: SQL Server と Azure SQL Managed Instance での T-SQL の相違点
description: この記事では、Azure SQL Managed Instance と SQL Server の Transact-SQL (T-SQL) の相違点について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 3/16/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 1afd5a0e24e144169280e683321b5843e9766136
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601374"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>SQL Server と Azure SQL Managed Instance での T-SQL の相違点
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、Azure SQL Managed Instance と SQL Server の構文および動作の相違点について簡単に説明します。 


SQL Managed Instance は、SQL Server データベース エンジンとの高い互換性を備えており、ほとんどの機能は SQL Managed Instance でサポートされています。

![SQL Server からの移行が簡単](./media/transact-sql-tsql-differences-sql-server/migration.png)

SQL Managed Instance には、SQL Server と比べて PaaS の制限と動作の違いがいくつかあります。 それらの相違点は、次のカテゴリに分けることができます。<a name="Differences"></a>

- [可用性](#availability)には、[Always On 可用性グループ](#always-on-availability-groups)と[バックアップ](#backup)の相違点が含まれます。
- [セキュリティ](#security)には、[監査](#auditing)、[証明書](#certificates)、[資格情報](#credential)、[暗号化プロバイダー](#cryptographic-providers)、[ログイン/ユーザー](#logins-and-users)、[サービス キーとサービス マスター キー](#service-key-and-service-master-key)の相違点が含まれます。
- [構成](#configuration)には、[バッファー プール拡張機能](#buffer-pool-extension)、[照合順序](#collation)、[互換性レベル](#compatibility-levels)、[データベース ミラーリング](#database-mirroring)、[データベース オプション](#database-options)、[SQL Server エージェント](#sql-server-agent)、[テーブル オプション](#tables)の相違点が含まれます。
- [機能](#functionalities)には、[BULK INSERT/OPENROWSET](#bulk-insert--openrowset)、[CLR](#clr)、[DBCC](#dbcc)、[分散トランザクション](#distributed-transactions)、[拡張イベント](#extended-events)、[外部ライブラリ](#external-libraries)、[filestream と FileTable](#filestream-and-filetable)、[フルテキスト セマンティック検索](#full-text-semantic-search)、[リンク サーバー](#linked-servers)、[PolyBase](#polybase)、[レプリケーション](#replication)、[RESTORE](#restore-statement)、[Service Broker](#service-broker)、[ストアド プロシージャ、関数、およびトリガー](#stored-procedures-functions-and-triggers)が含まれます。
- VNet やサブネット構成などの[環境設定](#Environment)。

これらの機能の大半はアーキテクチャ上の制約であり、サービスの機能を表します。

SQL Managed Instance で見つかり、将来解決される予定の一時的な既知の問題については、[リリース ノート ページ](../database/doc-changes-updates-release-notes.md)で説明しています。

## <a name="availability"></a>可用性

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Always On 可用性グループ

[高可用性](../database/high-availability-sla.md)は SQL Managed Instance に組み込まれており、ユーザーが制御することはできません。 次のステートメントはサポートされていません。

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) ステートメントの [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 句

### <a name="backup"></a>バックアップ

SQL Managed Instance には自動バックアップがあるので、ユーザーは完全なデータベースの `COPY_ONLY` バックアップを作成できます。 差分、ログ、ファイル スナップショットの各バックアップはサポートされていません。

- SQL Managed Instance では、Azure BLOB ストレージ アカウントにのみインスタンス データベースをバックアップできます。
  - サポートされるのは `BACKUP TO URL` のみです。
  - `FILE`、`TAPE`、およびバックアップ デバイスはサポートされていません。
- ほとんどの一般的な `WITH` オプションがサポートされています。
  - `COPY_ONLY` は必須です。
  - `FILE_SNAPSHOT` はサポートされていません。
  - テープ オプション: `REWIND`、`NOREWIND`、`UNLOAD`、`NOUNLOAD` はサポートされていません。
  - ログ固有のオプション: `NORECOVERY`、`STANDBY`、`NO_TRUNCATE` はサポートされていません。

制限事項: 

- SQL Managed Instance では、最大 32 個のストライプを使用するバックアップにインスタンス データベースをバックアップできます。バックアップの圧縮を使用した場合、このバックアップで最大 4 TB のデータベースに十分対応できます。
- サービス管理 Transparent Data Encryption (TDE) を使用して暗号化されたデータベースでは、`BACKUP DATABASE ... WITH COPY_ONLY` は実行できません。 サービス管理 TDE では、バックアップを内部の TDE のキーで暗号化するように強制します。 キーはエクスポートできないので、バックアップを復元することはできません。 自動バックアップとポイントインタイム リストアを使用するか、代わりに[顧客管理 (BYOK) TDE](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) を使用します。 また、データベースで暗号化を無効にすることができます。
- Managed Instance 上で行われたネイティブ バックアップを SQL Server に復元することはできません。 これは、SQL Server のどのバージョンと比べても、Managed Instance の内部データベース バージョンが高いためです。
- SQL Managed Instance で `BACKUP` コマンドを使用した場合の最大バックアップ ストライプ サイズは、最大 BLOB サイズである 195 GB です。 バックアップ コマンドでストライプ サイズを増やして、個々のストライプ サイズを減らし、この制限内に収まるようにします。

    > [!TIP]
    > 次の操作を実行すると、オンプレミス環境の SQL Server または仮想マシンからデータベースをバックアップするときに、この制限を回避できます。
    >
    > - `URL` にバックアップする代わりに `DISK` にバックアップします。
    > - バックアップ ファイルを BLOB ストレージにアップロードします。
    > - SQL Managed Instance に復元します。
    >
    > SQL Managed Instance で `Restore` コマンドを実行すると、アップロードされたバックアップ ファイルに対して異なる BLOB タイプが使用されるため、より大きな BLOB サイズがサポートされます。

T-SQL を使用したバックアップについては、[BACKUP](/sql/t-sql/statements/backup-transact-sql) に関する記事をご覧ください。

## <a name="security"></a>セキュリティ

### <a name="auditing"></a>監査

Microsoft Azure SQL と SQL Server での監査の主な違いは次のとおりです。

- SQL Managed Instance では、監査はサーバー レベルで機能します。 `.xel` ログ ファイルは、Azure BLOB ストレージに格納されます。
- Azure SQL Database では、監査はデータベース レベルで機能します。 `.xel` ログ ファイルは、Azure BLOB ストレージに格納されます。
- オンプレミスまたは仮想マシンの SQL Server では、監査はサーバー レベルで機能します。 イベントは、ファイル システムまたは Windows イベント ログに格納されます。
 
SQL Managed Instance の XEvent 監査では、Azure Blob Storage のターゲットがサポートされます。 ファイル ログと Windows ログはサポートされていません。

Azure Blob Storage を監査するための `CREATE AUDIT` 構文の主な相違点は次のとおりです。

- `.xel` ファイルが配置されている Azure BLOB ストレージ コンテナーの URL の指定に使用できる新しい `TO URL` 構文が用意されています。
- SQL Managed Instance は Windows ファイル共有にアクセスできないため、`TO FILE` 構文はサポートされていません。

詳細については、次を参照してください。 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [監査](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>証明書

SQL Managed Instance はファイル共有と Windows フォルダーにはアクセスできないので、次の制約が適用されます。

- `CREATE FROM`/`BACKUP TO` ファイルは、証明書ではサポートされていません。
- `FILE`/`ASSEMBLY` からの `CREATE`/`BACKUP` 証明書はサポートされていません。 秘密キー ファイルは使用できません。 

[CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) に関する記事、および [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql) に関する記事をご覧ください。 
 
**回避策**:証明書のバックアップを作成してバックアップを復元するのでなく、[証明書のバイナリ コンテンツと秘密キーを取得し、それを .sql ファイルとして保存し、バイナリから作成します](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)。

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>資格情報

Azure Key Vault と `SHARED ACCESS SIGNATURE` の ID だけがサポートされています。 Windows ユーザーはサポートされていません。

[CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) に関する記事、および [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql) に関する記事をご覧ください。

### <a name="cryptographic-providers"></a>暗号化プロバイダー

SQL Managed Instance はファイルにアクセスできないため、暗号化プロバイダーは作成できません。

- `CREATE CRYPTOGRAPHIC PROVIDER` はサポートされていません。 [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql) に関する記事をご覧ください。
- `ALTER CRYPTOGRAPHIC PROVIDER` はサポートされていません。 [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql) に関する記事をご覧ください。

### <a name="logins-and-users"></a>ログインとユーザー

- `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY`、`FROM SID` を使用して作成された SQL ログインはサポートされています。 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) に関する記事をご覧ください。
- [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 構文または [CREATE USER FROM Login [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 構文を使用して作成された Azure Active Directory (Azure AD) サーバー プリンシパル (ログイン) がサポートされます。 これらのログインは、サーバー レベルで作成されます。

    SQL Managed Instance は、`CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 構文で Azure AD データベース プリンシパルをサポートします。 この機能は、Azure AD 包含データベース ユーザーとも呼ばれます。

- `CREATE LOGIN ... FROM WINDOWS` 構文を使用して作成された Windows ログインはサポートされていません。 Azure Active Directory のログインとユーザーを使用します。
- インスタンスを作成した Azure AD ユーザーは、[無制限の管理特権](../database/logins-create-manage.md)を持ちます。
- 管理者以外の、データベース レベルの Azure AD ユーザーは、`CREATE USER ... FROM EXTERNAL PROVIDER` 構文を使用して作成できます。 [CREATE USER ...FROM EXTERNAL PROVIDER](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) を参照してください。
- Azure AD サーバー プリンシパル (ログイン) は、1 つの SQL Managed Instance 内のみで SQL 機能をサポートします。 同じ Azure AD テナント内か、または異なるテナント内かに関係なく、Azure AD ユーザーの場合、クロス インスタンス対話を必要とする機能はサポートされていません。 そのような機能の例として次のものがあります。

  - SQL トランザクション レプリケーション。
  - リンク サーバー。

- Azure AD グループにマップされている Azure AD ログインをデータベース所有者として設定することはサポートされていません。
- [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 句など、他の Azure AD プリンシパルを使用することによる Azure AD サーバー レベル プリンシパルの権限の借用はサポートされています。 EXECUTE AS の制限事項は次のとおりです。

  - 名前がログイン名と異なる場合、EXECUTE AS USER は Azure AD ユーザーに対してサポートされません。 たとえば、CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] 構文を使用してユーザーが作成されて、EXEC AS USER = _myAadUser_ を使用して権限借用が試行された場合などです。 Azure AD サーバー プリンシパル (ログイン) から **USER** を作成するときは、user_name を **LOGIN** と同じ login_name として指定します。
  - Azure AD プリンシパルを対象とした次の操作を実行できるのは、`sysadmin` ロールに属している SQL Server レベル プリンシパル (ログイン) のみです。

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - EXECUTE AS ステートメントを使用してユーザーを偽装するには、そのユーザーが Azure AD サーバー プリンシパル (ログイン) に直接マップされる必要があります。 Azure AD サーバー プリンシパルにマップされた Azure AD グループのメンバーであるユーザーは、指定されたユーザー名に対する偽装権限を呼び出し元が持っている場合でも、EXECUTE AS ステートメントを使用して有効に偽装できません。

- bacpac ファイルを使用したデータベースのエクスポート/インポートは、[SSMS V 18.4 以降](/sql/ssms/download-sql-server-management-studio-ssms)または [SQLPackage.exe](/sql/tools/sqlpackage-download) のいずれかを使用している SQL Managed Instance 内の Azure AD ユーザーに対してサポートされます。
  - データベース bacpac ファイルを使用すると、次の構成がサポートされます。 
    - 同じ Azure AD ドメイン内の異なるマネージド インスタンス間でのデータベースのエクスポート/インポート。
    - SQL Managed Instance からデータベースをエクスポートし、同じ Azure AD ドメイン内の SQL Database にインポートする。 
    - SQL Database からデータベースをエクスポートし、同じ Azure AD ドメイン内の SQL Managed Instance にインポートする。
    - SQL Managed Instance からデータベースをエクスポートし、SQL Server (バージョン 2012 以降) にインポートする。
      - この構成では、すべての Azure AD ユーザーが、ログインなしの SQL Server データベース プリンシパル (ユーザー) として作成されます。 ユーザーの種類は `SQL` として表示され、sys.database_principals では `SQL_USER` として表示されます。 これらのアクセス許可とロールは SQL Server データベースのメタデータに残り、権限の借用に使用できます。 ただし、これらの資格情報を使用して SQL Server にアクセスしてログインすることはできません。

- SQL Managed Instance のプロビジョニング プロセスによって作成されるサーバーレベル プリンシパル ログイン、`securityadmin` や `sysadmin` などのサーバー ロールのメンバー、あるいはサーバー レベルの ALTER ANY LOGIN アクセス許可を持つその他のログインのみが、SQL Managed Instance のマスター データベースに Azure AD サーバー プリンシパル (ログイン) を作成できます。
- ログインが SQL プリンシパルの場合、作成コマンドを使用して Azure AD アカウントのログインを作成できるのは、`sysadmin` ロールに属しているログインのみです。
- Azure AD ログインは、Azure SQL Managed Instance に使用されるものと同じディレクトリ内の Azure AD のメンバーでなければなりません。
- Azure AD サーバー プリンシパル (ログイン) は、SQL Server Management Studio 18.0 プレビュー 5 から、オブジェクト エクスプローラーに表示されます。
- Azure AD サーバー プリンシパル (ログイン) と Azure AD 管理者アカウントとの重複は許可されます。 プリンシパルを解決してアクセス許可を SQL Managed Instance に適用するとき、Azure AD の管理者よりも Azure AD サーバー プリンシパル (ログイン) が優先されます。
- 認証時、認証するプリンシパルを解決するために次の順序が適用されます。

    1. Azure AD アカウントが、Azure AD サーバー プリンシパル (ログイン) に直接マップされているものとして存在する (sys.server_principals に type "E" と指定されている) 場合、アクセスを許可し、Azure AD サーバー プリンシパル (ログイン) のアクセス権を適用する。
    2. Azure AD アカウントが、Azure AD サーバー プリンシパル (ログイン) にマップされている Azure AD グループのメンバーである (sys.server_principals に type "X" と指定されている) 場合、アクセスを許可し、Azure AD グループ ログインのアクセス権を適用する。
    3. Azure AD アカウントが SQL Managed Instance 用のポータル構成の特殊な Azure AD 管理者である (SQL Managed Instance システム ビューに存在しない) 場合は、SQL Managed Instance の Azure AD 管理者の特殊な固定アクセス許可を適用する (レガシー モード)。
    4. Azure AD アカウントが、データベース内の Azure AD ユーザーに直接マップされたものとして存在する (sys.database_principals に type "E" と指定されている) 場合、アクセスを許可し、Azure AD データベース ユーザーのアクセス許可を適用する。
    5. Azure AD アカウントが、データベース内の Azure AD ユーザーにマップされた Azure AD グループのメンバーである (sys.database_principals に type "X" と指定されている) 場合、アクセスを許可し、Azure AD グループ ログインのアクセス許可を適用する。
    6. Azure AD ユーザー アカウントまたは Azure AD グループ アカウントのいずれかにマップされている Azure AD ログインがあり、認証しているユーザーを解決する場合、この Azure AD ログインのすべてのアクセス許可が適用される。

### <a name="service-key-and-service-master-key"></a>サービス キーとサービス マスター キー

- [マスター キーのバックアップ](/sql/t-sql/statements/backup-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。
- [マスター キーの復元](/sql/t-sql/statements/restore-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。
- [サービス マスター キーのバックアップ](/sql/t-sql/statements/backup-service-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。
- [サービス マスター キーの復元](/sql/t-sql/statements/restore-service-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。

## <a name="configuration"></a>構成

### <a name="buffer-pool-extension"></a>バッファー プール拡張

- [バッファー プール拡張機能](/sql/database-engine/configure-windows/buffer-pool-extension)はサポートされていません。
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` はサポートされていません。 [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql) に関する記事をご覧ください。

### <a name="collation"></a>照合順序

既定のインスタンスの照合順序は `SQL_Latin1_General_CP1_CI_AS` であり、作成パラメーターとして指定できます。 「[照合順序](/sql/t-sql/statements/collations)」をご覧ください。

### <a name="compatibility-levels"></a>互換性レベル

- サポートされている互換性レベルは、100、110、120、130、140、150 です。
- 100 より低い互換性レベルはサポートされていません。
- 新しいデータベースの既定の互換性レベルは 140 です。 互換性レベルが 100 以上のデータベースが復元された場合、互換性レベルは変更されません。

[ALTER DATABASE の互換性レベル](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)に関する記事をご覧ください。

### <a name="database-mirroring"></a>データベース ミラーリング

データベース ミラーリングはサポートされていません。

- `ALTER DATABASE SET PARTNER` オプションと `SET WITNESS` オプションはサポートされていません。
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` はサポートされていません。

詳細については、[ALTER DATABASE の SET PARTNER と SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring)、および [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql) の説明をご覧ください。

### <a name="database-options"></a>データベース オプション

- 複数のログ ファイルはサポートされていません。
- インメモリ オブジェクトは、General Purpose サービス レベルではサポートされていません。 
- General Purpose インスタンスあたり 280 ファイル (データベースあたり最大 280 ファイル) の制限があります。 General Purpose レベルのデータ ファイルとログ ファイルの両方がこの制限にカウントされます。 [Business Critical レベルでは、データベースあたり 32,767 ファイルがサポートされます](./resource-limits.md#service-tier-characteristics)。
- filestream データを含むファイル グループをデータベースに含めることはできません。 .bak に `FILESTREAM` データが含まれていると、復元は失敗します。 
- すべてのファイルが Azure Blob Storage に配置されます。 ファイルあたりの IO およびスループットは、個々のファイルのサイズによって異なります。

#### <a name="create-database-statement"></a>CREATE DATABASE ステートメント

次の制限事項が `CREATE DATABASE` に適用されます。

- ファイルおよびファイル グループは定義できません。 
- `CONTAINMENT` オプションはサポートされていません。 
- `WITH` オプションはサポートされていません。 
   > [!TIP]
   > 対処法として、`CREATE DATABASE` の後に `ALTER DATABASE` を使用して、ファイルの追加またはコンテインメントの設定を行うデータベース オプションを設定します。 

- `FOR ATTACH` オプションはサポートされていません。
- `AS SNAPSHOT OF` オプションはサポートされていません。

詳細については、[CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql) に関する記事をご覧ください。

#### <a name="alter-database-statement"></a>ALTER DATABASE ステートメント

ファイルの一部のプロパティは設定または変更できません。

- `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL ステートメントでは、ファイル パスは指定できません。 ファイルは SQL Managed Instance によって自動的に配置されるため、スクリプトから `FILENAME` を削除します。 
- `ALTER DATABASE` ステートメントを使用してファイル名を変更することはできません。

次のオプションは既定で設定されており、変更することはできません。

- `MULTI_USER`
- `ENABLE_BROKER`
- `AUTO_CLOSE OFF`

次のオプションは変更できません。

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

一部の `ALTER DATABASE` ステートメント (たとえば、[SET CONTAINMENT](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql)) は、データベースの自動バックアップ中やデータベースの作成直後などに一時的に失敗することがあります。 この場合、`ALTER DATABASE` ステートメントを再試行する必要があります。 関連するエラー メッセージの詳細については、「[解説](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2)」セクションを参照してください。

詳細については、[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options) に関する記事をご覧ください。

### <a name="sql-server-agent"></a>SQL Server エージェント

- SQL Managed Instance では現在、SQL Server エージェントの有効化/無効化はサポートされていません。 SQL エージェントは常に実行されています。
- アイドル状態の CPU に基づくジョブ スケジュール トリガーはサポートされていません。
- SQL Server エージェントの設定は読み取り専用です。 `sp_set_agent_properties` プロシージャは、SQL Managed Instance ではサポートされていません。 
- ジョブ
  - T-SQL ジョブ ステップがサポートされています。
  - 次のレプリケーション ジョブがサポートされています。
    - トランザクション ログ リーダー
    - スナップショット
    - ディストリビューター
  - SSIS ジョブ ステップがサポートされています。
  - 他の種類のジョブ ステップは現在サポートされていません。
    - マージ レプリケーション ジョブ ステップはサポートされていません。 
    - キュー リーダーはサポートされていません。 
    - コマンド シェルはまだサポートされていません。
  - SQL Managed Instance は、外部リソース (例: robocopy を介したネットワーク共有) にはアクセスできません。 
  - SQL Server Analysis Services はサポートされていません。
- 通知は部分的にサポートされています。
- 電子メール通知がサポートされていますが、データベース メール プロファイルを構成する必要があります。 SQL Server エージェントで使用できるデータベース メール プロファイルは 1 つだけであり、`AzureManagedInstance_dbmail_profile` という名前である必要があります。 
  - Pager はサポートされていません。
  - NetSend はサポートされていません。
  - アラートはまだサポートされていません。
  - プロキシはサポートされていません。
- EventLog はサポートされていません。
- SQL Agent ジョブを作成、変更、実行するために、ユーザーは Azure AD サーバー プリンシパル (ログイン) に直接マップされる必要があります。 直接マップされていないユーザー (たとえば、SQL Agent ジョブを作成、変更、または実行する権利を持つ Azure AD グループに属しているユーザー) は、これらの操作を実質的に実行できません。 これは、Managed Instance の借用と [EXECUTE AS の制限事項](#logins-and-users)のためです。
- マスター/ターゲット (MSX/TSX) ジョブのマルチサーバー管理機能はサポートされていません。

SQL Server エージェントについては、「[SQL Server エージェント](/sql/ssms/agent/sql-server-agent)」をご覧ください。

### <a name="tables"></a>テーブル

次のテーブルの種類はサポートされていません。

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (General Purpose レベルでのみサポートされません)

テーブルの作成および変更方法については、[CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) に関する記事および [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) に関する記事をご覧ください。

## <a name="functionalities"></a>機能

### <a name="bulk-insert--openrowset"></a>一括挿入/OPENROWSET

SQL Managed Instance はファイル共有や Windows フォルダーにアクセスできないため、ファイルは Azure BLOB ストレージからインポートする必要があります。

- が、Azure BLOB ストレージからファイルをインポートしている間、`BULK INSERT` コマンドで `DATASOURCE` が必要です。 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) に関する記事をご覧ください。
- Azure BLOB ストレージからのファイルの内容を読み取る場合、`OPENROWSET` 関数で `DATASOURCE` が必要です。 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql) に関する記事をご覧ください。
- `OPENROWSET` を使用すると、Azure SQL Database、Azure SQL Managed Instance、または SQL Server インスタンスからデータを読み取ることができます。 Oracle データベースや Excel ファイルなどのその他のソースはサポートされていません。

### <a name="clr"></a>CLR

SQL Managed Instance はファイル共有と Windows フォルダーにはアクセスできないので、次の制約が適用されます。

- サポートされるのは `CREATE ASSEMBLY FROM BINARY` のみです。 [CREATE ASSEMBLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql) に関する記事をご覧ください。 
- `CREATE ASSEMBLY FROM FILE` はサポートされていません。 [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql) に関する記事をご覧ください。
- `ALTER ASSEMBLY` ではファイルを参照できません。 [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql) に関する記事をご覧ください。

### <a name="database-mail-db_mail"></a>データベース メール (db_mail)
 - `sp_send_dbmail` では、@file_attachments パラメーターを使用して添付ファイルを送信できません。 この手順では、ローカル ファイル システムと外部共有または Azure Blob Storage にはアクセスできません。
 - `@query` パラメーターと認証に関連する既知の問題をご覧ください。
 
### <a name="dbcc"></a>DBCC

SQL Server で有効になっている、ドキュメントに記載されていない DBCC ステートメントは、SQL Managed Instance ではサポートされていません。

- サポートされるグローバル トレース フラグの数は限られています。 セッションレベルの `Trace flags` はサポートされません。 [トレース フラグ](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)に関する記事をご覧ください。
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) と [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) で使用できるトレースフラグの数が限られています。
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) とオプション REPAIR_ALLOW_DATA_LOSS、REPAIR_FAST、REPAIR_REBUILD の組み合わせは使用できません。これは、データベースを `SINGLE_USER` モードで設定することはできないためです。[ALTER DATABASE の相違点](#alter-database-statement)を参照してください。 データベースが破損した場合は Azure サポート チームが対応します。 データベースが破損している徴候がある場合は、Azure サポートにお問い合わせください。

### <a name="distributed-transactions"></a>分散トランザクション

[分散トランザクション](../database/elastic-transactions-overview.md)の部分的なサポートは、現在パブリック プレビューの段階です。 サポートされるシナリオは次のとおりです。
* 参加要素が[サーバー信頼グループ](./server-trust-group-overview.md)に含まれる Azure SQL Managed Instance のみのトランザクション。
* .NET (TransactionScope クラス) および Transact-SQL から開始されたトランザクション。

現在、Azure SQL Managed Instance では、MSDTC オンプレミスまたは Azure Virtual Machines で定期的にサポートされている他のシナリオはサポートされていません。

### <a name="extended-events"></a>拡張イベント

拡張イベント (XEvents) の一部の Windows 固有のターゲットはサポートされていません。

- `etw_classic_sync` ターゲットはサポートされていません。 Azure BLOB ストレージに `.xel` ファイルを保存します。 「[etw_classic_sync ターゲット](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)」をご覧ください。
- `event_file` ターゲットはサポートされていません。 Azure BLOB ストレージに `.xel` ファイルを保存します。 「[event_file ターゲット](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)」をご覧ください。

### <a name="external-libraries"></a>外部ライブラリ

In-Database R および Python 外部ライブラリは、限られたパブリック プレビューでサポートされています。 「[Azure SQL Managed Instance の Machine Learning Services (プレビュー)](machine-learning-services-overview.md)」を参照してください。

### <a name="filestream-and-filetable"></a>Filestream と FileTable

- filestream データはサポートされていません。
- `FILESTREAM` データを含むファイル グループをデータベースに含めることはできません。
- `FILETABLE` はサポートされていません。
- テーブルに `FILESTREAM` 型を含めることはできません。
- 次の関数はサポートされていません。
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

詳細については、[FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) に関する記事、および [FileTables](/sql/relational-databases/blob/filetables-sql-server) に関する記事をご覧ください。

### <a name="full-text-semantic-search"></a>フルテキスト セマンティック検索

[セマンティック検索](/sql/relational-databases/search/semantic-search-sql-server)はサポートされていません。

### <a name="linked-servers"></a>リンク サーバー

SQL Managed Instance のリンク サーバーがサポートするターゲットの数は限られています。

- サポートされているターゲットは、SQL Managed Instance、SQL Database、Azure Synapse SQL の[サーバーレス](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)と専用プール、および SQL Server インスタンスです。 
- 分散書き込み可能なトランザクションは、マネージド インスタンス間でのみ可能です。 詳細については、[分散トランザクション](../database/elastic-transactions-overview.md)に関する記事を参照してください。 ただし、MS DTC はサポートされていません。
- サポートされていないターゲットは、ファイル、Analysis Services、他の RDBMS です。 ファイル インポートの代わりに `BULK INSERT` または `OPENROWSET` を使用して Azure Blob Storage からネイティブ CSV インポートを使用するか、[Azure Synapse Analytics 内のサーバーレス SQL プール](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)を使用してファイルの読み込みを試行します。

操作: 

- [インスタンス間](../database/elastic-transactions-overview.md)書き込みトランザクションは、マネージド インスタンスでのみサポートされています。
- リンク サーバーの削除で `sp_dropserver` がサポートされています。 [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql) に関する記事をご覧ください。
- SQL Server インスタンスでのみ、`OPENROWSET` 関数を使用してクエリを実行できます。 これらは、マネージド、オンプレミス、仮想マシンのいずれかで配置できます。 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql) に関する記事をご覧ください。
- SQL Server インスタンスでのみ、`OPENDATASOURCE` 関数を使用してクエリを実行できます。 これらは、マネージド、オンプレミス、仮想マシンのいずれかで配置できます。 プロバイダーとしてサポートされる値は、`SQLNCLI`、`SQLNCLI11`、`SQLOLEDB` だけです。 たとえば `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee` です。 [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql) に関する記事をご覧ください。
- リンク サーバーを使用してネットワーク共有からファイル (Excel、CSV) を読み取ることはできません。 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)、[OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) (Azure Blob Storage から CSV ファイルを読み取る)、または [Synapse Analytics 内のサーバーレス SQL プールを参照するリンク サーバー](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)の使用を試行します。 この要求は、[SQL Managed Instance フィードバック項目](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|で追跡します

### <a name="polybase"></a>PolyBase

Azure SQL データベース、Azure SQL マネージド インスタンス、および Azure Synapse プールに対して唯一使用可能な外部ソースの種類は RDBMS (パブリック プレビュー段階) です。 Azure Storage から直接読み取る Polybase 外部テーブルの回避策として、[Synapse Analytics 内のサーバーレス SQL プールを参照する外部テーブル](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/)を使用できます。 Azure SQL Managed Instance では、[Synapse Analytics 内のサーバーレス SQL プール](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)や SQL Server へのリンク サーバーを使用して、Azure Storage のデータを読み取ることができます。
PolyBase については、[PolyBase](/sql/relational-databases/polybase/polybase-guide) に関する記事をご覧ください。

### <a name="replication"></a>レプリケーション

- スナップショットおよび双方向のレプリケーションの種類がサポートされています。 マージ レプリケーション、ピア ツー ピア レプリケーション、および更新可能サブスクリプションはサポートされていません。
- [トランザクション レプリケーション](replication-transactional-overview.md)は SQL Managed Instance のパブリック プレビューで使用できますが、制約がいくつかあります。
    - すべての種類のレプリケーション参加者 (パブリッシャー、ディストリビューター、プル サブスクライバー、プッシュ サブスクライバー) を SQL Managed Instance に配置できますが、パブリッシャーとディストリビューターは両者ともクラウドに配置するか、または両者ともオンプレミスに配置する必要があります。
    - SQL Managed Instance は、最新バージョンの SQL Server と通信できます。 詳細については、[バージョンのサポート マトリックス](replication-transactional-overview.md#supportability-matrix)を参照してください。
    - トランザクション レプリケーションには、いくつかの[追加のネットワーク要件](replication-transactional-overview.md#requirements)があります。

トランザクション レプリケーションの構成の詳細については、次に関するチュートリアルを参照してください。
- [SQL MI パブリッシャーと SQL MI サブスクライバー間のレプリケーション](replication-between-two-instances-configure-tutorial.md)
- [SQL MI パブリッシャー、SQL MI ディストリビューター、および SQL Server サブスクライバー間のレプリケーション](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>RESTORE ステートメント 

- サポートされている構文:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- サポートされていない構文:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- ソース: 
  - `FROM URL` (Azure BLOB ストレージ) が、サポートされている唯一のオプションです。
  - `FROM DISK`/`TAPE`/バックアップ デバイスはサポートされていません。
  - バックアップ セットはサポートされていません。
- `WITH` オプションはサポートされていません。 `DIFFERENTIAL`、`STATS`、`REPLACE`などの `WITH` を含む復元の試行は失敗します。
- `ASYNC RESTORE`:クライアント接続が切断されても、復元は続行されます。 接続が破棄された場合は、`sys.dm_operation_status` ビューで復元操作とデータベースの CREATE 操作および DROP 操作の状態を確認できます。 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) に関する記事をご覧ください。 

次のデータベース オプションを設定またはオーバーライドします。これらを後で変更することはできません。 

- `NEW_BROKER` (.bak ファイルでブローカーが有効になっていない場合)。 
- `ENABLE_BROKER` (.bak ファイルでブローカーが有効になっていない場合)。 
- `AUTO_CLOSE=OFF` (.bak ファイル内のデータベースに `AUTO_CLOSE=ON` が設定されている場合)。 
- `RECOVERY FULL` (.bak ファイル内のデータベースが `SIMPLE` または `BULK_LOGGED` 回復モードの場合)。
- メモリ最適化ファイル グループが追加され、(ソースの .bak ファイルに含まれていなかった場合) XTP という名前が付けられます。 
- 既存のメモリ最適化ファイル グループの名前が XTP に変更されます。 
- `SINGLE_USER` および `RESTRICTED_USER` のオプションは、`MULTI_USER` に変換されます。

制限事項: 

- 破損したデータベースのバックアップは、破損の種類によっては復元される可能性がありますが、破損が修正されるまで自動バックアップは実行されません。 この問題を回避するには、必ずソース SQL Managed Instance 上で `DBCC CHECKDB` を実行し、バックアップ `WITH CHECKSUM` を使用します。
- このドキュメントで説明されている制限 (たとえば `FILESTREAM` または `FILETABLE` オブジェクト) を含むデータベースの `.BAK` ファイルの復元は、SQL Managed Instance では復元できません。
- `.BAK` ファイルに複数のバックアップ セットが含まれている場合、復元できません。 
- `.BAK` ファイルに複数のログ ファイルが含まれている場合、復元できません。
- 8 TB を超えるデータベース、アクティブなインメモリ OLTP オブジェクト、または 280 個を超えるファイル数を含むバックアップは、General Purpose インスタンスでは復元できません。 
- 4 TB を超えるデータベース、または[リソースの制限](resource-limits.md)で説明されているサイズよりも合計サイズが大きいインメモリ OLTP オブジェクトを含むバックアップは、Business Critical インスタンスでは復元できません。
RESTORE ステートメントについては、[RESTORE ステートメント](/sql/t-sql/statements/restore-statements-transact-sql)に関する記事をご覧ください。

 > [!IMPORTANT]
 > 同じ制限が、組み込みのポイントインタイム リストア操作に適用されます。 たとえば、4 TB を超える General Purpose データベースは、Business Critical インスタンスで復元することはできません。 インメモリ OLTP ファイルまたは 280 個を超えるファイルを含む Business Critical データベースは、General Purpose インスタンスで復元することはできません。

### <a name="service-broker"></a>Service Broker

クロス インスタンス Service Broker メッセージ交換は、Azure SQL Managed Instance 間でのみサポートされます。

- `CREATE ROUTE`: `CREATE ROUTE` を `LOCAL` 以外の `ADDRESS` または別の Azure SQL Managed Instance の DNS 名で使用することはできません。
- `ALTER ROUTE`: `ALTER ROUTE` を `LOCAL` 以外の `ADDRESS` または別の Azure SQL Managed Instance の DNS 名で使用することはできません。

トランスポート セキュリティはサポートされていますが、ダイアログ セキュリティはサポートされていません。
- `CREATE REMOTE SERVICE BINDING` はサポートされていません。

Service Broker は既定で有効になっており、無効にできません。 次の ALTER DATABASE オプションはサポートされていません。
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>ストアド プロシージャ、関数、トリガー

- `NATIVE_COMPILATION` は、General Purpose レベルではサポートされていません。
- [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) の次のオプションはサポートされていません。 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- `sp_execute_external_scripts` はサポートされていません。 [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples) に関するセクションをご覧ください。
- `xp_cmdshell` はサポートされていません。 [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql) に関する記事をご覧ください。
- `Extended stored procedures` はサポートされておらず、これには `sp_addextendedproc` および `sp_dropextendedproc` が含まれます。 この機能は SQL Server では非推奨になる予定のため、サポートされません。 詳細については、[拡張ストアド プロシージャ](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming)に関するページを参照してください。
- `sp_attach_db`、`sp_attach_single_file_db`、`sp_detach_db` はサポートされていません。 [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)、[sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql) に関する各記事をご覧ください。

### <a name="system-functions-and-variables"></a>システム関数とシステム変数

次の変数、関数、ビューは異なる結果を返します。

- `SERVERPROPERTY('EngineEdition')` は値 8 を返します。 このプロパティは、SQL Managed Instance を一意に識別します。 [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql) に関する記事をご覧ください。
- `SERVERPROPERTY('InstanceName')` は NULL を返します。これは SQL Server に関して存在するインスタンスの概念が、SQL Managed Instance には該当しないためです。 [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql) に関する説明をご覧ください。
- `@@SERVERNAME` は、"接続可能な" 完全 DNS 名を返します (例: my-managed-instance.wcus17662feb9ce98.database.windows.net)。 [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql) に関する記事をご覧ください。 
- `SYS.SERVERS` は、"name" プロパティと "data_source" プロパティを表す `myinstance.domain.database.windows.net` のような、"接続可能な" 完全 DNS 名を返します。 [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql) に関する記事をご覧ください。
- `@@SERVICENAME` は NULL を返します。これは SQL Server に関して存在するサービスの概念が、SQL Managed Instance には該当しないためです。 [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql) に関する記事をご覧ください。
- `SUSER_ID` はサポートされています。 Azure AD ログインが sys.syslogins に含まれていない場合は、NULL を返します。 [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql) に関する記事をご覧ください。 
- `SUSER_SID` はサポートされていません。 正しくないデータが返されます。これは既知の一時的な問題です。 [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql) に関する記事をご覧ください。 

## <a name="environment-constraints"></a><a name="Environment"></a>環境の制約

### <a name="subnet"></a>Subnet
-  SQL Managed Instance をデプロイしたサブネットに他のリソース (たとえば仮想マシン) を配置することはできません。 別のサブネットを使用してこれらのリソースをデプロイしてください。
- サブネットには、十分な数の利用可能な [IP アドレス](connectivity-architecture-overview.md#network-requirements)が含まれている必要があります。 最小で、サブネットに少なくとも 32 個の IP アドレスを設定します。
- リージョンでデプロイできるインスタンスの仮想コア数と種類には、いくつかの[制約と制限](resource-limits.md#regional-resource-limitations)があります。
- サブネットに適用する必要がある[ネットワーク構成](connectivity-architecture-overview.md#network-requirements)があります。

### <a name="vnet"></a>VNet
- VNet はリソース モデルを使用してデプロイできます。VNet のクラシック モードはサポートされていません。
- SQL Managed Instance の作成後の SQL Managed Instance または VNet の別のリソース グループまたはサブスクリプションへの移動はサポートされていません。
- 2020 年 9 月 22 日より前に作成された仮想クラスターでホストされる SQL マネージド インスタンスでは、[グローバル ピアリング](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)がサポートされません。 ExpressRoute、または VNet ゲートウェイ経由の VNet 対 VNet を介してこのようなリソースに接続できます。

### <a name="failover-groups"></a>フェールオーバー グループ
システム データベースは、フェールオーバー グループのセカンダリ インスタンスにはレプリケートされません。 そのため、オブジェクトがセカンダリに手動で作成されていない限り、セカンダリ インスタンスではシステム データベースのオブジェクトに依存するシナリオは実現できません。

### <a name="tempdb"></a>TEMPDB
- `tempdb` の最大ファイル サイズは、General Purpose レベルではコアあたり 24 GB より大きくすることはできません。 Business Critical レベルでは、`tempdb` の最大サイズは SQL Managed Instance ストレージ サイズによって制限されます。 `Tempdb` ログ ファイルのサイズは、General Purpose レベルでは 120 GB に制限されています。 `tempdb` のサイズがコアあたり 24 GB を超える場合、または 120 GB を超えるログ データが生成される場合は、一部のクエリでエラーが返されます。
- `Tempdb` は常に 12 個のデータ ファイルに分割されます (1 個のプライマリ (マスターとも呼ばれる) データ ファイルと 11 個のプライマリ以外のデータ ファイル)。 ファイル構造を変更することも、`tempdb` に新しいファイルを追加することもできません。 
- [[メモリ最適化]`tempdb` メタデータ](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15#memory-optimized-tempdb-metadata) (新しい SQL Server 2019 のメモリ内データベース機能) は、サポートされていません。
- `tempdb` ではモデル データベースから初期オブジェクト リストが取得されないため、再起動後またはフェールオーバー後に、モデル データベースで作成されたオブジェクトを `tempdb` で自動作成できません。 再起動後またはフェールオーバー後に、`tempdb` でオブジェクトを手動で作成する必要があります。

### <a name="msdb"></a>MSDB

SQL Managed Instance の次の MSDB スキーマは、それぞれの定義済みロールによって所有されている必要があります。

- 一般的なロール
  - TargetServersRole
- [固定データベース ロール](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail ロール](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [統合サービスのロール](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - msdb
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> 定義済みのロール名、スキーマ名、スキーマ所有者を顧客が変更すると、サービスの通常の動作に影響します。 通常のサービス動作を保証するために、これらに加えられた変更は検出されるとすぐに、または次の最新のサービス更新時に、事前に定義済みの値に戻されます。

### <a name="error-logs"></a>エラー ログ

SQL Managed Instance では、エラー ログに詳細情報が書き込まれます。 エラー ログに記録される内部システム イベントが数多く存在します。 カスタムの手順を使用して、関連のない項目をフィルターで除外するエラー ログを読み取ります。 詳細については、Azure Data Studio の [SQL Managed Instance - sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) または [SQL Managed Instance の拡張機能 (プレビュー)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

- SQL Managed Instance の詳細については、[SQL Managed Instance の概要](sql-managed-instance-paas-overview.md)に関する記事を参照してください。
- 機能と比較の一覧については、[Azure SQL Managed Instance の機能の比較](../database/features-comparison.md)に関する記事を参照してください。
- 更新プログラムのリリースと既知の問題の状態については、[SQL Managed Instance のリリース ノート](../database/doc-changes-updates-release-notes.md)を参照してください
- 新しい SQL Managed Instance の作成方法を示したクイックスタートについては、[SQL Managed Instance の作成](instance-create-quickstart.md)に関するページを参照してください。
