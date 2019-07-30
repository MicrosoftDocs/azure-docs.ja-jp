---
title: Azure SQL Database と Data Warehouse の Transparent Data Encryption | Microsoft Docs
description: SQL Database と Data Warehouse の Transparent Data Encryption の概要。 このドキュメントでは、Transparent Data Encryption の利点と構成オプション (サービスによって管理された Transparent Data Encryption や Bring Your Own Key など) について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: dfc48fec948ab0cf3d16a49bb60eb3c274f2864e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723272"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>SQL Database と Data Warehouse の Transparent Data Encryption

Transparent Data Encryption (TDE) を使用すると、保存データを暗号化することで、悪意のあるオフライン アクティビティの脅威から Azure SQL Database、Azure SQL Managed Instance、Azure Data Warehouse を保護できます。 データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。 既定では、新しくデプロイされるすべての Azure SQL データベースで TDE が有効になります。 TDE を使用して、SQL Database の論理 **master** データベースを暗号化することはできません。  **master** データベースには、ユーザー データベースで TDE 操作を実行するために必要なオブジェクトが含まれています。

Azure SQL Managed Instance、Azure SQL Database の古いデータベース、Azure SQL Data Warehouse に対して TDE を手動で有効にする必要があります。  

Transparent Data Encryption は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。 このデータベース暗号化キーは、Transparent Data Encryption 保護機能によって保護されます。 保護機能は、サービスによって管理された証明書 (サービスによって管理された Transparent Data Encryption) または Azure Key Vault に格納されている非対称キー (Bring Your Own Key) です。 TDE プロテクターは、Azure SQL Database と Data Warehouse の場合はサーバー レベルで、Azure SQL Managed Instance の場合はインスタンス レベルで設定します。 *サーバー*という言葉は、別途明記されていない限り、このドキュメントではサーバーとインスタンスの両方を指します。

データベースの起動時に、暗号化されたデータベース暗号化キーが暗号化解除され、SQL Server データベース エンジン プロセスでデータベース ファイルの暗号化解除と再暗号化に使用されます。 Transparent Data Encryption では、データのリアルタイムの I/O 暗号化と暗号化解除がページ レベルで実行されます。 各ページは、メモリに読み込まれるときに暗号化解除され、ディスクに書き込まれる前に暗号化されます。 Transparent Data Encryption の概要については、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) に関する記事をご覧ください。

Azure 仮想マシン上で実行されている SQL Server も、Key Vault の非対称キーを使用できます。 構成手順は、SQL Database と SQL Managed Instance で非対称キーを使用する場合とは異なります。 詳細については、「[Azure Key Vault を使用する拡張キー管理 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)」をご覧ください。

## <a name="service-managed-transparent-data-encryption"></a>サービスによって管理された Transparent Data Encryption

Azure での Transparent Data Encryption の既定の設定では、データベース暗号化キーは組み込みのサーバー証明書によって保護されます。 組み込みのサーバー証明書はサーバーごとに一意であり、使用される暗号化アルゴリズムは AES 256 です。 データベースが geo レプリケーションのリレーションシップに含まれている場合、プライマリ データベースと geo セカンダリ データベースの両方が、プライマリ データベースの親サーバー キーによって保護されます。 2 つのデータベースが同じサーバーに接続されている場合は、同じ組み込み証明書も共有されます。  Microsoft では社内のセキュリティ ポリシーに準拠してこれらの証明書のローテーションが自動的に行われており、ルート キーは Microsoft 内のシークレット ストアによって保護されています。  お客様は、[Microsoft Trust Center](https://servicetrust.microsoft.com/) で入手可能な独立したサード パーティの監査レポートで、SQL Database が内部セキュリティ ポリシーに準拠していることを確認できます。

また、geo レプリケーションと復元のために、必要に応じてキーの移動と管理をシームレスに行います。

> [!IMPORTANT]
> 新しく作成されたすべての SQL データベースは、サービスによって管理された Transparent Data Encryption を使用して既定で暗号化されます。 Azure SQL Managed Instance データベース、2017 年 5 月より前に作成された既存の SQL データベース、復元、geo レプリケーション、データベース コピーによって作成された SQL データベースは既定では暗号化されません。

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>ユーザーが管理する Transparent Data Encryption - Bring Your Own Key

[Azure Key Vault 内のユーザーが管理するキーと TDE を併用](transparent-data-encryption-byok-azure-sql.md)すると、TDE 保護機能と呼ばれる、ユーザーが管理する非対称キーを使用して、データベース暗号化キー (DEK) を暗号化できます。  これは、一般に、Transparent Data Encryption に対する Bring Your Own Key (BYOK) のサポートとも呼ばれます。 BYOK シナリオでは、TDE 保護機能は、ユーザーが所有および管理する [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (Azure のクラウドベースの外部キー管理システム) 内に格納されます。 TDE 保護機能は[キー コンテナーによって生成するか、オンプレミスの HSM デバイスからキー コンテナーに転送する](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)ことができます。 TDE の DEK は、データベースのブート ページに格納され、TDE 保護機能によって暗号化および暗号化解除されます。これは Azure Key Vault に格納され、キー コンテナーに留まり続けます。  SQL Database には、DEK の暗号化解除と暗号化のために、ユーザーが所有するキー コンテナーへのアクセス許可が付与されている必要があります。 論理 SQL サーバーからキー コンテナーへのアクセス許可が取り消されると、データベースはアクセス不可となり、すべてのデータが暗号化されます。 Azure SQL Database の場合、TDE 保護機能は論理 SQL サーバー レベルで設定され、そのサーバーに関連付けられているすべてのデータベースによって継承されます。 [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance) (プレビュー段階の BYOK 機能) の場合、TDE 保護機能はインスタンス レベルで設定され、そのインスタンス上のすべての*暗号化された*データベースによって継承されます。 *サーバー*という言葉は、別途明記されていない限り、このドキュメントではサーバーとインスタンスの両方を指します。

TDE と Azure Key Vault の統合により、ユーザーは Azure Key Vault 機能を使用して、キーの交換、キー コンテナーのアクセス許可、キーのバックアップ、すべての TDE 保護機能の監査/レポートの有効化などのキー管理タスクを制御できます。 Key Vault はキーの一元管理を提供し、厳しく監視されたハードウェア セキュリティ モジュール (HSM) を利用します。また、キー管理とデータ管理の職務の分離を可能にすることでセキュリティ ポリシーの遵守を支援します。
Azure SQL Database、SQL Managed Instance (プレビュー段階の BYOK 機能)、Data Warehouse における Transparent Data Encryption と Azure Key Vault の統合 (Bring Your Own Key のサポート) の詳細については、[Transparent Data Encryption と Azure Key Vault の統合](transparent-data-encryption-byok-azure-sql.md)に関する記事を参照してください。

Transparent Data Encryption と Azure Key Vault の統合 (Bring Your Own Key のサポート) の使用を開始する場合は、[PowerShell で Key Vault の独自のキーを使用して Transparent Data Encryption を有効にする](transparent-data-encryption-byok-azure-sql-configure.md)方法のガイドをご覧ください。

## <a name="move-a-transparent-data-encryption-protected-database"></a>Transparent Data Encryption で保護されたデータベースを移動する

Azure 内での操作のためにデータベースを暗号化解除する必要はありません。 ソース データベースまたはプライマリ データベースの Transparent Data Encryption 設定は、ターゲットで透過的に継承されます。 含まれている操作は次のとおりです。

- geo リストア
- セルフサービスのポイントインタイム リストア
- 削除されたデータベースの復元
- アクティブ geo レプリケーション
- データベース コピーの作成
- Azure SQL Managed Instance にバックアップ ファイルを復元する

> [!IMPORTANT]
> サービス マネージド TDE によって暗号化されたデータベースのコピーのみの手動バックアップを取得することは、Azure SQL Managed Instance では許可されていません。暗号化に使用される証明書にアクセスできないためです。 ポイントインタイム リストア機能を使用して、この種類のデータベースを別のマネージド インスタンスに移動してください。

Transparent Data Encryption で保護されたデータベースをエクスポートした場合、データベースのエクスポートされたコンテンツは暗号化されません。 このエクスポートされたコンテンツは、暗号化されていない BACPAC ファイルに保存されます。 BACPAC ファイルを適切に保護し、新しいデータベースのインポートが完了してから Transparent Data Encryption を有効にする必要があります。

たとえば、BACPAC ファイルをオンプレミスの SQL Server インスタンスからエクスポートした場合、新しいデータベースのインポートされるコンテンツは自動的には暗号化されません。 同様に、BACPAC ファイルをオンプレミスの SQL Server インスタンスにエクスポートした場合も、新しいデータベースは自動的には暗号化されません。

唯一の例外は、SQL データベースとの間でエクスポートを実行する場合です。 Transparent Data Encryption は新しいデータベースで有効になりますが、BACPAC ファイル自体は暗号化されません。

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Azure portal で Transparent Data Encryption を管理する

Azure portal を使用して Transparent Data Encryption を構成するには、Azure の所有者、共同作成者、または SQL セキュリティ管理者として接続する必要があります。

Transparent Data Encryption のオン/オフはデータベース レベルで切り替えます。 データベースで Transparent Data Encryption を有効にするには、[Azure portal](https://portal.azure.com) に移動し、Azure 管理者または共同作成者アカウントでサインインします。 ユーザー データベースの Transparent Data Encryption 設定を見つけます。 既定では、サービスによって管理された Transparent Data Encryption が使用されます。 Transparent Data Encryption 証明書は、データベースを含むサーバーに対して自動的に生成されます。 Azure SQL Managed Instance の場合、T-SQL を使用し、データベースで Transparent Data Encryption のオン/オフを切り替えます。

![サービスによって管理された Transparent Data Encryption](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Transparent Data Encryption マスター キー (Transparent Data Encryption 保護機能とも呼ばれます) は、サーバー レベルで設定します。 Bring Your Own Key をサポートする Transparent Data Encryption を使用し、Key Vault のキーでデータベースを保護するには、サーバーの Transparent Data Encryption 設定を開きます。

![Bring Your Own Key をサポートする Transparent Data Encryption](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>PowerShell を使用して Transparent Data Encryption を管理する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

PowerShell を使用して Transparent Data Encryption を構成するには、Azure の所有者、共同作成者、または SQL セキュリティ管理者として接続する必要があります。

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Azure SQL Database と Data Warehouse のコマンドレット

Azure SQL Database と Data Warehouse には次のコマンドレットを使用します。

| コマンドレット | 説明 |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |データベースの Transparent Data Encryption を有効または無効にします。|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |データベースの Transparent Data Encryption の状態を取得します。 |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |データベースの暗号化の進行状況を確認します。 |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |SQL Server インスタンスに Key Vault キーを追加します。 |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Azure SQL Database サーバーの Key Vault キーを取得します。  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |SQL Server インスタンスの Transparent Data Encryption 保護機能を設定します。 |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Transparent Data Encryption 保護機能を取得します。 |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |SQL Server インスタンスから Key Vault キーを削除します。 |
|  | |

> [!IMPORTANT]
> Azure SQL Managed Instance の場合、T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) コマンドを使用し、データベース レベルで Transparent Data Encryption のオン/オフを切り替え、[サンプル PowerShell スクリプト](transparent-data-encryption-byok-azure-sql-configure.md)を確認してインスタンス レベルで Transparent Data Encryption を管理します。

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Transact-SQL を使用して Transparent Data Encryption を管理する

master データベースの **dbmanager** ロールの管理者またはメンバーであるログインを使用してデータベースに接続します。

| command | 説明 |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF によって、データベースを暗号化または暗号化解除します。 |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |データベースの暗号化の状態と、関連付けられているデータベース暗号化キーに関する情報を返します。 |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |各データ ウェアハウス ノードの暗号化の状態と、関連付けられているデータベース暗号化キーに関する情報を返します。 |
|  | |

Transact-SQL を使用して、Transparent Data Encryption 保護機能を Key Vault のキーに切り替えることはできません。 PowerShell または Azure portal を使用してください。

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>REST API を使用して Transparent Data Encryption を管理する

REST API を使用して Transparent Data Encryption を構成するには、Azure の所有者、共同作成者、または SQL セキュリティ管理者として接続する必要があります。
Azure SQL Database と Data Warehouse には次の一連のコマンドを使用します。

| command | 説明 |
| --- | --- |
|[サーバーの作成または更新](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|SQL Server インスタンスに Azure Active Directory ID を追加します (Key Vault へのアクセスを許可するために使用)。|
|[サーバー キーの作成または更新](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|SQL Server インスタンスに Key Vault キーを追加します。|
|[サーバー キーの削除](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|SQL Server インスタンスから Key Vault キーを削除します。|
|[サーバー キーの取得](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|SQL Server インスタンスから特定の Key Vault キーを取得します。|
|[サーバーごとにサーバー キーのリストを取得](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|SQL Server インスタンスの Key Vault キーを取得します。 |
|[暗号化保護機能の作成または更新](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|SQL Server インスタンスの Transparent Data Encryption 保護機能を設定します。|
|[暗号化保護機能の取得](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|SQL Server インスタンスの Transparent Data Encryption 保護機能を取得します。|
|[サーバーごとに暗号化保護機能のリストを取得](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|SQL Server インスタンスの Transparent Data Encryption 保護機能を取得します。 |
|[Transparent Data Encryption 構成の作成または更新](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|データベースの Transparent Data Encryption を有効または無効にします。|
|[Transparent Data Encryption 構成の取得](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|データベースの Transparent Data Encryption 構成を取得します。|
|[Transparent Data Encryption 構成の結果リストの取得](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|データベースの暗号化の結果を取得します。|

## <a name="next-steps"></a>次の手順

- Transparent Data Encryption の概要については、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) に関する記事をご覧ください。
- Azure SQL Database、Azure SQL Managed Instance、Data Warehouse の Bring Your Own Key をサポートする Transparent Data Encryption の詳細については、[Bring Your Own Key をサポートする Transparent Data Encryption](transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。
- Bring Your Own Key をサポートする Transparent Data Encryption の使用を開始する場合は、[PowerShell で Key Vault の独自のキーを使用して Transparent Data Encryption を有効にする](transparent-data-encryption-byok-azure-sql-configure.md)方法のガイドをご覧ください。
- Key Vault の詳細については、[Key Vault のドキュメント ページ](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)をご覧ください。
