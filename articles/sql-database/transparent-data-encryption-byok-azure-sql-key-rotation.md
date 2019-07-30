---
title: PowerShell - TDE 保護機能のローテーション - Azure SQL Database | Microsoft Docs
description: Azure SQL サーバーの Transparent Data Encryption (TDE) 保護機能をローテーションする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 03/12/2019
ms.openlocfilehash: c3e982c0fc46ea72692d5fa7f27e14b88c6383df
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274258"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell を使用して Transparent Data Encryption (TDE) 保護機能をローテーションする

この記事では、Azure Key Vault から TDE 保護機能を使用して Azure SQL サーバーのキーをローテーションする方法について説明します。 Azure SQL サーバーの TDE 保護機能をローテーションすることは、サーバー上のデータベースを保護する新しい非対称キーに切り替えることを意味します。 キー ローテーションはオンラインで行われ、データベース全体ではなくデータべースのデータ暗号化キーを復号化して再暗号化するのみであるため、完了までに数秒しかかかりません。

このガイドでは、サーバーの TDE 保護機能をローテーションする 2 つのオプションについて説明します。

> [!NOTE]
> キー ローテーションを行う前に、停止された SQL Data Warehouse を再開する必要があります。
>

> [!IMPORTANT]
> ロールオーバー後に以前のバージョンのキーは**削除しないでください**。  キーがロール オーバーされると、古いデータベース バックアップなど、一部のデータは引き続き以前のキーで暗号化されます。 
>

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

- このハウツー ガイドは、Azure SQL Database または Data Warehouse の TDE 保護機能として、Azure Key Vault のキーを既に使用していることを前提としています。 [BYOK をサポートする Transparent Data Encryption](transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。
- Azure PowerShell がインストールされ、実行されている必要があります。
- [推奨ただし省略可能] まず TDE 保護機能のキー素材をハードウェア セキュリティ モジュール (HSM) またはローカル キー ストアで作成し、そのキー素材を Azure Key Vault にインポートします。 詳しくは、[ハードウェア セキュリティ モジュール (HSM) と Key Vault の使用手順](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)をご覧ください。

## <a name="manual-key-rotation"></a>手動でのキーのローテーション

手動でのキーのローテーションでは、[Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey) コマンドレット、[Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) コマンドレット、および [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) コマンドレットを使用して、まったく新しいキーを追加します。これは新しいキー名の下または別のキー コンテナーに追加できます。 このアプローチを使用する場合は、別のキー コンテナーに同じキーを追加し、高可用性と geo dr シナリオをサポートすることがサポートされます。

>[!NOTE]
>キー コンテナー名とキー名を組み合わせた長さは 94 文字以下である必要があります。

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>その他の便利な PowerShell コマンドレット

- TDE 保護機能を Microsoft マネージドから BYOK モードに切り替えるには、[Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) コマンドレットを使用します。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- TDE 保護機能を BYOK モードから Microsoft マネージドに切り替えるには、[Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) コマンドレットを使用します。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>次の手順

- セキュリティ リスクに備えて、侵害された可能性のある TDE 保護機能を削除する方法を確認する:[侵害された可能性のあるキーを削除する](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- TDE のために Azure Key Vault の統合と Bring Your Own Key サポートの使用を開始する:[PowerShell で Key Vault の独自のキーを使用して TDE を有効にする](transparent-data-encryption-byok-azure-sql-configure.md)
