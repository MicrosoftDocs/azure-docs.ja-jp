---
title: PowerShell と CLI:SQL TDE を有効にする - Azure Key Vault の使用 - Bring Your Own Key - Azure SQL Database | Microsoft Docs
description: PowerShell または CLI を使用して、保存時の暗号化に Transparent Data Encryption (TDE) を使用するように Azure SQL Database と Data Warehouse を構成する方法について説明します。
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
ms.openlocfilehash: c42c6175512105de38a29be260c370851e152137
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871646"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell と CLI:Azure Key Vault のユーザー管理キーを使用して Transparent Data Encryption を有効にする

この記事では、SQL Database または Data Warehouse で、Transparent Data Encryption (TDE) に Azure Key Vault のキーを使用する方法について説明します。 Bring Your Own Key (BYOK) のサポートのため、Azure Key Vault と統合される TDE の詳細については、[Azure Key Vault のユーザー管理キーを使用する TDE](transparent-data-encryption-byok-azure-sql.md) に関するページを参照してください。 

## <a name="prerequisites-for-powershell"></a>PowerShell の前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

- Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
- [推奨されますが、必須ではありません] TDE 保護機能のキー マテリアルのローカル コピーを作成するためのハードウェア セキュリティ モジュール (HSM) またはローカル キー ストアを用意します。
- Azure PowerShell がインストールされ、実行されている必要があります。 
- TDE に使用する Azure Key Vault とキーを作成します。
  - [PowerShell を使用した Key Vault の操作](../key-vault/key-vault-overview.md)
  - [ハードウェア セキュリティ モジュール (HSM) と Key Vault の使用手順](../key-vault/key-vault-hsm-protected-keys.md)
    - TDE に使用するには、キー コンテナーに次のプロパティが必要です。
  - [論理的な削除](../key-vault/key-vault-ovw-soft-delete.md)
  - [PowerShell で Key Vault の論理的な削除を使用する方法](../key-vault/key-vault-soft-delete-powershell.md) 
- TDE に使用するには、キーに次の属性が必要です。
   - 有効期限がない
   - 無効化されていない
   - "*取得*"、"*キーのラップ*"、"*キーのラップ解除*" の各操作を実行できる

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>手順 1. Azure AD ID をサーバーに割り当てる 

既存のサーバーがある場合は、次のコマンドを使用して Azure AD ID をサーバーに追加します。

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

サーバーを作成する場合は、サーバーの作成時に、-Identity タグを指定した [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) コマンドレットを使用して、Azure AD ID を追加します。

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>手順 2. Key Vault アクセス許可をサーバーに付与する

キー コンテナーのキーを TDE に使用する前に、[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) コマンドレットを使用して、キー コンテナーへのアクセス権をサーバーに付与します。

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>手順 3. Key Vault キーをサーバーに追加し、TDE 保護機能を設定する

- [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) コマンドレットを使用して、Key Vault のキーをサーバーに追加します。
- [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) コマンドレットを使用して、キーをすべてのサーバー リソースの TDE 保護機能として設定します。
- [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) コマンドレットを使用して、TDE 保護機能が意図したとおりに構成されていることを確認します。

> [!Note]
> キー コンテナー名とキー名を組み合わせた長さは 94 文字以下である必要があります。
> 

>[!Tip]
>Key Vault の KeyId の例: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>手順 4. TDE を有効にする 

[Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) コマンドレットを使用して、TDE を有効にします。

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

データベースまたはデータ ウェアハウスで、Key Vault の暗号化キーを使用して TDE が有効になりました。

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>手順 5. 暗号化の状態と暗号化アクティビティを確認する

[Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) を使用して暗号化の状態を取得し、[Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) を使用して、データベースまたはデータ ウェアハウスの暗号化の進行状況を確認します。

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>その他の便利な PowerShell コマンドレット

- TDE を無効にするには、[Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) コマンドレットを使用します。

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- サーバーに追加された Key Vault キーのリストを取得するには、[Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) コマンドレットを使用します。

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- サーバーから Key Vault キーを削除するには、[Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) を使用します。

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>トラブルシューティング

問題が発生した場合は、以下を確認してください。
- キー コンテナーが見つからない場合は、[Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) コマンドレットを使用して、適切なサブスクリプションを使用していることを確認します。

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- 新しいキーをサーバーに追加できない場合や、新しいキーを TDE 保護機能として更新できない場合は、以下を確認します。
   - キーに有効期限がないこと。
   - キーで、"*取得*"、"*キーのラップ*"、"*キーのラップ解除*" の各操作が有効になっている必要があります。

## <a name="next-steps"></a>次の手順

- セキュリティ要件に準拠するためにサーバーの TDE 保護機能をローテーションする方法を確認する:[PowerShell を使用して Transparent Data Encryption (TDE) 保護機能をローテーションする](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- セキュリティ リスクに備えて、侵害された可能性のある TDE 保護機能を削除する方法を確認する:[侵害された可能性のあるキーを削除する](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

## <a name="prerequisites-for-cli"></a>CLI の前提条件

- Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
- [推奨されますが、必須ではありません] TDE 保護機能のキー マテリアルのローカル コピーを作成するためのハードウェア セキュリティ モジュール (HSM) またはローカル キー ストアを用意します。
- コマンドライン インターフェイス バージョン 2.0 以降。 最新バージョンをインストールして Azure サブスクリプションに接続するには、「[Azure クロスプラットフォーム コマンド ライン インターフェイス 2.0 のインストールと構成](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。 
- TDE に使用する Azure Key Vault とキーを作成します。
  - [CLI 2.0 を使用した Key Vault の管理](../key-vault/key-vault-manage-with-cli2.md)
  - [ハードウェア セキュリティ モジュール (HSM) と Key Vault の使用手順](../key-vault/key-vault-hsm-protected-keys.md)
    - TDE に使用するには、キー コンテナーに次のプロパティが必要です。
  - [論理的な削除](../key-vault/key-vault-ovw-soft-delete.md)
  - [CLI で Key Vault の論理的な削除を使用する方法](../key-vault/key-vault-soft-delete-cli.md) 
- TDE に使用するには、キーに次の属性が必要です。
   - 有効期限がない
   - 無効化されていない
   - "*取得*"、"*キーのラップ*"、"*キーのラップ解除*" の各操作を実行できる
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>手順 1. Azure AD の ID でサーバーを作成する
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>"principalID" ではサーバーを作成しないようにします。これは、次の手順で Key Vault のアクセス許可を割り当てるために使用するオブジェクト ID です
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>手順 2. Key Vault のアクセス許可を論理 SQL サーバーに付与する
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>次の手順のために新しいキーのキー URI または keyID を保持します (例: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h)
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>手順 3. Key Vault キーをサーバーに追加し、TDE 保護機能を設定する
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> キー コンテナー名とキー名を組み合わせた長さは 94 文字以下である必要があります。
> 

  
## <a name="step-4-turn-on-tde"></a>手順 4. TDE を有効にする 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

データベースまたはデータ ウェアハウスで、Azure Key Vault の顧客マネージド暗号化キーにより TDE が有効になりました。

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>手順 5. 暗号化の状態と暗号化アクティビティを確認する

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>SQL CLI リファレンス

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

