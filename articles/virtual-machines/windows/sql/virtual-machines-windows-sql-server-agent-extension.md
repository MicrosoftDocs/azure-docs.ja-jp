---
title: SQL Server IaaS Agent 拡張機能を使用して Azure Virtual Machines で管理タスクを自動化する | Microsoft Docs
description: この記事では、SQL Server エージェント拡張機能を管理して、SQL Server の特定の管理機能を自動化する方法について説明します。 自動バックアップ、自動修正、および Azure Key Vault の統合が含まれます。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798049"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS Agent 拡張機能を使用して Azure Virtual Machines で管理タスクを自動化する
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [クラシック](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent 拡張機能 (SQLIaaSExtension) は、管理タスクを自動化するために Azure 仮想マシン上で実行されます。 この記事では、この拡張機能によってサポートされるサービスの概要と、インストール、状態、および削除に関する手順について説明します。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

この記事のクラシック バージョンを確認するには、「[SQL Server VM 用 SQL Server Agent 拡張機能 (クラシック)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)」をご覧ください。

SQL IaaS 拡張機能には 3 つの SQL 管理モードとして、**完全**、**軽量**、**NoAgent** があります。 

- **完全**モードでは、すべての機能が提供されますが、SQL Server の再起動と SA アクセス許可が必要です。 これは、既定でインストールされるオプションであり、単一のインスタンスを備えた SQL Server VM の管理に使用する必要があります。 

- **軽量**では SQL Server の再起動は必要ありませんが、サポートされるのはライセンスの種類と SQL Server のエディションの変更のみです。 このオプションは、複数のインスタンスを備えているかフェールオーバー クラスター インスタンス (FCI) に参加している SQL Server VM に使用する必要があります。 

- **NoAgent** は Windows Server 2008 にインストールされた SQL Server 2008 および SQL Server 2008 R2 の専用モードです。 Windows Server 2008 イメージへの `NoAgent` モードの利用については、[Windows Server 2008 の登録](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms)に関する記事を参照してください。 

## <a name="supported-services"></a>サポートされているサービス
SQL Server IaaS Agent 拡張機能は、次の管理タスクをサポートします。

| 管理機能 | 説明 |
| --- | --- |
| **SQL Automated Backup** |VM 上の SQL Server の既定のインスタンスまたは[適切にインストールされた](virtual-machines-windows-sql-server-iaas-faq.md#administration)名前付きインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。 詳細については、「[Azure Virtual Machines での SQL Server の自動バックアップ (Resource Manager)](virtual-machines-windows-sql-automated-backup.md)」をご覧ください。 |
| **SQL Automated Patching** |VM で重要な更新プログラムを実行できるメンテナンス期間を構成します。これにより、ワークロードのピーク時の更新を回避できます。 詳細については、「[Azure Virtual Machines での SQL Server の自動修正 (Resource Manager)](virtual-machines-windows-sql-automated-patching.md)」をご覧ください。 |
| **Azure Key Vault の統合** |SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。 詳細については、「[Azure VM で SQL Server 用に Azure Key Vault 統合を構成する (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md)」をご覧ください。 |

SQL Server IaaS Agent 拡張機能をインストールして実行すると、Azure ポータル内の仮想マシンの SQL Server パネルや、SQL Server marketplace イメージ用の Azure PowerShell、拡張機能のインストール用の Azure PowerShell などを介して、これらの管理機能を使用できるようになります。 

## <a name="prerequisites"></a>前提条件
VM で SQL Server IaaS Agent 拡張機能を使用するための要件:

**オペレーティング システム**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server のバージョン**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [最新の Azure PowerShell コマンドをダウンロードして構成します](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>管理モードの変更

PowerShell を使用することで、次のように SQL IaaS エージェントの現在のモードを確認できます。 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

*NoAgent* または "*軽量*" の IaaS 拡張機能がインストールされている SQL Server VM の場合、Azure portal を使用してそのモードを "*完全*" にアップグレードできます。 ダウングレードはできません。これを行うには、SQL IaaS 拡張機能を完全にアンインストールし、再度インストールする必要があります。 

エージェント モードを "*完全*" にアップグレードするには、以下を実行します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. [SQL 仮想マシン](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) リソースに移動します。 
1. 対象の SQL Server 仮想マシンを選択し、 **[概要]** を選択します。 
1. *NoAgent* または "*軽量*" IaaS モードの SQL VM の場合は、**対象の SQL IaaS 拡張機能で利用できるのはライセンスの種類とエディションの更新のみ**という内容のメッセージを選択します。

    ![ポータルからモード変更を開始する](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. チェック ボックスをオンにして **SQL Server サービスの再起動**に同意し、 **[確認]** を選択して IaaS モードを "完全" にアップグレードします。 

    ![IaaS 拡張機能の完全な管理を有効にする](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>インストール
SQL IaaS 拡張機能は、SQL Server VM を [SQL VM リソースプロバイダー](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)に登録するとインストールされます。 ただし、必要な場合は、"*完全*" または "*軽量*" モードのインストールを使用して、手動で SQL IaaS エージェントをインストールすることもできます。 

"*完全*" SQL Server IaaS Agent 拡張機能は、Azure portal を使用して SQL Server 仮想マシン ギャラリーのイメージのいずれかをプロビジョニングすると自動的にインストールされます。 

### <a name="full-mode-installation"></a>完全モードのインストール
"*完全*" SQL IaaS 拡張機能では、SQL Server VM 上の単一インスタンスを完全に管理できます。 既定のインスタンスがある場合、拡張機能は、既定のインスタンスで動作し、その他のインスタンスの管理はサポートしません。 既定のインスタンスはないが、名前付きインスタンスが 1 つだけある場合、名前付きインスタンスを管理します。 既定のインスタンスがなく、複数の名前付きインスタンスがある場合、拡張機能はインストールに失敗します。 

"*フル*" モードの SQL IaaS をインストールすると、SQL Server サービスが再起動されます。 SQL Server サービスが再起動されないようにするには、代わりに管理性の制限がある "*軽量*" モードをインストールします。 

PowerShell を使用して、次のように SQL IaaS エージェントを "*完全*" モードでインストールします。

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| パラメーター | 使用可能な値                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'` または `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - 拡張機能がまだインストールされていない場合、**完全**拡張機能をインストールすると、SQL Server サービスが再起動されます。 SQL Server サービスの再起動を防ぐには、**軽量**モードを使用します。 
> - SQL IaaS 拡張機能を更新しても、SQL Server サービスは再起動されません。 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>単一の名前付き SQL Server インスタンスを備えた VM にインストールする
SQL IaaS 拡張機能は、既定のインスタンスがアンインストールされて IaaS 拡張機能が再インストールされた場合に、SQL Server 上の名前付きインスタンスと連携します。

SQL Server の名前付きインスタンスを使用するには、次の手順を実行します。
   1. マーケットプレースから SQL Server VM をデプロイします。 
   1. [Azure portal](https://portal.azure.com) 内から、IaaS 拡張機能をアンインストールします。
   1. SQL Server VM 内で SQL Server を完全にアンインストールします。
   1. SQL Server VM 内の名前付きインスタンスで SQL Server をインストールします。 
   1. Azure portal 内から、IaaS 拡張機能をインストールします。  


### <a name="install-in-lightweight-mode"></a>軽量モードでインストールする
軽量モードでは、SQL Server サービスが再起動されませんが、提供される機能は限定されます。 

PowerShell を使用して、次のように SQL IaaS エージェントを "*軽量*" モードでインストールします。


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| パラメーター | 使用可能な値                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'` または `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>SQL IaaS 拡張機能の状態を取得する
拡張機能がインストールされていることを確認する 1 つの方法は、Azure ポータルにエージェントの状態を表示することです。 仮想マシンのウィンドウで **[すべての設定]** を選択し、 **[拡張機能]** をクリックします。 **SqlIaasExtension** 拡張機能が一覧表示されます。

![Azure ポータルでの SQL Server IaaS Agent 拡張機能](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzVMSqlServerExtension** Azure PowerShell コマンドレットを使用することもできます。

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

前のコマンドは、エージェントがインストールされていることを確認し、全般的なステータス情報を提供します。 次のコマンドを使用して、自動バックアップと自動修正に関する特定のステータス情報を取得することもできます。

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>削除
Azure Portal で拡張機能をアンインストールするには、仮想マシンのプロパティの **[拡張機能]** ウィンドウにある省略記号をクリックします。 その後、 **[削除]** をクリックします。

![Azure ポータルで SQL Server IaaS Agent 拡張機能をアンインストールする](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzVMSqlServerExtension** PowerShell コマンドレットを使用することもできます。

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>次の手順
拡張機能によってサポートされるいずれかのサービスの使用を開始します。 詳細については、この記事の「[サポートされているサービス](#supported-services)」セクションで参照されているトピックをご覧ください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「 [Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

