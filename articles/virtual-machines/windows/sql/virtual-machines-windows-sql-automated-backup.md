---
title: "SQL Server 2014 Azure Virtual Machines の自動バックアップ | Microsoft Docs"
description: "Azure で実行されている SQL Server 2014 VM の自動バックアップ機能について説明します。 この記事は、Resource Manager を使用する VM のみにあてはまります。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/30/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1b9401d7c2f756081d585b9432ceef412ab1f807
ms.lasthandoff: 03/25/2017


---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>SQL Server 2014 Virtual Machines の自動バックアップ (Resource Manager)
> [!div class="op_single_selector"]
> * [リソース マネージャー](virtual-machines-windows-sql-automated-backup.md)
> * [クラシック](../classic/sql-automated-backup.md)
> 
> 

自動バックアップでは、SQL Server 2014 Standard または Enterprise を実行する Azure VM 上の既存のデータベースと新しいデータベースのすべてを対象に、 [Microsoft Azure へのマネージ バックアップ](https://msdn.microsoft.com/library/dn449496.aspx) が自動的に構成されます。 これにより、永続的な Azure BLOB ストレージを利用した日常的なデータベース バックアップを構成できます。 自動バックアップは、 [SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に依存します。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

この記事のクラシック バージョンを確認するには、「[Azure Virtual Machines での SQL Server の自動バックアップ (クラシック)](../classic/sql-automated-backup.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件
自動バックアップを使用するには、次の前提条件を検討してください。

**オペレーティング システム**:

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server バージョン/エディション**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> 自動バックアップは、SQL Server 2014 で動作します。 SQL Server 2016 を使用している場合は、自動バックアップ v2 を使用してデータベースをバックアップできます。 詳細については、「[Automated Backup v2 for SQL Server 2016 Azure Virtual Machines (SQL Server 2016 Azure Virtual Machines の自動バックアップ v2)](virtual-machines-windows-sql-automated-backup-v2.md)」を参照してください。

**データベースの構成**:

- ターゲット データベースでは、完全復旧モデルを使用する必要があります。

バックアップに対する完全復旧モデルの影響の詳細については、「[完全復旧モデルでのバックアップ](https://technet.microsoft.com/library/ms190217.aspx)」を参照してください。

**Azure デプロイメント モデル**:

- リソース マネージャー

**Azure PowerShell**:

- [最新の Azure PowerShell コマンドをインストールします](/powershell/azureps-cmdlets-docs) (PowerShell で自動バックアップを構成する場合)。

> [!NOTE]
> 自動バックアップは、SQL Server IaaS Agent 拡張機能に依存します。 現在の SQL 仮想マシン ギャラリー イメージでは、既定でこの拡張機能が追加されます。 詳細については、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に関するページをご覧ください。

## <a name="settings"></a>Settings
自動バックアップで構成できるオプションを次の表に示します。 実際の構成手順は、Azure ポータルと Azure Windows PowerShell コマンドのどちらを使用するかによって異なります。

| 設定 | 範囲 (既定値) | 説明 |
| --- | --- | --- |
| **自動化されたバックアップ** | 有効/無効 (無効) | SQL Server 2014 Standard または Enterprise を実行している Azure VM で、自動バックアップを有効または無効にします。 |
| **保有期間** | 1 ～ 30 日 (30 日) | バックアップを保持する日数。 |
| **ストレージ アカウント** | Azure ストレージ アカウント | 自動バックアップのファイルを BLOB ストレージに保存するために使用する Azure ストレージ アカウント。 この場所にコンテナーが作成され、すべてのバックアップ ファイルが保存されます。 バックアップ ファイルの名前付け規則には、日付、時刻、およびコンピューター名が含まれます。 |
| **暗号化** | 有効/無効 (無効) | 暗号化を有効または無効にします。 暗号化を有効にすると、バックアップの復元に使用する証明書は、指定されたストレージ アカウントの同じ `automaticbackup` コンテナー内に、同じ名前付け規則を使用して配置されます。 パスワードが変更された場合、そのパスワードを使用して新しい証明書が生成されますが、以前のバックアップの復元には古い証明書が引き続き使用されます。 |
| **パスワード** | パスワード テキスト | 暗号化キーのパスワード。 暗号化を有効にした場合にのみ必須となります。 暗号化されたバックアップを復元するには、バックアップの作成時に使用した正しいパスワードおよび関連する証明書が必要です。 |

## <a name="configuration-in-the-portal"></a>ポータルでの構成
Azure Portal を使用して、プロビジョニング中または既存の SQL Server 2014 VM 用に、自動バックアップを構成することができます。

### <a name="new-vms"></a>新しい VM
Resource Manager デプロイメント モデルで新しい SQL Server 2014 Virtual Machine を作成するときに、Azure Portal を使用して自動バックアップを構成します。

**[SQL Server の設定]** ブレードで、**[自動バックアップ]** を選択します。 次の Azure ポータルのスクリーンショットは、 **[SQL 自動バックアップ]** ブレードを示しています。

![Azure ポータルの SQL 自動バックアップ構成](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

コンテキストについては、[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)に関するトピックをご覧ください。

### <a name="existing-vms"></a>既存の VM
既存の SQL Server 仮想マシンの場合は、ご使用の SQL Server 仮想マシンを選択します。 **[設定]** ブレードの **[SQL Server の構成]** セクションを選択します。

![既存の VM の SQL 自動バックアップ](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

**[SQL Server の構成]** ブレードの [自動バックアップ] セクションで **[編集]** ボタンをクリックします。

![既存の VM の SQL 自動バックアップを構成する](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

終了したら、**[SQL Server の構成]** ブレードの下部にある **[OK]** ボタンをクリックして変更を保存します。

自動バックアップを初めて有効にすると、バックグラウンドで SQL Server IaaS エージェントが構成されます。 この間、自動バックアップが構成されていることは、Azure ポータルに示されない可能性があります。 エージェントがインストールされ、構成されるまで数分待ちます。 その後、Azure ポータルに新しい設定が反映されます。

> [!NOTE]
> テンプレートを使用して自動バックアップを構成することもできます。 詳細については、 [自動バックアップ用の Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)に関するページをご覧ください。
> 
> 

## <a name="configuration-with-powershell"></a>PowerShell での構成
SQL VM をプロビジョニングしたら、PowerShell を使用して自動バックアップを構成します。 開始する前に、次の操作を行う必要があります。

- [最新の Azure PowerShell をダウンロードしてインストールします](http://aka.ms/webpi-azps)。
- Windows PowerShell を開いてアカウントに関連付けます。 これを行うには、プロビジョニングのトピックの「[サブスクリプションの構成](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription)」セクションに記載された手順に従います。

次の PowerShell の例では、既存の SQL Server 2014 VM の自動バックアップを構成しています。 **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** コマンドは、仮想マシンに関連付けられた Azure ストレージ アカウントにバックアップを保存するように、自動バックアップ設定を構成します。 これらのバックアップは 10 日間保持されます。 **Set-AzureRmVMSqlServerExtension** コマンドは、指定された Azure VM をこれらの設定で更新します。

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

暗号化を有効にするには、**EnableEncryption** パラメーターと、**CertificatePassword** パラメーターのパスワード (セキュリティで保護された文字列) を渡すように、前のスクリプトを変更します。 次のスクリプトでは、前の例の自動バックアップ設定を有効にし、暗号化を追加します。

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

自動バックアップを無効にするには、**AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfigg** コマンドの **-Enable** パラメーターを指定せずに、同じスクリプトを実行します。 **-Enable** パラメーターがない場合は、機能を無効にするコマンドが伝えられます。 インストールと同様に、自動バックアップの無効化には数分かかる場合があります。

> [!NOTE]
> SQL Server IaaS Agent を削除しても、前に構成された自動バックアップ設定は削除されません。 SQL Server IaaS エージェントを無効化またはアンインストールする前に、自動バックアップを無効にしておく必要があります。
> 
> 

## <a name="next-steps"></a>次のステップ
自動バックアップでは、Azure VM でマネージ バックアップが構成されます。 そのため、 [マネージ バックアップに関するドキュメント](https://msdn.microsoft.com/library/dn449496.aspx) を見直して、動作と影響を理解することが重要です。

Azure VM の SQL Server のバックアップと復元に関するその他のガイダンスについては、「 [Azure の仮想マシンにおける SQL Server のバックアップと復元](virtual-machines-windows-sql-backup-recovery.md)」をご覧ください。

その他の利用可能なオートメーション タスクについては、 [SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、 [Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)に関するページをご覧ください。


