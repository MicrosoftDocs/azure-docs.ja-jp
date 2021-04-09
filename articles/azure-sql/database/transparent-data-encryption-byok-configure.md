---
title: Azure Key Vault を使用して SQL TDE を有効にする
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: PowerShell または Azure CLI を使用して、保存時の暗号化に Transparent Data Encryption (TDE) を使用するように Azure SQL Database と Azure Synapse Analytics を構成する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 610f0a9692c18afbd7bb446959b09bac14d6f629
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507621"
---
# <a name="powershell-and-the-azure-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell と Azure CLI:Azure Key Vault のユーザー管理キーを使用して Transparent Data Encryption を有効にする
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

この記事では、Azure SQL Database または Azure Synapse Analytics で、Transparent Data Encryption (TDE) に Azure Key Vault のキーを使用する方法について説明します。 Bring Your Own Key (BYOK) のサポートのため、Azure Key Vault と統合される TDE の詳細については、[Azure Key Vault のユーザー管理キーを使用する TDE](transparent-data-encryption-byok-overview.md) に関するページを参照してください。

> [!NOTE] 
> Azure SQL では、マネージド HSM に格納されている RSA キーを TDE プロテクターとして使用できるようになりました。 この機能は **パブリック プレビュー** 段階にあります。 Azure Key Vault Managed HSM は、フル マネージド、高可用性、シングル テナント、標準準拠を特徴とするクラウド サービスで、FIPS 140-2 レベル 3 適合の HSM を使用してクラウド アプリケーションの暗号化キーを保護することができます。 [マネージド HSM](../../key-vault/managed-hsm/index.yml) の詳細を参照してください。

## <a name="prerequisites-for-powershell"></a>PowerShell の前提条件

- Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
- [推奨されますが、必須ではありません] TDE 保護機能のキー マテリアルのローカル コピーを作成するためのハードウェア セキュリティ モジュール (HSM) またはローカル キー ストアを用意します。
- Azure PowerShell がインストールされ、実行されている必要があります。
- TDE に使用する Azure Key Vault とキーを作成します。
  - [ハードウェア セキュリティ モジュール (HSM) と Key Vault の使用手順](../../key-vault/keys/hsm-protected-keys.md)
    - TDE に使用するには、キー コンテナーに次のプロパティが必要です。
  - [論理的な削除](../../key-vault/general/soft-delete-overview.md)と消去保護
- TDE に使用するには、キーに次の属性が必要です。
  - 有効期限がない
  - 無効化されていない
  - "*取得*"、"*キーのラップ*"、"*キーのラップ解除*" の各操作を実行できる
- **(プレビュー段階)** Managed HSM キーを使用するには、[こちら](../../key-vault/managed-hsm/quick-create-cli.md)の指示に従って、Azure CLI を使用して Managed HSM を作成およびアクティブ化します

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az モジュールのインストール手順については、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)を参照してください。 具体的なコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。

Key Vault の詳細については、「[PowerShell を使用した Key Vault の操作](../../key-vault/secrets/quick-create-powershell.md)」と「[PowerShell で Key Vault の論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)」を参照してください。

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) モジュールは引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

## <a name="assign-an-azure-active-directory-azure-ad-identity-to-your-server"></a>サーバーに Azure Active Directory (Azure AD) ID を割り当てる

既存の[サーバー](logical-servers.md)がある場合は、次を使用してサーバーに Azure Active Directory (Azure AD) ID を追加します。

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

サーバーを作成する場合は、サーバーの作成時に、-Identity タグを指定した [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) コマンドレットを使用して、Azure AD ID を追加します。

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault アクセス許可をサーバーに付与する

キー コンテナーのキーを TDE に使用する前に、[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) コマンドレットを使用して、キー コンテナーへのアクセス権をサーバーに付与します。

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```
Managed HSM 上のサーバーにアクセス許可を追加するには、"Managed HSM 暗号化サービスの暗号化" ローカル RBAC ロールをサーバーに追加します。 これにより、Managed HSM 内のキーに対してサーバーによってキーの取得、キーを折り返す、キーの折り返しを解除の操作を実行できます。
[Managed HSM でサーバー アクセスをプロビジョニングするための手順](../../key-vault/managed-hsm/role-management.md)

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Key Vault キーをサーバーに追加し、TDE 保護機能を設定する

- [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) コマンドレットを使用して、Key Vault からキー ID を取得します。
- [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) コマンドレットを使用して、Key Vault のキーをサーバーに追加します。
- [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) コマンドレットを使用して、キーをすべてのサーバー リソースの TDE 保護機能として設定します。
- [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) コマンドレットを使用して、TDE 保護機能が意図したとおりに構成されていることを確認します。

> [!NOTE]
> **(プレビュー段階)** Managed HSM キーには、Az.Sql 2.11.1 バージョンの PowerShell を使用します。

> [!NOTE]
> キー コンテナー名とキー名を組み合わせた長さは 94 文字以下である必要があります。

> [!TIP]
> Key Vault の KeyId の例: <br/>https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
> Managed HSM の KeyId の例:<br/>https://contosoMHSM.managedhsm.azure.net/keys/myrsakey

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>TDE を有効にする

[Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) コマンドレットを使用して、TDE を有効にします。

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

データベースまたはデータ ウェアハウスで、Key Vault の暗号化キーを使用して TDE が有効になりました。

## <a name="check-the-encryption-state-and-encryption-activity"></a>暗号化の状態と暗号化アクティビティを確認する

[Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) を使用して暗号化の状態を取得し、[Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) を使用して、データベースまたはデータ ウェアハウスの暗号化の進行状況を確認します。

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

必要なバージョンの Azure CLI (バージョン 2.0 以降) をインストールして Azure サブスクリプションに接続するには、[Azure クロスプラットフォーム コマンド ライン インターフェイス 2.0 のインストールと構成](/cli/azure/install-azure-cli)に関する記事をご覧ください。

Key Vault の詳細については、[CLI 2.0 を使用した Key Vault の管理](../../key-vault/general/manage-with-cli2.md)と [CLI で Key Vault の論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)に関する記事をご覧ください。

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Azure AD ID をサーバーに割り当てる

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> "principalID" ではサーバーを作成しないようにします。これは、次の手順で Key Vault のアクセス許可を割り当てるために使用するオブジェクト ID です

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault アクセス許可をサーバーに付与する

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> 次の手順のために新しいキーのキー URI または keyID を保持します (例: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h )

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Key Vault キーをサーバーに追加し、TDE 保護機能を設定する

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> キー コンテナー名とキー名を組み合わせた長さは 94 文字以下である必要があります。

## <a name="turn-on-tde"></a>TDE を有効にする

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

データベースまたはデータ ウェアハウスで、Azure Key Vault の顧客マネージド暗号化キーにより TDE が有効になりました。

## <a name="check-the-encryption-state-and-encryption-activity"></a>暗号化の状態と暗号化アクティビティを確認する

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>便利な PowerShell コマンドレット

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- TDE を無効にするには、[Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) コマンドレットを使用します。

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- サーバーに追加された Key Vault キーのリストを取得するには、[Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) コマンドレットを使用します。

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- サーバーから Key Vault キーを削除するには、[Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) を使用します。

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

- データベースの一般的な設定については、「[az sql](/cli/azure/sql)」を参照してください。

- コンテナー キーの設定については、「[az sql server key](/cli/azure/sql/server/key)」を参照してください。

- TDE の設定については、「[az sql server tde-key](/cli/azure/sql/server/tde-key)」と「[az sql db tde](/cli/azure/sql/db/tde)」を参照してください。

* * *

## <a name="troubleshooting"></a>トラブルシューティング

問題が発生した場合は、以下を確認してください。

- キー コンテナーが見つからない場合は、適切なサブスクリプションを使用していることを確認します。

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- 新しいキーをサーバーに追加できない場合や、新しいキーを TDE 保護機能として更新できない場合は、以下を確認します。
   - キーに有効期限がないこと。
   - キーで、"*取得*"、"*キーのラップ*"、"*キーのラップ解除*" の各操作が有効になっている必要があります。

## <a name="next-steps"></a>次のステップ

- セキュリティ要件に準拠するためにサーバーの TDE 保護機能をローテーションする方法を確認する:[PowerShell を使用して Transparent Data Encryption (TDE) 保護機能をローテーションする](transparent-data-encryption-byok-key-rotation.md)
- セキュリティ リスクに備えて、侵害された可能性のある TDE 保護機能を削除する方法を確認する:[侵害された可能性のあるキーを削除する](transparent-data-encryption-byok-remove-tde-protector.md)
