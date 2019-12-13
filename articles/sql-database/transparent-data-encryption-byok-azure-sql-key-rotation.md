---
title: TDE 保護機能のローテーション - PowerShell
description: Azure SQL サーバーの Transparent Data Encryption (TDE) 保護機能をローテーションする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 553d8535d2fdbd7daa5c93535c7c4bd51f2da1a1
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995807"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell を使用して Transparent Data Encryption (TDE) 保護機能をローテーションする

この記事では、Azure Key Vault から TDE 保護機能を使用して Azure SQL サーバーのキーをローテーションする方法について説明します。 Azure SQL サーバーの TDE 保護機能をローテーションすることは、サーバー上のデータベースを保護する新しい非対称キーに切り替えることを意味します。 キー ローテーションはオンラインで行われ、データベース全体ではなくデータべースのデータ暗号化キーを復号化して再暗号化するのみであるため、完了までに数秒しかかかりません。

このガイドでは、サーバーの TDE 保護機能をローテーションする 2 つのオプションについて説明します。

> [!NOTE]
> キー ローテーションを行う前に、停止された SQL Data Warehouse を再開する必要があります。

> [!IMPORTANT]
> ロールオーバー後に以前のバージョンのキーは削除しないでください。 キーがロール オーバーされると、古いデータベース バックアップなど、一部のデータは引き続き以前のキーで暗号化されます。

## <a name="prerequisites"></a>前提条件

- このハウツー ガイドは、Azure SQL Database または Data Warehouse の TDE 保護機能として、Azure Key Vault のキーを既に使用していることを前提としています。 [BYOK をサポートする Transparent Data Encryption](transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。
- Azure PowerShell がインストールされ、実行されている必要があります。
- [推奨ただし省略可能] まず TDE 保護機能のキー素材をハードウェア セキュリティ モジュール (HSM) またはローカル キー ストアで作成し、そのキー素材を Azure Key Vault にインポートします。 詳しくは、[ハードウェア セキュリティ モジュール (HSM) と Key Vault の使用手順](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)をご覧ください。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Az モジュールのインストール手順については、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)を参照してください。 具体的なコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

インストールについては、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

* * *

## <a name="manual-key-rotation"></a>手動でのキーのローテーション

手動でのキーの交換では、次のコマンドを使用して完全に新しいキーを追加します。このキーは、新しいキー名で、場合によっては別の Key Vault に存在する可能性があります。 このアプローチを使用する場合は、別のキー コンテナーに同じキーを追加し、高可用性と geo dr シナリオをサポートすることがサポートされます。

> [!NOTE]
> キー コンテナー名とキー名を組み合わせた長さは 94 文字以下である必要があります。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey)、[Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)、および [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) のコマンドレットを使用します。

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)、[az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create)、および [az sql server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) のコマンドを使用します。

```azure-cli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>便利な PowerShell コマンドレット

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- TDE 保護機能を Microsoft マネージドから BYOK モードに切り替えるには、[Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) コマンドレットを使用します。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- TDE 保護機能を BYOK モードから Microsoft マネージドに切り替えるには、[Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) コマンドレットを使用します。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

次の例では [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) を使用します。

- TDE 保護機能を Microsoft による管理から BYOK モードに切り替えるには、

   ```azure-cli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- TDE 保護機能を BYOK モードから Microsoft による管理に切り替えるには、

   ```azure-cli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>次の手順

- セキュリティ リスクに備えて、侵害された可能性のある TDE 保護機能を削除する方法を確認する:[侵害された可能性のあるキーを削除する](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- TDE のために Azure Key Vault の統合と Bring Your Own Key サポートの使用を開始する:[PowerShell で Key Vault の独自のキーを使用して TDE を有効にする](transparent-data-encryption-byok-azure-sql-configure.md)
