---
title: TDE 保護機能をローテーションする (PowerShell と Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: PowerShell と Azure CLI を使用して、Azure SQL Database や Azure Synapse Analytics によって使用される Azure 内のサーバーの Transparent Data Encryption (TDE) 保護機能をローテーションする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: b2729975db154fbaf4569afc5aa9b5eaab358146
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321356"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Transparent Data Encryption (TDE) 保護機能をローテーションする
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


この記事では、Azure Key Vault から TDE 保護機能を使用して[サーバー](logical-servers.md)のキーをローテーションする方法について説明します。 サーバーの論理的な TDE 保護機能のローテーションは、サーバー上のデータベースを保護する新しい非対称キーに切り替えることを示します。 キー ローテーションはオンラインで行われ、データベース全体ではなくデータべースのデータ暗号化キーを復号化して再暗号化するのみであるため、完了までに数秒しかかかりません。

このガイドでは、サーバーの TDE 保護機能をローテーションする 2 つのオプションについて説明します。

> [!NOTE]
> キーのローテーションを行う前に、一時停止している Azure Synapse Analytics SQL プールを再開する必要があります。

> [!IMPORTANT]
> ロールオーバー後に以前のバージョンのキーは削除しないでください。 キーがロール オーバーされると、古いデータベース バックアップなど、一部のデータは引き続き以前のキーで暗号化されます。

## <a name="prerequisites"></a>前提条件

- このハウツー ガイドでは、Azure Key Vault のキーを Azure SQL Database または Azure Synapse Analytics の TDE 保護機能として既に使用していることを前提としています。 [BYOK をサポートする Transparent Data Encryption](transparent-data-encryption-byok-overview.md) に関する記事をご覧ください。
- Azure PowerShell がインストールされ、実行されている必要があります。
- [推奨ただし省略可能] まず TDE 保護機能のキー素材をハードウェア セキュリティ モジュール (HSM) またはローカル キー ストアで作成し、そのキー素材を Azure Key Vault にインポートします。 詳しくは、[ハードウェア セキュリティ モジュール (HSM) と Key Vault の使用手順](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)をご覧ください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az モジュールのインストール手順については、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)を参照してください。 具体的なコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) モジュールは引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

インストールについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

* * *

## <a name="manual-key-rotation"></a>手動でのキーのローテーション

手動でのキーの交換では、次のコマンドを使用して完全に新しいキーを追加します。このキーは、新しいキー名で、場合によっては別の Key Vault に存在する可能性があります。 このアプローチを使用する場合は、別のキー コンテナーに同じキーを追加し、高可用性と geo dr シナリオをサポートすることがサポートされます。

> [!NOTE]
> キー コンテナー名とキー名を組み合わせた長さは 94 文字以下である必要があります。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)、[az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create)、および [az sql server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) のコマンドを使用します。

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>TDE 保護機能モードを切り替える

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の例では [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) を使用します。

- TDE 保護機能を Microsoft による管理から BYOK モードに切り替えるには、

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- TDE 保護機能を BYOK モードから Microsoft による管理に切り替えるには、

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>次のステップ

- セキュリティ リスクに備えて、侵害された可能性のある TDE 保護機能を削除する方法を確認する:[侵害された可能性のあるキーを削除する](transparent-data-encryption-byok-remove-tde-protector.md)

- TDE のために Azure Key Vault の統合と Bring Your Own Key サポートの使用を開始する:[PowerShell で Key Vault の独自のキーを使用して TDE を有効にする](transparent-data-encryption-byok-configure.md)。
