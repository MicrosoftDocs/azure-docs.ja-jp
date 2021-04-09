---
title: TDE 証明書の移行 - マネージド インスタンス
description: Transparent Data Encryption を使用してデータベースのデータベース暗号化キーを保護している証明書を Azure SQL Managed Instance に移行します
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: c465da3d5d812ea7e811cbe59318122700c6e786
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97824675"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>TDE で保護されたデータベースの証明書を Azure SQL Managed Instance に移行する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

ネイティブな復元オプションを使用して、[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) によって保護されたデータベースを Azure SQL Managed Instance に移行する場合、データベースの復元前に、SQL Server インスタンスから対応する証明書を移行しておく必要があります。 この記事では、証明書を Azure SQL Managed Instance に手動で移行するプロセスについて説明します。

> [!div class="checklist"]
>
> * 証明書を Personal Information Exchange (.pfx) ファイルにエクスポートする
> * ファイルから base-64 文字列に証明書を抽出する
> * PowerShell コマンドレットを使用してそれをアップロードする

TDE で保護されたデータベースと対応する証明書の両方を円滑に移行するためにフル マネージド サービスを使用する別の方法については、[Azure Database Migration Service を使用してオンプレミスのデータベースを Azure SQL Managed Instance に移行する方法](../../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。

> [!IMPORTANT]
> 移行された証明書は TDE で保護されたデータベースの復元にのみ使用されます。 復元が終わり次第、移行済みの証明書は、インスタンスに設定した TDE によりサービス マネージド証明書またはキー コンテナーの非同期キーのいずれかの異なるプロテクタに置き換わります。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の前提条件を満たす必要があります。

* ファイルとしてエクスポートされた証明書にアクセス可能なオンプレミス サーバーまたは他のコンピューターへの [Pvk2Pfx](/windows-hardware/drivers/devtest/pvk2pfx) コマンドライン ツールのインストール。 Pvk2Pfx ツールは、自己完結型コマンドライン環境である [Enterprise Windows Driver Kit](/windows-hardware/drivers/download-the-wdk) に含まれています。
* [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) バージョン 5.0 以上のインストール。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次のものを用意してください。

* Azure PowerShell モジュールの[インストールと更新](/powershell/azure/install-az-ps)。
* [Az.Sql モジュール](https://www.powershellgallery.com/packages/Az.Sql)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Managed Instance によってまだサポートされていますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRM モジュールのコマンドの引数は実質的に同じです。

PowerShell で次のコマンドを実行して、モジュールをインストール/更新します。

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>TDE 証明書を .pfx ファイルにエクスポートします。

証明書はソース SQL Server インスタンスから直接エクスポートできます。または、証明書ストアに保持されている場合にそこからエクスポートできます。

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>ソース SQL Server インスタンスから証明書をエクスポートする

SQL Server Management Studio で証明書をエクスポートして .pfx 形式に変換するには、次の手順を使用します。 手順全体を通して、証明書名およびファイル名とパスには汎用的な名前である *TDE_Cert* と *full_path* が使用されています。 これらは実際の名前に置き換える必要があります。

1. SSMS で、新しいクエリ ウィンドウを開いてソース SQL Server インスタンスに接続します。

1. 次のスクリプトを使用することで、TDE で保護されたデータベースを一覧表示し、移行対象のデータベースの暗号化を保護する証明書の名前を取得します。

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![TDE 証明書の一覧](./media/tde-certificate-migrate/onprem-certificate-list.png)

1. 次のスクリプトを実行して証明書を 1 組のファイル (.cer と .pvk) にエクスポートし、公開キーおよび秘密キーの情報を保持します。

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![TDE 証明書のバックアップ](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. PowerShell コンソールを使用し、Pvk2Pfx ツールを使用して、新しく作成されたファイルのペアから .pfx ファイルに証明書情報をコピーします。

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>証明書ストアから証明書をエクスポートする

証明書が SQL Server のローカル コンピューター証明書ストアに保持される場合、次の手順を使用してそれをエクスポートできます。

1. PowerShell コンソールを開き、次のコマンドを実行して、Microsoft 管理コンソールの証明書スナップインを開きます。

   ```cmd
   certlm
   ```

2. 証明書 MMC スナップインで Personal、Certificates の順にパスを展開し、証明書の一覧を表示します。

3. 証明書を右クリックして **[エクスポート]** をクリックします。

4. ウィザードに従って、証明書と秘密キーを .pfx 形式にエクスポートします。

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>Azure PowerShell コマンドレットを使用して証明書を Azure SQL Managed Instance にアップロードする

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. PowerShell で準備手順を開始します。

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. すべての準備手順が完了したら、次のコマンドを実行して、Base-64 でエンコードされた証明書をターゲットのマネージド インスタンスにアップロードします。

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

最初に、 *.pfx* ファイルを使用して [Azure キー コンテナーを設定する](../../key-vault/general/manage-with-cli2.md)必要があります。

1. PowerShell で準備手順を開始します。

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. すべての準備手順が完了したら、次のコマンドを実行して、Base-64 でエンコードされた証明書をターゲットのマネージド インスタンスにアップロードします。

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

これで、指定したマネージド インスタンスで証明書が使用できるようになりました。TDE で保護された対応するデータベースのバックアップを正常に復元できます。

## <a name="next-steps"></a>次のステップ

この記事では、Transparent Data Encryption が有効なデータベースの暗号化キーを保護する証明書を、オンプレミスまたは IaaS の SQL Server インスタンスから Azure SQL Managed Instance に移行する方法について説明しました。

データベース バックアップを Azure SQL Managed Instance に復元する方法については、[Azure SQL Managed Instance へのデータベース バックアップの復元](restore-sample-database-quickstart.md)に関するページを参照してください。