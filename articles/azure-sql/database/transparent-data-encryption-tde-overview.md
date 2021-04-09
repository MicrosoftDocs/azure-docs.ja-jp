---
title: 透過的なデータ暗号化
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL Database、Azure SQL Managed Instance、および Azure Synapse Analytics の透過的なデータ暗号化の概要。 このドキュメントでは、Transparent Data Encryption の利点と構成オプション (サービスによって管理された Transparent Data Encryption や Bring Your Own Key など) について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: 8fbbd7a2aabc9de417f1eefd2513edba3119bfc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791394"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>SQL Database、SQL Managed Instance および Azure Synapse Analytics の透過的なデータ暗号化
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) は、保存データを暗号化することによって、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics を悪意のあるオフライン アクティビティの脅威から保護するために役立ちます。 データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。 新しくデプロイされるすべての SQL Database では、TDE は既定で有効になっています。Azure SQL Database や Azure SQL Managed Instance の古いデータベースでは手動で有効にする必要があります。 Azure Synapse Analytics には、TDE を手動で有効にする必要があります。

TDE では、ページ レベルでデータのリアルタイム I/O 暗号化と暗号化解除が実行されます。 各ページは、メモリに読み込まれるときに暗号化解除され、ディスクに書き込まれる前に暗号化されます。 TDE では、データベース暗号化キー (DEK) という対称キーを使用してデータベース全体のストレージが暗号化されます。 データベースの起動時に、DEK の暗号化は解除され、SQL Server データベース エンジン プロセスでデータベース ファイルの暗号化解除と再暗号化を行うために使用されます。 DEK は TDE 保護機能によって保護されます。 TDE 保護機能は、サービスによって管理される証明書 (サービスによって管理される透過的なデータ暗号化) または [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md) に格納される非対称キー (顧客によって管理される透過的なデータ暗号化) のどちらかです。

Azure SQL Database と Azure Synapse の場合、TDE 保護機能は[サーバー](logical-servers.md) レベルで設定され、そのサーバーに関連付けられているすべてのデータベースによって継承されます。 Azure SQL Managed Instance の場合、TDE 保護機能はインスタンス レベルで設定され、そのインスタンス上のすべての暗号化されたデータベースによって継承されます。 *サーバー* という言葉は、別途明記されていない限り、このドキュメントではサーバーとインスタンスの両方を指します。

> [!IMPORTANT]
> SQL Database で新しく作成されたすべてのデータベースは、サービスによって管理される透過的なデータ暗号化を使用して既定で暗号化されます。 2017 年 5 月より前に作成された既存の SQL データベースと、復元、geo レプリケーション、データベース コピーによって作成された SQL データベースは、既定では暗号化されません。 2019 年 2 月より前に作成された既存の SQL Managed Instance データベースは、既定では暗号化されません。 復元によって作成された SQL Managed Instance データベースでは、ソースから暗号化の状態が継承されます。

> [!NOTE]
> TDE を使用して、Azure SQL Database および Azure SQL Managed Instance の **マスター** データベースなどのシステム データベースを暗号化することはできません。 **master** データベースには、ユーザー データベースで TDE 操作を実行するために必要なオブジェクトが含まれています。 機密データをシステム データベースに保存しないようにしてください。 現在、マスターを含むシステム データベースを暗号化する[インフラストラクチャ暗号化](transparent-data-encryption-byok-overview.md#doubleencryption)がロールアウトされています。 

## <a name="service-managed-transparent-data-encryption"></a>サービスによって管理された Transparent Data Encryption

Azure では、TDE の既定の設定は、組み込みのサーバー証明書によって保護される DEK です。 組み込みのサーバー証明書はサーバーごとに一意であり、使用される暗号化アルゴリズムは AES 256 です。 データベースが geo レプリケーションのリレーションシップに含まれている場合、プライマリ データベースと geo セカンダリ データベースの両方が、プライマリ データベースの親サーバー キーによって保護されます。 2 つのデータベースが同じサーバーに接続されている場合は、同じ組み込み証明書も共有されます。 Microsoft では社内のセキュリティ ポリシーに準拠してこれらの証明書のローテーションが自動的に行われており、ルート キーは Microsoft 内のシークレット ストアによって保護されています。 お客様は、[Microsoft Trust Center](https://servicetrust.microsoft.com/) で入手可能な独立したサード パーティの監査レポートで、SQL Database および SQL Managed Instance が内部セキュリティ ポリシーに準拠していることを確認できます。

また、geo レプリケーションと復元のために、必要に応じてキーの移動と管理をシームレスに行います。

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>ユーザーが管理する Transparent Data Encryption - Bring Your Own Key

ユーザーによって管理される TDE は、TDE に対する Bring Your Own Key (BYOK) サポートとも呼ばれます。 このシナリオでは、DEK を暗号化する TDE 保護機能はユーザーによって管理される非対象キーであり、それはユーザーが所有して管理している Azure Key Vault (Azure のクラウドベースの外部キー管理システム) 内に格納され、そのキー コンテナーから出ることはありません。 TDE 保護機能は[キー コンテナーによって生成するか、キー コンテナーに転送する](../../key-vault/keys/hsm-protected-keys.md) (オンプレミスのハードウェア セキュリティ モジュール (HSM) デバイスから転送する) ことができます。 SQL Database、SQL Managed Instance、および Azure Synapse には、DEK の暗号化解除と暗号化のために、ユーザーが所有するキー コンテナーへのアクセス許可が付与されている必要があります。 サーバーからキー コンテナーへのアクセス許可が取り消されると、データベースはアクセス不可となり、すべてのデータが暗号化されます。

TDE と Azure Key Vault の統合により、ユーザーは Azure Key Vault 機能を使用して、キーの交換、キー コンテナーのアクセス許可、キーのバックアップ、すべての TDE 保護機能の監査/レポートの有効化などのキー管理タスクを制御できます。 Key Vault ではキーの一元管理が提供され、厳しく監視された HSM を使用してキー管理とデータ管理の職務を分離できるようにすることでセキュリティ ポリシーが順守されるようにサポートします。
Azure SQL Database と Azure Synapse の BYOK の詳細については、[Azure Key Vault の統合による透過的なデータ暗号化](transparent-data-encryption-byok-overview.md)に関する記事を参照してください。

Azure Key Vault の統合で TDE の使用を開始するには、[Key Vault の自分のキーを使用して透過的なデータ暗号化を有効にする](transparent-data-encryption-byok-configure.md)方法のガイドを参照してください。

## <a name="move-a-transparent-data-encryption-protected-database"></a>Transparent Data Encryption で保護されたデータベースを移動する

Azure 内での操作のためにデータベースを暗号化解除する必要はありません。 ソース データベースまたはプライマリ データベースの TDE の設定は、ターゲットに透過的に継承されます。 含まれている操作は次のとおりです。

- geo リストア
- セルフサービスのポイントインタイム リストア
- 削除されたデータベースの復元
- アクティブな地理的レプリケーション
- データベース コピーの作成
- Azure SQL Managed Instance にバックアップ ファイルを復元する

> [!IMPORTANT]
> サービス マネージド TDE によって暗号化されたデータベースのコピーのみの手動バックアップを取得することは、Azure SQL Managed Instance ではサポートされていません。暗号化に使用される証明書にアクセスできないためです。 ポイントインタイム リストア機能を使用して、この種類のデータベースを別の SQL Managed Instance に移動するか、カスタマー マネージド キーに切り替えてください。

TDE で保護されたデータベースをエクスポートする場合、エクスポートされるデータベースのコンテンツは暗号化されません。 このエクスポートされたコンテンツは、暗号化されていない BACPAC ファイルに保存されます。 新しいデータベースのインポートが完了したら、BACPAC ファイルが適切に保護されていることを確認し、TDE を有効にします。

たとえば、BACPAC ファイルを SQL Server インスタンスからエクスポートした場合、新しいデータベースのインポートされるコンテンツは自動的には暗号化されません。 同様に、BACPAC ファイルを SQL Server インスタンスにインポートした場合も、新しいデータベースは自動的には暗号化されません。

唯一の例外は、SQL Database との間でデータベースをエクスポートする場合です。 新しいデータベースでは TDE が有効になりますが、BACPAC ファイル自体はまだ暗号化されていません。

## <a name="manage-transparent-data-encryption"></a>Transparent Data Encryption の管理

# <a name="the-azure-portal"></a>[Azure ポータル](#tab/azure-portal)

Azure portal で TDE を管理します。

Azure portal で TDE を構成するには、Azure の所有者、共同作成者、または SQL セキュリティ マネージャーとして接続する必要があります。

データベース レベルで TDE を有効または無効にします。 Azure SQL Managed Instance の場合、Transact-SQL (T-SQL) を使用してデータベースの TDE のオン/オフを切り替えます。 Azure SQL Database と Azure Synapse では、Azure 管理者または共同作成者アカウントを使用してサインインした後、[Azure portal](https://portal.azure.com) でデータベースの TDE を管理できます。 ユーザー データベース以下の TDE 設定を見つけます。 既定では、サービスによって管理された Transparent Data Encryption が使用されます。 データベースを含むサーバー用の TDE 証明書が自動的に生成されます。

![サービスによって管理された Transparent Data Encryption](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

TDE マスター キー (TDE 保護機能とも呼ばれます) は、サーバーまたはインスタンスのレベルで設定します。 BYOK のサポートによる TDE を使用しているときに、Key Vault のキーを使用してデータベースを保護するには、お使いのサーバーの TDE 設定を開きます。

![Bring Your Own Key をサポートする Transparent Data Encryption](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して TDE を管理します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

PowerShell で TDE を構成するには、Azure の所有者、共同作成者、または SQL セキュリティ マネージャーとして接続する必要があります。

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Azure SQL Database と Azure Synapse のコマンドレット

Azure SQL Database と Azure Synapse には、次のコマンドレットを使用します。

| コマンドレット | 説明 |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |データベースの透過的なデータ暗号化を有効または無効にします。|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |データベースの透過的なデータ暗号化の状態を取得します。 |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |データベースの暗号化の進行状況を確認します。 |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |サーバーに Key Vault キーを追加します。 |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |サーバーの Key Vault キーを取得します。  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |サーバーの透過的なデータ暗号化の保護機能を設定します。 |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Transparent Data Encryption 保護機能を取得します。 |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |サーバーから Key Vault キーを削除します。 |
|  | |

> [!IMPORTANT]
> Azure SQL Managed Instance の場合、T-SQL の [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) コマンドを使用して、データベース レベルで TDE のオン/オフを切り替えます。インスタンス レベルで TDE を管理するには、[PowerShell サンプル スクリプト](transparent-data-encryption-byok-configure.md)を確認してください。

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Transact-SQL を使用して TDE を管理します。

master データベースの **dbmanager** ロールの管理者またはメンバーであるログインを使用してデータベースに接続します。

| command | 説明 |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF によって、データベースを暗号化または暗号化解除します。 |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |データベースの暗号化の状態と、関連付けられているデータベース暗号化キーに関する情報を返します。 |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |各 Azure Synapse ノードの暗号化の状態と、関連付けられているデータベース暗号化キーに関する情報を返します。 |
|  | |

Transact-SQL を使用して、TDE 保護機能を Key Vault のキーに切り替えることはできません。 PowerShell または Azure portal を使用してください。

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)

REST API を使用して TDE を管理します。

REST API で TDE を構成するには、Azure の所有者、共同作成者、または SQL セキュリティ マネージャーとして接続する必要があります。
Azure SQL Database と Azure Synapse には次の一連のコマンドを使用します。

| command | 説明 |
| --- | --- |
|[サーバーの作成または更新](/rest/api/sql/servers/createorupdate)|Azure Active Directory ID をサーバーに追加します。 (Key Vault へのアクセスを許可するために使用します)|
|[サーバー キーの作成または更新](/rest/api/sql/serverkeys/createorupdate)|サーバーに Key Vault キーを追加します。|
|[サーバー キーの削除](/rest/api/sql/serverkeys/delete)|サーバーから Key Vault キーを削除します。 |
|[サーバー キーの取得](/rest/api/sql/serverkeys/get)|サーバーから特定の Key Vault キーを取得します。|
|[サーバーごとにサーバー キーのリストを取得](/rest/api/sql/serverkeys/listbyserver)|サーバーの Key Vault キーを取得します。 |
|[暗号化保護機能の作成または更新](/rest/api/sql/encryptionprotectors/createorupdate)|サーバーの TDE 保護機能を設定します。|
|[暗号化保護機能の取得](/rest/api/sql/encryptionprotectors/get)|サーバーの TDE 保護機能を取得します。|
|[サーバーごとに暗号化保護機能のリストを取得](/rest/api/sql/encryptionprotectors/listbyserver)|サーバーの TDE 保護機能を取得します。 |
|[Transparent Data Encryption 構成の作成または更新](/rest/api/sql/transparentdataencryptions/createorupdate)|データベースの TDE を有効または無効にします。|
|[Transparent Data Encryption 構成の取得](/rest/api/sql/transparentdataencryptions/get)|データベースの TDE 構成を取得します。|
|[Transparent Data Encryption 構成の結果リストの取得](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|データベースの暗号化結果を取得します。|

## <a name="see-also"></a>参照

- Azure 仮想マシン上で実行されている SQL Server も、Key Vault の非対称キーを使用できます。 構成手順は、SQL Database と SQL Managed Instance で非対称キーを使用する場合とは異なります。 詳細については、「[Azure Key Vault を使用する拡張キー管理 (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)」をご覧ください。
- TDE の概要については、「[透過的なデータ暗号化](/sql/relational-databases/security/encryption/transparent-data-encryption)」をご覧ください。
- Azure SQL Database、Azure SQL Managed Instance、および Azure Synapse に対する BYOK のサポートによる TDE の詳細については、[Bring Your Own Key のサポートによる透過的なデータ暗号化](transparent-data-encryption-byok-overview.md)に関する記事をご覧ください。
- Bring Your Own Key のサポートによる TDE の使用を開始するには、[Key Vault の自分のキーを使用して透過的なデータ暗号化をオンにする](transparent-data-encryption-byok-configure.md)方法のガイドを参照してください。
- Key Vault の詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](../../key-vault/general/secure-your-key-vault.md)」を参照してください。