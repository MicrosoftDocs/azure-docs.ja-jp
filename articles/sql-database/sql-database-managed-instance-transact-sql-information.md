---
title: Azure SQL Database Managed Instance の T-SQL の違い | Microsoft Docs
description: この記事では、Azure SQL Database Managed Instance と SQL Server の T-SQL の相違点について説明します
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 2ca2e4e98f56f7df5e81217bcda00179f05ff69e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070351"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database Managed Instance と SQL Server の T-SQL の相違点

この記事では、Azure SQL Database Managed Instance とオンプレミスの SQL Server データベース エンジンの構文および動作の相違点について簡単に説明します。 次の内容について説明します。<a name="Differences"></a>

- [可用性](#availability)には、[Always On](#always-on-availability) と[バックアップ](#backup)の相違点が含まれます。
- [セキュリティ](#security)には、[監査](#auditing)、[証明書](#certificates)、[資格情報](#credential)、[暗号化プロバイダー](#cryptographic-providers)、[ログイン/ユーザー](#logins-and-users)、[サービス キーとサービス マスター キー](#service-key-and-service-master-key)の相違点が含まれます。
- [構成](#configuration)には、[バッファー プール拡張機能](#buffer-pool-extension)、[照合順序](#collation)、[互換性レベル](#compatibility-levels)、[データベース ミラーリング](#database-mirroring)、[データベース オプション](#database-options)、[SQL Server エージェント](#sql-server-agent)、[テーブル オプション](#tables)の相違点が含まれます。
- [機能](#functionalities)には、[BULK INSERT/OPENROWSET](#bulk-insert--openrowset)、[CLR](#clr)、[DBCC](#dbcc)、[分散トランザクション](#distributed-transactions)、 [拡張イベント](#extended-events)、[外部ライブラリ](#external-libraries)、[Filestream と Filetable](#filestream-and-filetable)、[フルテキスト セマンティック検索](#full-text-semantic-search)、[リンク サーバー](#linked-servers)、[PolyBase](#polybase)、[レプリケーション](#replication)、[RESTORE](#restore-statement)、[Service Broker](#service-broker)、[ストアド プロシージャ、関数、およびトリガー](#stored-procedures-functions-and-triggers)が含まれます。
- VNet やサブネット構成などの[環境設定](#Environment)。
- [Managed Instance で動作が異なる機能](#Changes)。
- [一時的な制限事項と既知の問題](#Issues)。

マネージド インスタンスのデプロイ オプションは、オンプレミスの SQL Server データベース エンジンとの高い互換性を備えています。 SQL Server データベース エンジンのほとんどの機能がマネージド インスタンスでサポートされています。

![移行](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>可用性

### <a name="always-on-availability"></a>Always On

[高可用性](sql-database-high-availability.md)は Managed Instance に組み込まれており、ユーザーが制御することはできません。 次のステートメントはサポートされていません。

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) ステートメントの [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 句

### <a name="backup"></a>バックアップ

Managed Instance には自動バックアップがあるので、ユーザーは完全なデータベースの `COPY_ONLY` バックアップを作成できます。 差分、ログ、ファイル スナップショットの各バックアップはサポートされていません。

- Managed Instance では、Azure BLOB ストレージ アカウントにのみインスタンス データベースをバックアップできます。
  - `BACKUP TO URL` だけがサポートされています。
  - `FILE`、`TAPE`、およびバックアップ デバイスはサポートされていません。
- ほとんどの一般的な `WITH` オプションがサポートされています。
  - `COPY_ONLY` は必須です。
  - `FILE_SNAPSHOT` はサポートされていません。
  - テープ オプション: `REWIND`、`NOREWIND`、`UNLOAD`、`NOUNLOAD` はサポートされていません。
  - ログ固有のオプション: `NORECOVERY`、`STANDBY`、`NO_TRUNCATE` はサポートされていません。

制限事項: 

- Managed Instance では、最大 32 個のストライプを使用するバックアップにインスタンス データベースをバックアップできます。バックアップの圧縮を使用した場合、このバックアップで最大 4 TB のデータベースに十分対応できます。
- `BACKUP` コマンドを使用した場合の最大バックアップ ストライプ サイズは、最大 BLOB サイズである 195 GB です。 バックアップ コマンドのストライプ数を増やして、個々のストライプ サイズを小さくし、この制限を超えないようにします。

    > [!TIP]
    > 次の操作を実行すると、オンプレミス環境の SQL Server または仮想マシンからデータベースをバックアップするときに、この制限を回避できます。
    >
    > - `URL` にバックアップする代わりに `DISK` にバックアップします。
    > - バックアップ ファイルを BLOB ストレージにアップロードします。
    > - Managed Instance に復元します。
    >
    > Managed Instance で `Restore` コマンドを実行すると、アップロードされたバックアップ ファイルに対して異なる BLOB タイプが使用されるため、より大きな BLOB サイズがサポートされます。

T-SQL を使用したバックアップについては、[BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) に関する記事をご覧ください。

## <a name="security"></a>セキュリティ

### <a name="auditing"></a>監査

Azure SQL Database のデータベースと SQL Server のデータベースにおける監査の主な相違点は、次のとおりです。

- Azure SQL Database で Managed Instance のデプロイ オプションを使用すると、監査はサーバー レベルで機能します。 `.xel` ログ ファイルは、Azure BLOB ストレージに格納されます。
- Azure SQL Database で単一データベースとエラスティック プールのデプロイ オプションを使用すると、監査はデータベース レベルで機能します。
- オンプロミスまたは仮想マシンの SQL Server では、監査はサーバー レベルで機能します。 イベントは、ファイル システムまたは Windows イベント ログに格納されます。
 
マネージド インスタンスの XEvent 監査では、Azure Blob Storage のターゲットがサポートされます。 ファイル ログと Windows ログはサポートされていません。

Azure Blob Storage を監査するための `CREATE AUDIT` 構文の主な相違点は次のとおりです。

- `.xel` ファイルが配置されている Azure BLOB ストレージ コンテナーの URL の指定に使用できる新しい `TO URL` 構文が用意されています。
- Managed Instance は Windows ファイル共有にアクセスできないため、`TO FILE` 構文はサポートされていません。

詳細については、次を参照してください。 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [監査](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>証明書

Managed Instance　はファイル共有と Windows フォルダーにはアクセスできないので、次の制約が適用されます。

- `CREATE FROM`/`BACKUP TO` ファイルは、証明書ではサポートされていません。
- `FILE`/`ASSEMBLY` からの `CREATE`/`BACKUP` 証明書はサポートされていません。 秘密キー ファイルは使用できません。 

[CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) に関する記事、および [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql) に関する記事をご覧ください。 
 
**対処法**: 証明書または秘密キーのスクリプトを作成し、.sql ファイルとして保存してバイナリから作成します。

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>資格情報

Azure Key Vault と `SHARED ACCESS SIGNATURE` の ID だけがサポートされています。 Windows ユーザーはサポートされていません。

[CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) に関する記事、および [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql) に関する記事をご覧ください。

### <a name="cryptographic-providers"></a>暗号化プロバイダー

Managed Instance はファイルにアクセスできないため、暗号化プロバイダーは作成できません。

- `CREATE CRYPTOGRAPHIC PROVIDER` はサポートされていません。 [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql) に関する記事をご覧ください。
- `ALTER CRYPTOGRAPHIC PROVIDER` はサポートされていません。 [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql) に関する記事をご覧ください。

### <a name="logins-and-users"></a>ログインとユーザー

- `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY`、`FROM SID` を使用して作成された SQL ログインはサポートされています。 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql) に関する記事をご覧ください。
- [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 構文または [CREATE USER FROM Login [Azure AD Login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 構文を使用して作成された Azure Active Directory (Azure AD) サーバー プリンシパル (ログイン) がサポートされます (パブリック プレビュー)。 これらのログインは、サーバー レベルで作成されます。

    マネージド インスタンスは、`CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 構文で Azure AD データベース プリンシパルをサポートします。 この機能は、Azure AD 包含データベース ユーザーとも呼ばれます。

- `CREATE LOGIN ... FROM WINDOWS` 構文を使用して作成された Windows ログインはサポートされていません。 Azure Active Directory のログインとユーザーを使用します。
- インスタンスを作成した Azure AD ユーザーは、[無制限の管理特権](sql-database-manage-logins.md#unrestricted-administrative-accounts)を持ちます。
- 管理者以外の、データベース レベルの Azure AD ユーザーは、`CREATE USER ... FROM EXTERNAL PROVIDER` 構文を使用して作成できます。 [CREATE USER ...FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users) を参照してください。
- Azure AD サーバー プリンシパル (ログイン) は、1 つの Managed Instance 内のみで SQL 機能をサポートします。 同じ Azure AD テナント内か、または異なるテナント内かに関係なく、Azure AD ユーザーの場合、クロス インスタンス対話を必要とする機能はサポートされていません。 そのような機能の例として次のものがあります。

  - SQL トランザクション レプリケーション。
  - リンク サーバー。

- Azure AD グループにマップされている Azure AD ログインをデータベース所有者として設定することはサポートされていません。
- [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 句など、他の Azure AD プリンシパルを使用することによる Azure AD サーバー レベル プリンシパルの権限の借用はサポートされています。 EXECUTE AS の制限事項は次のとおりです。

  - 名前がログイン名と異なる場合、EXECUTE AS USER は Azure AD ユーザーに対してサポートされません。 たとえば、CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] 構文を使用してユーザーが作成されて、EXEC AS USER = _myAadUser_ を使用して権限借用が試行された場合などです。 Azure AD サーバー プリンシパル (ログイン) から **USER** を作成するときは、user_name を **LOGIN** と同じ login_name として指定します。
  - Azure AD プリンシパルを対象とした次の操作を実行できるのは、`sysadmin` ロールに属している SQL Server レベル プリンシパル (ログイン) のみです。

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Azure AD サーバー プリンシパル (ログイン) のパブリック プレビュー制限事項:

  - Managed Instance に対する Active Directory 管理者の制限事項:

    - Managed Instance の設定に使用された Azure AD 管理者は、Managed Instance 内での Azure AD サーバー プリンシパル (ログイン) の作成には使用できません。 `sysadmin` ロールである SQL Server アカウントを使用することにより、最初の Azure AD サーバー プリンシパル (ログイン) を作成する必要があります。 この一時的な制限事項は、Azure AD サーバー プリンシパル (ログイン) が一般提供されると削除されます。 Azure AD 管理者アカウントを使用してログインを作成しようとした場合、次のエラーが表示されます。`Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - 現時点では、マスター データベースに作成される最初の Azure AD ログインは、[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER を使用することにより、`sysadmin` である標準の SQL Server アカウント (Azure AD ではない) が作成する必要があります。 一般提供後、この制限事項は削除されます。 続いて、Managed Instance の Active Directory 管理者によって初期 Azure AD ログインを作成できます。
    - SQL Server Management Studio または SqlPackage で使用される DacFx (エクスポート/インポート) は、Azure AD ログインではサポートされていません。 この制限事項は、Azure AD サーバー プリンシパル (ログイン) が一般提供されると削除されます。
    - SQL Server Management Studio での Azure AD サーバー プリンシパル (ログイン) の使用:

      - 任意の認証ログインを使用した Azure AD ログインのスクリプトはサポートされていません。
      - IntelliSense は、CREATE LOGIN FROM EXTERNAL PROVIDER ステートメントを認識せず、赤の下線を表示します。

- Managed Instance のプロビジョニング プロセスによって作成されるサーバー レベル プリンシパル ログイン、`securityadmin` や `sysadmin` などのサーバー ロールのメンバー、あるいはサーバー レベルの ALTER ANY LOGIN アクセス許可を持つその他のログインのみが、Managed Instance のマスター データベースに Azure AD サーバー プリンシパル (ログイン) を作成できます。
- ログインが SQL プリンシパルの場合、作成コマンドを使用して Azure AD アカウントのログインを作成できるのは、`sysadmin` ロールに属しているログインのみです。
- Azure AD ログインは、Azure SQL Database Managed Instance に使用されるのと同じディレクトリ内の Azure AD のメンバーでなければなりません。
- Azure AD サーバー プリンシパル (ログイン) は、SQL Server Management Studio 18.0 プレビュー 5 から、オブジェクト エクスプローラーに表示されます。
- Azure AD サーバー プリンシパル (ログイン) と Azure AD 管理者アカウントとの重複は許可されます。 プリンシパルを解決してアクセス許可を Managed Instance に適用するとき、Azure AD の管理者よりも Azure AD サーバー プリンシパル (ログイン) が優先されます。
- 認証時、認証するプリンシパルを解決するために次の順序が適用されます。

    1. Azure AD アカウントが、Azure AD サーバー プリンシパル (ログイン) に直接マップされているものとして存在する (sys.server_principals に type "E" と指定されている) 場合、アクセスを許可し、Azure AD サーバー プリンシパル (ログイン) のアクセス権を適用する。
    2. Azure AD アカウントが、Azure AD サーバー プリンシパル (ログイン) にマップされている Azure AD グループのメンバーである (sys.server_principals に type "X" と指定されている) 場合、アクセスを許可し、Azure AD グループ ログインのアクセス権を適用する。
    3. Azure AD アカウントが Managed Instance 用のポータル構成の特殊な Azure AD 管理者である (Managed Instance システム ビューに存在しない) 場合は、Managed Instance の Azure AD 管理者の特殊な固定アクセス許可を適用する (レガシー モード)。
    4. Azure AD アカウントが、データベース内の Azure AD ユーザーに直接マップされたものとして存在する (sys.database_principals に type "E" と指定されている) 場合、アクセスを許可し、Azure AD データベース ユーザーのアクセス許可を適用する。
    5. Azure AD アカウントが、データベース内の Azure AD ユーザーにマップされた Azure AD グループのメンバーである (sys.database_principals に type "X" と指定されている) 場合、アクセスを許可し、Azure AD グループ ログインのアクセス許可を適用する。
    6. Azure AD ユーザー アカウントまたは Azure AD グループ アカウントのいずれかにマップされている Azure AD ログインがあり、認証しているユーザーを解決する場合、この Azure AD ログインのすべてのアクセス許可が適用される。

### <a name="service-key-and-service-master-key"></a>サービス キーとサービス マスター キー

- [マスター キーのバックアップ](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。
- [マスター キーの復元](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。
- [サービス マスター キーのバックアップ](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。
- [サービス マスター キーの復元](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)はサポートされていません (SQL Database サービスによって管理されます)。

## <a name="configuration"></a>構成

### <a name="buffer-pool-extension"></a>バッファー プール拡張機能

- [バッファー プール拡張機能](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)はサポートされていません。
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` はサポートされていません。 [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql) に関する記事をご覧ください。

### <a name="collation"></a>Collation

既定のインスタンスの照合順序は `SQL_Latin1_General_CP1_CI_AS` であり、作成パラメーターとして指定できます。 「[照合順序](https://docs.microsoft.com/sql/t-sql/statements/collations)」をご覧ください。

### <a name="compatibility-levels"></a>互換性レベル

- サポートされている互換性レベルは、100、110、120、130、および 140 です。
- 100 より低い互換性レベルはサポートされていません。
- 新しいデータベースの既定の互換性レベルは 140 です。 互換性レベルが 100 以上のデータベースが復元された場合、互換性レベルは変更されません。

[ALTER DATABASE の互換性レベル](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)に関する記事をご覧ください。

### <a name="database-mirroring"></a>データベース ミラーリング

データベース ミラーリングはサポートされていません。

- `ALTER DATABASE SET PARTNER` オプションと `SET WITNESS` オプションはサポートされていません。
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` はサポートされていません。

詳細については、[ALTER DATABASE の SET PARTNER と SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring)、および [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql) の説明をご覧ください。

### <a name="database-options"></a>データベース オプション

- 複数のログ ファイルはサポートされていません。
- インメモリ オブジェクトは、General Purpose サービス レベルではサポートされていません。 
- General Purpose インスタンスあたり 280 ファイル (データベースあたり最大 280 ファイル) の制限があります。 General Purpose レベルのデータ ファイルとログ ファイルの両方がこの制限にカウントされます。 [Business Critical レベルでは、データベースあたり 32,767 ファイルがサポートされます](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。
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

詳細については、[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql) に関する記事をご覧ください。

#### <a name="alter-database-statement"></a>ALTER DATABASE ステートメント

ファイルの一部のプロパティは設定または変更できません。

- `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL ステートメントでは、ファイル パスは指定できません。 ファイルはマネージド インスタンスによって自動的に配置されるため、スクリプトから `FILENAME` を削除します。 
- `ALTER DATABASE` ステートメントを使用してファイル名を変更することはできません。

次のオプションは既定で設定されており、変更することはできません。

- `MULTI_USER`
- `ENABLE_BROKER ON`
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

詳細については、[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options) に関する記事をご覧ください。

### <a name="sql-server-agent"></a>SQL Server エージェント

- Managed Instance では現在、SQL Server エージェントの有効化/無効化はサポートされていません。 SQL エージェントは常時稼動状態となります。
- SQL Server エージェントの設定は読み取り専用です。 `sp_set_agent_properties` プロシージャは、Managed Instance ではサポートされていません。 
- [ジョブ]
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
  - Managed Instance は、外部リソース (例: robocopy を介したネットワーク共有) にはアクセスできません。 
  - SQL Server Analysis Services はサポートされていません。
- 通知は部分的にサポートされています。
- 電子メール通知がサポートされていますが、データベース メール プロファイルを構成する必要があります。 SQL Server エージェントで使用できるデータベース メール プロファイルは 1 つだけであり、`AzureManagedInstance_dbmail_profile` という名前である必要があります。 
  - Pager はサポートされていません。 
  - NetSend はサポートされていません。
  - アラートはまだサポートされていません。
  - プロキシはサポートされていません。 
- EventLog はサポートされていません。

次の機能は現在サポートされていませんが、今後有効になります。

- プロキシ
- アイドル状態の CPU でのジョブのスケジューリング
- エージェントの有効化または無効化
- アラート

SQL Server エージェントについては、「[SQL Server エージェント](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)」をご覧ください。

### <a name="tables"></a>テーブル

次のテーブルはサポートされていません。

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

テーブルの作成および変更方法については、[CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) に関する記事および [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) に関する記事をご覧ください。

## <a name="functionalities"></a>機能

### <a name="bulk-insert--openrowset"></a>一括挿入/openrowset

Managed Instance はファイル共有や Windows フォルダーにアクセスできないため、ファイルは Azure BLOB ストレージからインポートする必要があります。

- が、Azure BLOB ストレージからファイルをインポートしている間、`BULK INSERT` コマンドで `DATASOURCE` が必要です。 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql) に関する記事をご覧ください。
- Azure BLOB ストレージからのファイルの内容を読み取る場合、`OPENROWSET` 関数で `DATASOURCE` が必要です。 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql) に関する記事をご覧ください。

### <a name="clr"></a>CLR

Managed Instance　はファイル共有と Windows フォルダーにはアクセスできないので、次の制約が適用されます。

- `CREATE ASSEMBLY FROM BINARY` だけがサポートされています。 [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql) に関する記事をご覧ください。 
- `CREATE ASSEMBLY FROM FILE` はサポートされていません。 [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql) に関する記事をご覧ください。
- `ALTER ASSEMBLY` ではファイルを参照できません。 [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql) に関する記事をご覧ください。

### <a name="dbcc"></a>DBCC

SQL Server で有効になっている、ドキュメントに記載されていない DBCC ステートメントは、Managed Instance ではサポートされていません。

- `Trace flags` はサポートされていません。 [トレース フラグ](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)に関する記事をご覧ください。
- `DBCC TRACEOFF` はサポートされていません。 [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) に関する記事をご覧ください。
- `DBCC TRACEON` はサポートされていません。 [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) に関する記事をご覧ください。

### <a name="distributed-transactions"></a>分散トランザクション

MSDTC も[エラスティック トランザクション](sql-database-elastic-transactions-overview.md)も現在、Managed Instance ではサポートされていません。

### <a name="extended-events"></a>拡張イベント

拡張イベント (XEvents) の一部の Windows 固有のターゲットはサポートされていません。

- `etw_classic_sync` ターゲットはサポートされていません。 Azure BLOB ストレージに `.xel` ファイルを保存します。 「[etw_classic_sync ターゲット](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)」をご覧ください。
- `event_file` ターゲットはサポートされていません。 Azure BLOB ストレージに `.xel` ファイルを保存します。 「[event_file ターゲット](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)」をご覧ください。

### <a name="external-libraries"></a>外部ライブラリ

In-Database R および In-Database Python 外部ライブラリはまだサポートされていません。 「[SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)」をご覧ください。

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

詳細については、[FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) に関する記事、および [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server) に関する記事をご覧ください。

### <a name="full-text-semantic-search"></a>フルテキスト セマンティック検索

[セマンティック検索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server)はサポートされていません。

### <a name="linked-servers"></a>リンク サーバー

マネージド インスタンスのリンク サーバーがサポートするターゲットの数は限られています。

- サポートされるターゲットは SQL Server と SQL Database です。
- サポートされていないターゲットは、ファイル、Analysis Services、他の RDBMS です。

Operations

- クロス インスタンス書き込みトランザクションはサポートされていません。
- リンク サーバーの削除で `sp_dropserver` がサポートされています。 [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql) に関する記事をご覧ください。
- SQL Server インスタンスでのみ、`OPENROWSET` 関数を使用してクエリを実行できます。 これらは、マネージド、オンプレミス、仮想マシンのいずれかで配置できます。 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql) に関する記事をご覧ください。
- SQL Server インスタンスでのみ、`OPENDATASOURCE` 関数を使用してクエリを実行できます。 これらは、マネージド、オンプレミス、仮想マシンのいずれかで配置できます。 プロバイダーとしてサポートされる値は、`SQLNCLI`、`SQLNCLI11`、`SQLOLEDB` だけです。 例: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql) に関する記事をご覧ください。

### <a name="polybase"></a>PolyBase

HDFS または Azure BLOB ストレージ内のファイルを参照する外部テーブルはサポートされていません。 PolyBase については、[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) に関する記事をご覧ください。

### <a name="replication"></a>レプリケーション

マネージド インスタンスのパブリック プレビューで、レプリケーションを使用できます。 レプリケーションについては、「[SQL Server のレプリケーション](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)」をご覧ください。

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
- `WITH` オプションはサポートされていません (`DIFFERENTIAL`、`STATS` などは使用不可)。
- `ASYNC RESTORE`:クライアント接続が切断されても、復元は続行されます。 接続が破棄された場合は、`sys.dm_operation_status` ビューで復元操作とデータベースの CREATE 操作および DROP 操作の状態を確認できます。 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) に関する記事をご覧ください。 

次のデータベース オプションを設定またはオーバーライドします。これらを後で変更することはできません。 

- `NEW_BROKER` (.bak ファイルでブローカーが有効になっていない場合)。 
- `ENABLE_BROKER` (.bak ファイルでブローカーが有効になっていない場合)。 
- `AUTO_CLOSE=OFF` (.bak ファイル内のデータベースに `AUTO_CLOSE=ON` が設定されている場合)。 
- `RECOVERY FULL` (.bak ファイル内のデータベースが `SIMPLE` または `BULK_LOGGED` 回復モードの場合)。
- メモリ最適化ファイル グループが追加され、(ソースの .bak ファイルに含まれていなかった場合) XTP という名前が付けられます。 
- 既存のメモリ最適化ファイル グループの名前が XTP に変更されます。 
- `SINGLE_USER` および `RESTRICTED_USER` のオプションは、`MULTI_USER` に変換されます。

制限事項: 

- `.BAK` ファイルに複数のバックアップ セットが含まれている場合、復元できません。 
- `.BAK` ファイルに複数のログ ファイルが含まれている場合、復元できません。
- .bak に `FILESTREAM` データが含まれていると、復元は失敗します。
- アクティブなインメモリ オブジェクトが存在するデータベースが含まれたバックアップは、General Purpose インスタンスで復元することはできません。 RESTORE ステートメントについては、[RESTORE ステートメント](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)に関する記事をご覧ください。

### <a name="service-broker"></a>Service Broker

クロス インスタンス Service Broker はサポートされていません。

- `sys.routes`:前提条件として sys.routes からアドレスを選択する必要があります。 アドレスは、各ルートで LOCAL である必要があります。 [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql) に関する記事をご覧ください。
- `CREATE ROUTE`:`LOCAL` 以外の `ADDRESS` で `CREATE ROUTE` を使用することはできません。 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql) に関する記事をご覧ください。
- `ALTER ROUTE`:`LOCAL` 以外の `ADDRESS` で `ALTER ROUTE` を使用することはできません。 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql) に関する記事をご覧ください。 

### <a name="stored-procedures-functions-and-triggers"></a>ストアド プロシージャ、関数、トリガー

- `NATIVE_COMPILATION` は、General Purpose レベルではサポートされていません。
- [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) の次のオプションはサポートされていません。 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` はサポートされていません。 [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples) に関するセクションをご覧ください。
- `xp_cmdshell` はサポートされていません。 [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql) に関する記事をご覧ください。
- `Extended stored procedures` はサポートされておらず、これには `sp_addextendedproc`  および `sp_dropextendedproc` が含まれます。 [拡張ストアド プロシージャ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)に関する記事をご覧ください。
- `sp_attach_db`、`sp_attach_single_file_db`、`sp_detach_db` はサポートされていません。 [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)、[sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql) に関する各記事をご覧ください。

## <a name="Environment"></a>環境の制約

### <a name="subnet"></a>Subnet
- ご利用の Managed Instance 用に予約されているサブネットに、その他のリソース (仮想マシンなど) を配置することはできません。 これらのリソースはその他のサブネットに配置します。
- サブネットには、十分な数の利用可能な [IP アドレス](sql-database-managed-instance-connectivity-architecture.md#network-requirements)が含まれている必要があります。 最小値は 16 ですが、サブネットに少なくとも 32 個の IP アドレスを含めることをお勧めします。
- [マネージド インスタンスのサブネットにサービス エンドポイントを関連付けることはできません](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 仮想ネットワークの作成時に、サービス エンドポイント オプションが無効になっていることを確認してください。
- リージョンでデプロイできるインスタンスの仮想コア数と種類には、いくつかの[制約と制限](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)があります。
- [サブネットで適用される必要があるセキュリティ規則](sql-database-managed-instance-connectivity-architecture.md#network-requirements)があります。

### <a name="vnet"></a>VNet
- VNet はリソース モデルを使用してデプロイできます。VNet のクラシック モードはサポートされていません。
- VNet が[グローバル ピアリング](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)を使用して接続されている場合、App Service Environment、ロジック アプリ、Managed Instance (geo レプリケーション、トランザクション レプリケーション、またはリンクされたサーバー経由で使用) など、一部のサービスでは、さまざまなリージョンにある Managed Instance にアクセスできません。 ExpressRoute、または VNet ゲートウェイ経由の VNet 対 VNet を介してこのようなリソースに接続できます。

## <a name="Changes"></a>動作の変更

次の変数、関数、ビューは異なる結果を返します。

- `SERVERPROPERTY('EngineEdition')` は値 8 を返します。 このプロパティは、マネージド インスタンスを一意に識別します。 [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql) に関する記事をご覧ください。
- `SERVERPROPERTY('InstanceName')` は NULL を返します。これは SQL Server に関して存在するインスタンスの概念が、Managed Instance には該当しないためです。 [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql) に関する説明をご覧ください。
- `@@SERVERNAME` は、"接続可能な" 完全 DNS 名を返します (例: my-managed-instance.wcus17662feb9ce98.database.windows.net)。 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql) に関する記事をご覧ください。 
- `SYS.SERVERS` は、"name" プロパティと "data_source" プロパティを表す `myinstance.domain.database.windows.net` のような、"接続可能な" 完全 DNS 名を返します。 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql) に関する記事をご覧ください。
- `@@SERVICENAME` は NULL を返します。これは SQL Server に関して存在するサービスの概念が、Managed Instance には該当しないためです。 [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql) に関する記事をご覧ください。
- `SUSER_ID` がサポートされています。 Azure AD ログインが sys.syslogins に含まれていない場合は、NULL を返します。 [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql) に関する記事をご覧ください。 
- `SUSER_SID` はサポートされていません。 正しくないデータが返されます。これは既知の一時的な問題です。 [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql) に関する記事をご覧ください。 

## <a name="Issues"></a>既知の問題と制限事項

### <a name="tempdb-size"></a>TEMPDB のサイズ

`tempdb` の最大ファイル サイズは、General Purpose レベルではコアあたり 24 GB より大きくすることはできません。 Business Critical レベルでは、`tempdb` の最大サイズはインスタンス ストレージ サイズで制限されます。 `tempdb` は常に 12 個のデータ ファイルに分割されます。 ファイルあたりのこの最大サイズは変更できず、`tempdb` に新しいファイルを追加することはできません。 一部のクエリで `tempdb` のサイズがコアあたり 24 GB を超える必要がある場合は、エラーが返されます。 `tempdb` は、インスタンスが開始またはフェールオーバーしたときに、常に空のデータベースとして再作成され、`tempdb` で行われたどの変更も保持されません。 

### <a name="cant-restore-contained-database"></a>包含データベースを復元できない

Managed Instance は[包含データベース](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)を復元できません。 既存のデータベースのポイントインタイム リストアは、Managed Instance には機能しません。 この問題は、まもなく解決されます。 その間、Managed Instance に配置されるデータベースから、包含オプションを削除することをお勧めします。 運用データベースの包含オプションを使用しないでください。 

### <a name="exceeding-storage-space-with-small-database-files"></a>小さなデータベース ファイルによる記憶域の超過

インスタンスが Azure ストレージの制限に達する可能性があるため、`CREATE DATABASE`、`ALTER DATABASE ADD FILE`、および `RESTORE DATABASE` ステートメントが失敗することがあります。

各 General Purpose Managed Instance には、Azure Premium ディスク領域用に予約された 最大 35 TB のストレージがあります。 各データベース ファイルは、個別の物理ディスクに配置されます。 ディスク サイズとして、128 GB、256 GB、512 GB、1 TB、4 TB のいずれかを指定できます。 ディスク上の未使用領域については請求されませんが、Azure Premium ディスクのサイズ合計が 35 TB を超えることはできません。 場合によっては、内部の断片化のために、合計で 8 TB を必要としない Managed Instance が、記憶域のサイズに関する 35 TB の Azure での制限を超える場合があります。

たとえば、General Purpose Managed Instance には、4 TB のディスクに配置されているサイズが 1.2 TB のファイルが 1 つあります。 また、個別の 128 GB ディスクに配置される、サイズがそれぞれ 1 GB のファイルが 248 あります。 次の点に注意してください。

- 割り当てられるディスク ストレージの合計サイズは、1 x 4 TB + 248 x 128 GB = 35 TB となります。
- インスタンス上のデータベースの予約済み領域の合計は、1 x 1.2 TB + 248 x 1 GB = 1.4 TB となります。

この例は、特定の状況下では、特殊なファイルの配分のために、Managed Instance が、想定していなかったアタッチ済み Azure Premium ディスクに予約されている 35 TB の制限に到達する可能性があることを示しています。

この例では既存のデータベースは引き続き機能し、新しいファイルを追加しない限りは問題なく拡張できます。 すべてのデータベースの合計サイズがインスタンス サイズの上限に到達しない場合でも、新しいディスク ドライブ用の十分な領域がないため、新しいデータベースの作成や復元はできません。 その場合に返されるエラーは明確ではありません。

システム ビューを使用して、[残りのファイルの数を特定](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)できます。 この制限に達した場合は、[DBCC SHRINKFILE ステートメントを使用して、より小さなファイルをいくつか空にして削除](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)してみるか、[この制限のない Business Critical レベル](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)に切り替えてください。

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>データベースの復元時の SAS キーの構成が正しくない

.bak ファイルを読み取る `RESTORE DATABASE` は、`CREDENTIAL` の Shared Access Signature が正しくない場合、.bak ファイルの読み取りの再試行を繰り返し、長時間経過した後にエラーを返すことがあります。 データベースを復元する前に RESTORE HEADERONLY を実行して、SAS キーが正しいことを確認します。
Azure portal を使用して生成された SAS キーから、先頭の `?` を必ず削除してください。

### <a name="tooling"></a>ツール

SQL Server Management Studio と SQL Server Data Tools には、Managed Instance にアクセスする際に問題がある可能性があります。

- 現在のところ、SQL Server Data Tools で Azure AD サーバー プリンシパル (ログイン) とユーザー (パブリック プレビュー) を使用することはできません。
- Azure AD サーバー プリンシパル (ログイン) とユーザー (パブリック プレビュー) のスクリプトは、SQL Server Management Studio ではサポートされていません。

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>一部のビュー、ログ、およびメッセージでの不正なデータベース名

複数のシステム ビュー、パフォーマンス カウンター、エラー メッセージ、XEvent、およびエラー ログ エントリで、実際のデータベース名ではなく GUID データベース識別子が表示されています。 将来、実際のデータベース名に置き換えられるため、これらの GUID 識別子には依存しないでください。

### <a name="database-mail"></a>データベース メール

[sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) プロシージャの `@query` パラメーターは機能しません。

### <a name="database-mail-profile"></a>データベース メール プロファイル

SQL Server エージェントによって使用されるデータベース メールプロファイルは、`AzureManagedInstance_dbmail_profile` という名前である必要があります。 その他のデータベース メール プロファイルの名前に関する制限はありません。

### <a name="error-logs-arent-persisted"></a>エラー ログが非永続的である

Managed Instance で利用可能なエラー ログは永続的ではなく、このログのサイズは、最大ストレージ上限には含まれません。 フェールオーバーが発生した場合、エラー ログは自動的に消去される可能性があります。

### <a name="error-logs-are-verbose"></a>エラー ログが詳細である

Managed Instance では、エラー ログに詳細情報が書き込まれ、その多くは関連のない内容です。 エラー ログの情報量は将来、減らされる予定です。

**対処法:** カスタムの手順を使用して、関連のない項目をフィルターで除外するエラー ログを読み取ります。 詳細については、[Managed Instance - sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) に関する記事を参照してください。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>同じインスタンス内にある 2 つのデータベース上でトランザクション スコープがサポートされない

`TransactionScope` クラス (.NET) は、同じトランザクション スコープ下では、同一インスタンス内にある 2 つのデータベースに対して 2 つのクエリが送信された場合に機能しません。

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

このコードは同一インスタンス内でデータを操作しますが、MSDTC を必要とします。

**対処法:** 2 つの接続を使用する代わりに [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) を使って、接続コンテキスト内で他のデータベースを使用します。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR モジュールとリンク サーバーでローカル IP アドレスを参照できないことがある

Managed Instance に配置された CLR モジュールと、現在のインスタンスを参照しているリンク サーバーまたは分散クエリでは、ローカル インスタンスの IP を解決できないことがあります。 このエラーは一時的な問題です。

**対処法:** 可能であれば、CLR モジュールでコンテキスト接続を使用します。

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>サービス管理キーを使用して TDE で暗号化されたデータベースでは、ユーザーが開始したバックアップはサポートされない

サービス管理 Transparent Data Encryption (TDE) を使用して暗号化されたデータベースでは、`BACKUP DATABASE ... WITH COPY_ONLY` は実行できません。 サービス管理 TDE では、バックアップを内部の TDE のキーで暗号化するように強制します。 キーはエクスポートできないので、バックアップを復元することはできません。

**対処法:** 自動バックアップとポイントインタイム リストアを使用するか、代わりに[顧客管理 (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) を使用します。 また、データベースで暗号化を無効にすることができます。

## <a name="next-steps"></a>次の手順

- Managed Instance の詳細については、[Managed Instance の概要](sql-database-managed-instance.md)に関するページを参照してください
- 機能と比較の一覧については、「[Azure SQL Database の機能の比較](sql-database-features.md)」を参照してください。
- 新しい Managed Instance の作成方法を示したクイックスタートについては、[Managed Instance の作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
