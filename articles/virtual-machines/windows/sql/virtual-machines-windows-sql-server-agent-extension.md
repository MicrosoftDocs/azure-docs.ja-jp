---
title: SQL VM (Resource Manager) での管理タスクの自動化 | Microsoft Docs
description: この記事では、SQL Server エージェント拡張機能を管理して、SQL Server の特定の管理機能を自動化する方法について説明します。 自動バックアップ、自動修正、および Azure Key Vault の統合が含まれます。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 71878d5d033f0005d2c8c36d9f59799e125a19dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762703"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>SQL Server Agent 拡張機能 (Resource Manager) による Azure Virtual Machines での管理タスクの自動化
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [クラシック](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS Agent 拡張機能 (SQLIaaSExtension) は、管理タスクを自動化するために Azure 仮想マシン上で実行されます。 この記事では、この拡張機能によってサポートされるサービスの概要と、インストール、状態、および削除のための手順について説明します。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

この記事のクラシック バージョンを確認するには、「[SQL Server VM 用 SQL Server Agent 拡張機能 (クラシック)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)」をご覧ください。

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

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server のバージョン**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [最新の Azure PowerShell コマンドをダウンロードして構成します](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> 現時点で、Azure 上の SQL Server FCI では [SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)がサポートされていません。 FCI に参加している VM からこの拡張機能をアンインストールすることをお勧めします。 エージェントのアンインストール後、この拡張機能によってサポートされる機能を SQL VM で使用することはできません。

## <a name="installation"></a>インストール
SQL Server IaaS Agent 拡張機能は、SQL Server 仮想マシン ギャラリー イメージのいずれかをプロビジョニングしたときに自動的にインストールされます。 SQL IaaS 拡張機能は、SQL Server VM 上の単一インスタンスの管理を提供します。 既定のインスタンスがある場合、拡張機能は、既定のインスタンスで動作し、その他のインスタンスの管理はサポートしません。 既定のインスタンスはないが、名前付きインスタンスが 1 つだけある場合、名前付きインスタンスを管理します。 既定のインスタンスがなく、複数の名前付きインスタンスがある場合、拡張機能はインストールに失敗します。 



これらの SQL Server VM の 1 つに拡張機能を手動で再インストール必要がある場合は、次の PowerShell コマンドを使用します。

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> 拡張機能がまだインストールされていない場合、拡張機能をインストールすると、SQL Server サービスが再起動されます。 ただし、SQL IaaS 拡張機能を更新しても、SQL Server サービスは再起動されません。 

> [!NOTE]
> SQL Server IaaS Agent 拡張機能をカスタム SQL Server イメージにインストールすることはできますが、機能は現在[ライセンスの種類の変更](virtual-machines-windows-sql-ahb.md)に限られます。 SQL IaaS 拡張機能によって提供されるその他の機能は、[SQL Server VM ギャラリー イメージ](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (従量課金制またはライセンス持ち込み) でのみ機能します。

### <a name="use-a-single-named-instance"></a>単一の名前付きインスタンスを使用する
SQL IaaS 拡張機能は、既定のインスタンスが正常にアンインストールされた場合および IaaS 拡張機能が再インストールされた場合に、SQL Server イメージ上の名前付きインスタンスと連携します。

SQL Server の名前付きインスタンスを使用するには、次の手順を実行します。
   1. マーケットプレースから SQL Server VM をデプロイします。 
   1. [Azure portal](https://portal.azure.com) 内から、IaaS 拡張機能をアンインストールします。
   1. SQL Server VM 内で SQL Server を完全にアンインストールします。
   1. SQL Server VM 内の名前付きインスタンスで SQL Server をインストールします。 
   1. Azure portal 内から、IaaS 拡張機能をインストールします。  

## <a name="status"></a>Status
拡張機能がインストールされていることを確認する 1 つの方法は、Azure ポータルにエージェントの状態を表示することです。 仮想マシンのウィンドウで **[すべての設定]** を選択し、**[拡張機能]** をクリックします。 **SqlIaasExtension** 拡張機能が一覧表示されます。

![Azure ポータルでの SQL Server IaaS Agent 拡張機能](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzVMSqlServerExtension** Azure PowerShell コマンドレットを使用することもできます。

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

前のコマンドは、エージェントがインストールされていることを確認し、全般的なステータス情報を提供します。 次のコマンドを使用して、自動バックアップと自動修正に関する特定のステータス情報を取得することもできます。

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>削除
Azure Portal で拡張機能をアンインストールするには、仮想マシンのプロパティの **[拡張機能]** ウィンドウにある省略記号をクリックします。 その後、 **[削除]** をクリックします。

![Azure ポータルで SQL Server IaaS Agent 拡張機能をアンインストールする](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzVMSqlServerExtension** PowerShell コマンドレットを使用することもできます。

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>次の手順
拡張機能によってサポートされるいずれかのサービスの使用を開始します。 詳細については、この記事の「[サポートされているサービス](#supported-services) 」で参照されているトピックをご覧ください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「 [Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

