---
title: SQL Server Virtual Machines の自動バックアップ (クラシック) | Microsoft Docs
description: 'Azure Virtual Machines で実行されている SQL Server に対する、Resource Manager を使用した自動バックアップ機能について説明します。 '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aeb97d661d330ed6afb3ca5e5e1eb924dacc4024
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096301"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Azure Virtual Machines での SQL Server の自動バックアップ (クラシック)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [クラシック](../classic/sql-automated-backup.md)
> 
> 

自動バックアップでは、SQL Server 2014 Standard または Enterprise を実行する Azure VM 上の既存のデータベースと新しいデータベースのすべてを対象に、 [Microsoft Azure へのマネージド バックアップ](https://msdn.microsoft.com/library/dn449496.aspx) が自動的に構成されます。 これにより、永続的な Azure BLOB ストレージを利用した日常的なデータベース バックアップを構成できます。 自動バックアップは、 [SQL Server IaaS Agent 拡張機能](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に依存します。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 この記事の Resource Manager バージョンを確認するには、[Resource Manager バージョンの Azure Virtual Machines での SQL Server の自動バックアップ](../sql/virtual-machines-windows-sql-automated-backup.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件
自動バックアップを使用するには、次の前提条件を検討してください。

**オペレーティング システム**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server バージョン/エディション**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> SQL Server 2016 の自動バックアップは、Resource Manager 仮想マシンでサポートされています。 詳細については、「[SQL Server 2016 Azure Virtual Machines の自動バックアップ v2 (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)」を参照してください。

**データベースの構成**:

* ターゲット データベースでは、完全復旧モデルを使用する必要があります。

**Azure PowerShell**:

* [最新の Azure PowerShell コマンドをインストールします](/powershell/azure/overview)。

**SQL Server IaaS 拡張機能**:

* [SQL Server IaaS 拡張機能をインストールします](../classic/sql-server-agent-extension.md)。

## <a name="settings"></a>設定
自動バックアップで構成できるオプションを次の表に示します。 クラシック VM の場合は、PowerShell を使用してこれらの設定を構成する必要があります。

| Setting | 範囲 (既定値) | 説明 |
| --- | --- | --- |
| **自動化されたバックアップ** |有効/無効 (無効) |SQL Server 2014 Standard または Enterprise を実行している Azure VM で、自動バックアップを有効または無効にします。 |
| **保有期間** |1 ～ 30 日 (30 日) |バックアップを保持する日数。 |
| **ストレージ アカウント** |Azure ストレージ アカウント (指定された VM 用に作成されたストレージ アカウント) |自動バックアップのファイルを BLOB ストレージに保存するために使用する Azure ストレージ アカウント。 この場所にコンテナーが作成され、すべてのバックアップ ファイルが保存されます。 バックアップ ファイルの名前付け規則には、日付、時刻、およびコンピューター名が含まれます。 |
| **暗号化** |有効/無効 (無効) |暗号化を有効または無効にします。 暗号化を有効にすると、バックアップの復元に使用する証明書は、指定されたストレージ アカウントの同じ automaticbackup コンテナー内に、同じ名前付け規則を使用して配置されます。 パスワードが変更された場合、そのパスワードを使用して新しい証明書が生成されますが、以前のバックアップの復元には古い証明書が引き続き使用されます。 |
| **パスワード** |パスワード テキスト (なし) |暗号化キーのパスワード。 暗号化を有効にした場合にのみ必須となります。 暗号化されたバックアップを復元するには、バックアップの作成時に使用した正しいパスワードおよび関連する証明書が必要です。 |
| **システム データベースのバックアップ** | 有効/無効 (無効) | Master、Model、および MSDB の完全バックアップを実行します。 |
| **バックアップ スケジュールの構成** | 手動/自動 (自動) | ログの増加に基づいて完全およびログ バックアップを自動的に実行するには、**[自動]** を選びます。 完全およびログ バックアップのスケジュールを指定するには、**[手動]** を選びます。 |

## <a name="configuration-with-powershell"></a>PowerShell での構成
次の PowerShell の例では、既存の SQL Server 2014 VM の自動バックアップを構成しています。 **New-AzureVMSqlServerAutoBackupConfig** コマンドは、$storageaccount 変数で指定された Azure ストレージ アカウントにバックアップを保存するように、自動バックアップ設定を構成します。 これらのバックアップは 10 日間保持されます。 **Set-AzureVMSqlServerExtension** コマンドは、指定された Azure VM をこれらの設定で更新します。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

暗号化を有効にするには、EnableEncryption パラメーターと、CertificatePassword パラメーターのパスワード (セキュリティで保護された文字列) を渡すように、前のスクリプトを変更します。 次のスクリプトでは、前の例の自動バックアップ設定を有効にし、暗号化を追加します。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

自動バックアップを無効にするには、**New-AzureVMSqlServerAutoBackupConfig** の **-Enable** パラメーターを指定せずに、同じスクリプトを実行します。 インストールと同様に、自動バックアップの無効化には数分かかる場合があります。

> [!NOTE]
> SQL Server IaaS エージェントを無効化またはアンインストールしても、以前に構成されたマネージド バックアップ設定は削除されません。 SQL Server IaaS エージェントを無効化またはアンインストールする前に、自動バックアップを無効にしておく必要があります。
> 
> 

## <a name="next-steps"></a>次の手順
自動バックアップでは、Azure VM でマネージド バックアップが構成されます。 そのため、 [マネージド バックアップに関するドキュメント](https://msdn.microsoft.com/library/dn449496.aspx) を見直して、動作と影響を理解することが重要です。

Azure VM の SQL Server のバックアップと復元に関するその他のガイダンスについては、「[Azure の仮想マシンにおける SQL Server のバックアップと復元](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)」のトピックをご覧ください。

その他の利用可能なオートメーション タスクについては、 [SQL Server IaaS Agent 拡張機能](../classic/sql-server-agent-extension.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、 [Azure Virtual Machines における SQL Server の概要](../sql/virtual-machines-windows-sql-server-iaas-overview.md)に関するページをご覧ください。

