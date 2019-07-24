---
title: PowerShell - TDE 保護機能の削除 - Azure SQL Database| Microsoft Docs
description: Bring Your Own Key (BYOK) をサポートする TDE を使用している Azure SQL Database または Data Warehouse の、侵害された可能性のある TDE 保護機能に対応するためのハウツー ガイド。
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
ms.date: 03/12/2019
ms.openlocfilehash: 51cdd43e62bd511da55978bbac3215200c3a8e01
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528273"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell を使用した Transparent Data Encryption (TDE) 保護機能の削除

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

- Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
- Azure PowerShell がインストールされ、実行されている必要があります。 
- このハウツー ガイドは、Azure SQL Database または Data Warehouse の TDE 保護機能として、Azure Key Vault のキーを既に使用していることを前提としています。 詳細については、[BYOK をサポートする Transparent Data Encryption](transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。

## <a name="overview"></a>概要

このハウツー ガイドでは、Azure Key Vault のユーザー管理キーで Bring Your Own Key (BYOK) をサポートする TDE を使用している Azure SQL Database または Data Warehouse の、侵害された可能性がある TDE 保護機能に対応する方法について説明します。 TDE の BYOK サポートの詳細については、[概要ページ](transparent-data-encryption-byok-azure-sql.md)をご覧ください。 

以下の手順は、極端な状況またはテスト環境でのみ実行する必要があります。 アクティブに使用されている TDE 保護機能を Azure Key Vault から削除すると、**データが失われる**可能性があるため、このハウツー ガイドを入念に確認してください。 

サービスまたはユーザーがキーに不正アクセスしているなど、キーが侵害された疑いがある場合は、キーを削除するのが最善です。

Key Vault の TDE 保護機能を削除すると、**サーバーにある暗号化されたデータベースへのすべての接続がブロックされ、これらのデータベースはオフラインになり、24 時間以内に削除される**ことに注意してください。 侵害されたキーで暗号化された古いバックアップにはアクセスできなくなります。

次の手順では、特定のデータベースの仮想ログ ファイル (VLF) でまだ使用されている TDE 保護機能の拇印を確認する方法の概要を示します。 データベースの現在の TDE 保護機能の拇印、およびデータベース ID は、SELECT [database_id],        [encryption_state], [encryptor_type], /*非対称キーは AKV を意味し、証明書はサービス管理キーを意味します*/ [encryptor_thumbprint], FROM [sys].[dm_database_encryption_keys] を実行することで確認できます 
 
次のクエリでは、VLF と、使用中の暗号化機能のそれぞれの拇印が返されます。 異なる拇印でそれぞれ、Azure Key Vault (AKV) の異なるキーが参照されます。SELECT * FROM sys.dm_db_log_info (database_id) 

PowerShell コマンドの Get-AzureRmSqlServerKeyVaultKey では、クエリで使用される TDE 保護機能の拇印が提供されるため、AKV で保持するキーと削除するキーを確認できます。 データベースで使用されなくなったキーのみを、Azure Key Vault から安全に削除することができます。

このハウツー ガイドでは、インシデント対応後の望ましい結果に応じた次の 2 つの方法を説明します。

- Azure SQL Databases/Data Warehouses を引き続き**アクセス可能**にしておく
- Azure SQL Databases/Data Warehouses を**アクセス不可**にする

## <a name="to-keep-the-encrypted-resources-accessible"></a>暗号化されたリソースを引き続きアクセス可能にしておくには

1. [Key Vault に新しいキー](/powershell/module/az.keyvault/add-azkeyvaultkey)を作成します。 アクセス制御はコンテナー レベルでプロビジョニングされるため、この新しいキーは、侵害された可能性のある TDE 保護機能とは別のキー コンテナーに作成する必要があります。
2. [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) および [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) コマンドレットを使用してサーバーに新しいキーを追加し、サーバーの新しい TDE 保護機能として更新します。

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) コマンドレットを使用して、サーバーとすべてのレプリカが新しい TDE 保護機能に更新されたことを確認します。 

   >[!NOTE]
   > サーバーにあるすべてのデータベースとセカンダリ データベースに新しい TDE 保護機能が伝達されるまでに数分かかることがあります。

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault 内の[新しいキーのバックアップ](/powershell/module/az.keyvault/backup-azkeyvaultkey)を作成します。

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) コマンドレットを使用して、侵害されたキーを Key Vault から削除します。 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. 今後、Key Vault にキーを復元するときは、[Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) コマンドレットを使用します。
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>暗号化されたリソースをアクセス不可にするには

1. 侵害された可能性のあるキーによって暗号化されているデータベースを削除します。

   データベースとログ ファイルは自動的にバックアップされるので、(キーを提供すれば) データベースのポイントインタイム リストアをいつでも実行できます。 最新のトランザクションの最大 10 分間のデータが失われる可能性を防ぐために、アクティブな TDE 保護機能を削除する前にデータベースを削除する必要があります。 
2. Key Vault 内の TDE 保護機能のキー マテリアルをバックアップします。
3. 侵害された可能性のあるキーを Key Vault から削除します。

## <a name="next-steps"></a>次の手順

- セキュリティ要件に準拠するためにサーバーの TDE 保護機能をローテーションする方法を確認する:[PowerShell を使用して Transparent Data Encryption (TDE) 保護機能をローテーションする](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- TDE の Bring Your Own Key サポートを使用する:[PowerShell で Key Vault の独自のキーを使用して TDE を有効にする](transparent-data-encryption-byok-azure-sql-configure.md)
