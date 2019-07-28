---
title: TDE 証明書の移行 - Azure SQL Database Managed Instance | Microsoft Docs
description: Transparent Data Encryption が有効なデータベースのデータベース暗号化キーを保護する証明書を Azure SQL Database Managed Instance に移行します
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mladjoa
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
manager: craigg
ms.date: 04/25/2019
ms.openlocfilehash: f54950ab96664b17aab056b468db0644216e8654
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706106"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>TDE で保護されたデータベースの証明書を Azure SQL Database Managed Instance に移行する

ネイティブな復元オプションを使用して、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) によって保護されたデータベースを Azure SQL Database Managed Instance に移行する場合、データベースの復元前に、オンプレミスまたは IaaS の SQL Server から対応する証明書を移行しておく必要があります。 この記事では、証明書を Azure SQL Database Managed Instance に手動で移行するプロセスについて説明します。

> [!div class="checklist"]
> * 証明書を Personal Information Exchange (.pfx) ファイルにエクスポートする
> * ファイルから Base-64 文字列に証明書を抽出する
> * PowerShell コマンドレットを使用してそれをアップロードする

TDE で保護されたデータベースと対応する証明書の両方を円滑に移行するためにフル マネージド サービスを使用する別の方法については、[Azure Database Migration Service を使用してオンプレミスのデータベースを Managed Instance に移行する方法](../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。

> [!IMPORTANT]
> 移行された証明書は TDE で保護されたデータベースの復元にのみ使用されます。 復元が終わり次第、移行済みの証明書は、インスタンスに設定した透過データ暗号化によりサービス マネージド証明書またはキー コンテナーの非同期キーのいずれかの異なるプロテクタに置き換わります。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

この記事の手順を完了するには、次の前提条件を満たす必要があります。

- ファイルとしてエクスポートされた証明書にアクセス可能なオンプレミス サーバーまたは他のコンピューターへの [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) コマンドライン ツールのインストール。 Pvk2Pfx ツールは、スタンドアロンの自己完結型コマンドライン環境、[Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk) に含まれています。
- [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) バージョン 5.0 以上のインストール。
- Azure PowerShell モジュールの[インストールと更新](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- [Az.Sql モジュール](https://www.powershellgallery.com/packages/Az.Sql)。
  PowerShell で次のコマンドを実行して、PowerShell モジュールをインストール/更新します。

   ```powershell
   Install-Module -Name Az.Sql
   Update-Module -Name Az.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>TDE 証明書を Personal Information Exchange (.pfx) ファイルにエクスポートする

証明書はソース SQL Server から直接エクスポートできます。または、証明書ストアに保持されている場合にそこからエクスポートできます。

### <a name="export-certificate-from-the-source-sql-server"></a>ソース SQL Server から証明書をエクスポートする

SQL Server Management Studio で証明書をエクスポートして .pfx 形式に変換するには、次の手順を使用します。 手順全体を通して、証明書名およびファイル名とパスには汎用的な名前である *TDE_Cert* と *full_path* が使用されています。 これらは実際の名前に置き換える必要があります。

1. SSMS で、新しいクエリ ウィンドウを開いてソース SQL Server に接続します。
2. 次のスクリプトを使用することで、TDE で保護されたデータベースを一覧表示し、移行対象のデータベースの暗号化を保護する証明書の名前を取得します。

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

   ![TDE 証明書の一覧](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

3. 次のスクリプトを実行して証明書を 1 組のファイル (.cer と .pvk) にエクスポートし、公開キーおよび秘密キーの情報を保持します。

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

   ![TDE 証明書のバックアップ](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

4. PowerShell コンソールを使って Pvk2Pfx ツールを使用し、新しく作成された 1 組のファイルから Personal Information Exchange (.pfx) ファイルに証明書情報をコピーします。

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>証明書ストアから証明書をエクスポートする

証明書が SQL Server のローカル コンピューター証明書ストアに保持される場合、次の手順を使用してそれをエクスポートできます。

1. PowerShell コンソールを開き、次のコマンドを実行して、Microsoft 管理コンソールの証明書スナップインを開きます。

   ```powershell
   certlm
   ```

2. 証明書 MMC スナップインで Personal、Certificates の順にパスを展開し、証明書の一覧を表示します。

3. 証明書を右クリックして [エクスポート] をクリックします。

4. ウィザードに従って、証明書と秘密キーを Personal Information Exchange 形式にエクスポートします。

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Azure PowerShell コマンドレットを使用して証明書を Azure SQL Database Managed Instance にアップロードする

1. PowerShell で準備手順を開始します。

   ```powershell
   # Import the module into the PowerShell session
   Import-Module Az
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # Set subscription for the session (replace Guid_Subscription_Id with actual subscription id)
   Select-AzSubscription Guid_Subscription_Id
   ```

2. すべての準備手順が完了したら、次のコマンドを実行して、Base-64 でエンコードされた証明書をターゲットのマネージド インスタンスにアップロードします。

   ```powershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

これで、指定したマネージド インスタンスで証明書が使用できるようになりました。TDE で保護された対応するデータベースのバックアップを正常に復元できます。

## <a name="next-steps"></a>次の手順

この記事では、Transparent Data Encryption が有効なデータベースの暗号化キーを保護する証明書を、オンプレミスまたは IaaS の SQL Server から Azure SQL Database Managed Instance に移行する方法について説明しました。

データベース バックアップを Azure SQL Database マネージド インスタンスに復元する方法については、「[Azure SQL Database マネージド インスタンスにデータベース バックアップを復元する](sql-database-managed-instance-get-started-restore.md)」を参照してください。
