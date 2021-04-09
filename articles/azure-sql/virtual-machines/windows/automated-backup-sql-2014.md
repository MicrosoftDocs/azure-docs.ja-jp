---
title: Azure 仮想マシンでの SQL Server 2014 の自動バックアップ
description: Azure で実行されている SQL Server 2014 VM の自動バックアップ機能について説明します。 この記事は、Resource Manager を使用する VM のみにあてはまります。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41add54ce767413982ab0503f7263c58aed4d4e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359287"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>SQL Server 2014 仮想マシンの自動バックアップ (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016/2017](automated-backup.md)

自動バックアップでは、SQL Server 2014 Standard または Enterprise を実行する Azure VM 上の既存のデータベースと新しいデータベースのすべてを対象に、 [Microsoft Azure へのマネージド バックアップ](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) が自動的に構成されます。 これにより、日常的なデータベースのバックアップで永続的な Azure BLOB ストレージを利用するように構成できます。 自動バックアップは、[SQL Server IaaS (サービスとしてのインフラストラクチャ) Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に依存します。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>前提条件
自動バックアップを使用するには、次の前提条件を検討してください。


**[オペレーティング システム]** :

- Windows Server 2012 以降 

**SQL Server バージョン/エディション**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!NOTE]
> SQL 2016 以降については、[SQL Server 2016 の自動バックアップ](automated-backup.md)に関するページを参照してください。

**データベースの構成**:

- ターゲット "_ユーザー_" データベースでは、完全復旧モデルを使用する必要があります。 システム データベースでは、完全復旧モデルを使用する必要はありません。 しかし、モデルまたは MSDB のログのバックアップの作成を必要とする場合は、完全復旧モデルを使用する必要があります。 バックアップに対する完全復旧モデルの影響の詳細については、「[完全復旧モデルでのバックアップ](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105))」を参照してください。 
- SQL Server VM が SQL IaaS Agent 拡張機能に[フル管理モード](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full)で登録されています。 
-  自動バックアップは、フル [SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に依存します。 そのため、自動バックアップは、既定のインスタンスのターゲット データベース、または単一の名前付きインスタンスでのみサポートされます。 既定のインスタンスがなく、複数の名前付きインスタンスがある場合、SQL IaaS 拡張機能は失敗し、自動バックアップは機能しません。 

## <a name="settings"></a>設定

自動バックアップで構成できるオプションを次の表に示します。 実際の構成手順は、Azure ポータルと Azure Windows PowerShell コマンドのどちらを使用するかによって異なります。

| 設定 | 範囲 (既定値) | 説明 |
| --- | --- | --- |
| **自動化されたバックアップ** | 有効/無効 (無効) | SQL Server 2014 Standard または Enterprise を実行している Azure VM で、自動バックアップを有効または無効にします。 |
| **保有期間** | 1 ～ 30 日 (30 日) | バックアップを保持する日数。 |
| **ストレージ アカウント** | Azure ストレージ アカウント | 自動バックアップのファイルを BLOB ストレージに保存するために使用する Azure ストレージ アカウント。 この場所にコンテナーが作成され、すべてのバックアップ ファイルが保存されます。 バックアップ ファイルの名前付け規則には、日付、時刻、およびコンピューター名が含まれます。 |
| **暗号化** | 有効/無効 (無効) | 暗号化を有効または無効にします。 暗号化を有効にすると、バックアップの復元に使用する証明書は、指定されたストレージ アカウントの同じ `automaticbackup` コンテナー内に、同じ名前付け規則を使用して配置されます。 パスワードが変更された場合、そのパスワードを使用して新しい証明書が生成されますが、以前のバックアップの復元には古い証明書が引き続き使用されます。 |
| **パスワード** | パスワード テキスト | 暗号化キーのパスワード。 暗号化を有効にした場合にのみ必須となります。 暗号化されたバックアップを復元するには、バックアップの作成時に使用した正しいパスワードおよび関連する証明書が必要です。 |


## <a name="configure-new-vms"></a>新しい VM を構成する

Resource Manager デプロイ モデルで新しい SQL Server 2014 Virtual Machine を作成するときに自動バックアップを構成するには、Azure portal を使用します。

**[SQL Server 設定]** タブで下にスクロールして **[自動バックアップ]** を見つけ、 **[有効にする]** を選択します。 次の Azure Portal のスクリーンショットは、**SQL Automated Backup** の設定を示しています。

![Azure portal での SQL 自動バックアップ構成](./media/automated-backup-sql-2014/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>既存の VM を構成する

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

既存の SQL Server 仮想マシンの場合、Azure portal から自動バックアップを有効または無効にしたり、保有期間を変更したり、ストレージ アカウントを指定したり、暗号化を有効にしたりすることができます。 

SQL Server 2014 仮想マシン用の [SQL 仮想マシンのリソース](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)に移動し、 **[バックアップ]** を選択します。 

![既存の VM の SQL 自動バックアップ](./media/automated-backup-sql-2014/azure-sql-rm-autobackup-existing-vms.png)

完了したら、 **[バックアップ]** ページの下にある **[適用]** ボタンを選択して変更内容を保存します。

自動バックアップを初めて有効にすると、バックグラウンドで SQL Server IaaS エージェントが構成されます。 この間、自動バックアップが構成されていることは、Azure ポータルに示されない可能性があります。 エージェントがインストールされ、構成されるまで数分待ちます。 その後、Azure ポータルに新しい設定が反映されます。

> [!NOTE]
> テンプレートを使用して自動バックアップを構成することもできます。 詳細については、 [自動バックアップ用の Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)に関するページをご覧ください。

## <a name="configure-with-powershell"></a>PowerShell での構成

PowerShell を使用して自動バックアップを構成できます。 開始する前に、次の操作を行う必要があります。

- [最新の Azure PowerShell をダウンロードしてインストールします](https://aka.ms/webpi-azps)。
- Windows PowerShell を開き、**Connect-AzAccount** コマンドを使用してそれをアカウントに関連付けます。 

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>SQL Server IaaS 拡張機能のインストール
SQL Server 仮想マシンを Azure portal からプロビジョニングした場合は、SQL Server IaaS 拡張機能は既にインストールされています。 これがお使いの仮想マシンにインストール済みかどうかを確認するには、**Get-AzVM** コマンドを呼び出して **Extensions** プロパティを調べます。

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

SQL Server IaaS Agent 拡張機能がインストールされている場合、それは "SqlIaaSAgent" または "SQLIaaSExtension" と表示されます。 また、拡張機能の **ProvisioningState** も "Succeeded" と表示されるはずです。

インストールされていない場合、またはプロビジョニングに失敗した場合は、次のコマンドを使ってインストールできます。 VM 名とリソース グループのほかに、VM が配置されているリージョン ( **$region**) を指定する必要があります。 SQL Server VM のライセンスの種類を指定し、[[Azure ハイブリッド特典]](https://azure.microsoft.com/pricing/hybrid-benefit/) を使用して従量課金制またはライセンス持ち込みを選択します。 ライセンスの詳細については、「[ライセンス モデル](licensing-model-azure-hybrid-benefit-ahb-change.md)」を参照してください。 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> 拡張機能がまだインストールされていない場合、拡張機能をインストールすると、SQL Server が再起動されます。

### <a name="verify-current-settings"></a><a id="verifysettings"></a> 現在の設定の確認

プロビジョニング中に自動バックアップを有効にすると、PowerShell を使って現在の構成を確認することができます。 **Get-AzVMSqlServerExtension** コマンドを実行して **AutoBackupSettings** プロパティを調べます。

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

次のような出力が表示されます。

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

出力で **[有効]** が **False** に設定されていると表示される場合は、自動バックアップを有効にする必要があります。 自動バックアップは同じ方法で有効にし、構成できます。 詳細については、次のセクションを参照してください。

> [!NOTE] 
> 変更直後に設定を確認すると、以前の構成値が表示されることがあります。 変更が適用されていることを確認するには、数分経ってから設定を再び確認します。

### <a name="configure-automated-backup"></a>自動バックアップの構成
PowerShell を使用すると、自動バックアップを有効にできるほか、自動バックアップの構成や動作をいつでも変更することができます。

最初に、バックアップ ファイル用のストレージ アカウントを選択または作成します。 次のスクリプトでは、ストレージ アカウントを選択し、ストレージ アカウントが存在しない場合は作成します。

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> 自動バックアップでは Premium Storage でのバックアップの保存をサポートしていませんが、Premium Storage を使用する VM ディスクからバックアップを取ることができます。

次に **New-AzVMSqlServerAutoBackupConfig** コマンドを使って、Azure ストレージ アカウントにバックアップを保存するように、自動バックアップを有効にして設定を構成します。 この例では、バックアップは 10 日間保持されます｡ 2 番目のコマンド **Set-AzVMSqlServerExtension** は、指定された Azure VM をこれらの設定で更新します。

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

> [!NOTE]
> SQL Server 2016 と自動バックアップ v2 のみに適用される、**New-AzVMSqlServerAutoBackupConfig** のその他の設定があります。 SQL Server 2014 では、**BackupSystemDbs**、**BackupScheduleType**、**FullBackupFrequency**、**FullBackupStartHour**、**FullBackupWindowInHours**、**LogBackupFrequencyInMinutes** はサポートしていません。 これらの設定を SQL Server 2014 仮想マシンで構成しようとした場合、エラーは発生しませんが、設定は適用されません。 これらの設定を SQL Server 2016 仮想マシンで使用したい場合は、[SQL Server 2016 Azure 仮想マシン用 Azure Backup v2](automated-backup.md) に関するページを参照してください。

暗号化を有効にするには、**EnableEncryption** パラメーターと、**CertificatePassword** パラメーターのパスワード (セキュリティで保護された文字列) を渡すように、前のスクリプトを変更します。 次のスクリプトでは、前の例の自動バックアップ設定を有効にし、暗号化を追加します。

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

設定が適用されたことを確認するには、[自動バックアップの構成を確認します](#verifysettings)。

### <a name="disable-automated-backup"></a>自動バックアップを無効にする

自動バックアップを無効にするには、**New-AzVMSqlServerAutoBackupConfig** コマンドの **-Enable** パラメーターを指定せずに、同じスクリプトを実行します。 **-Enable** パラメーターがない場合は、機能を無効にするコマンドが伝えられます。 インストールと同様に、自動バックアップの無効化には数分かかる場合があります。

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>サンプル スクリプト

次のスクリプトでは、お使いの VM で自動バックアップを有効にして構成するようカスタマイズできる変数のセットを提供しています。 場合によっては、要件に基づきスクリプトをカスタマイズする必要があるかもしれません。 たとえば、システム データベースのバックアップを無効にしたり、暗号化を有効にする場合は変更が必要です。

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>監視

SQL Server 2014 での Automated Backup の監視には､大きく 2 つの選択肢があります｡ 自動バックアップでは SQL Server マネージド バックアップ機能を使用するため、この両方に同じ監視手法が適用されます。

最初に [msdb.smart_admin.sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql) を呼び出すことによって状態をポーリングできます｡ あるいは[msdb.smart_admin.fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) テーブル値関数でクエリを実行します｡

> [!NOTE]
> SQL Server 2014 における Managed Backup のスキーマは **msdb.smart_admin** です｡ SQL Server 2016 では､このスキーマは **msdb.managed_backup** になっており､リファレンスのトピックでは､この新しいスキーマが使用されています｡ ただし､SQL Server 2014 の場合は､すべての Managed Backup オブジェクトに対して引き続き **smart_admin** スキーマを利用する必要があります｡

もう 1 つのオプションは、通知に組み込みのデータベース メール機能を利用する方法です。

1. [msdb.smart_admin.sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) ストアド プロシージャを呼び出して､**SSMBackup2WANotificationEmailIds** パラメーターに電子メール アドレスを設定します｡ 
1. [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) が Azure VM から電子メールを送信できるようにします。
1. SMTP サーバーとユーザー名を使用してデータベース メールを構成します。 データベース メールは、SQL Server Management Studio または Transact-SQL コマンドで構成できます。 詳細については、「[データベース メール](/sql/relational-databases/database-mail/database-mail)」を参照してください。
1. [データベース メールを使用するように SQL Server エージェントを構成します](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail)。
1. SMTP ポートがローカルの VM ファイアウォールと、その VM のネットワーク セキュリティ グループの両方で許可されていることを確認します。

## <a name="next-steps"></a>次のステップ

自動バックアップでは、Azure VM でマネージド バックアップが構成されます。 ですから､[SQL Server 2014 でのマネージド バックアップに関するドキュメントを確認](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)することが大切です｡

Azure VM の SQL Server のバックアップと復元に関するその他のガイダンスについては、[Azure 仮想マシンでの SQL Server のバックアップおよび復元](backup-restore.md)

その他の利用可能なオートメーション タスクについては、 [SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、[Azure 仮想マシンにおける SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページをご覧ください。