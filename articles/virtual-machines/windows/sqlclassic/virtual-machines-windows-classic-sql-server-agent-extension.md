---
title: SQL VM (クラシック) での管理タスクの自動化 | Microsoft Docs
description: このトピックでは、SQL Server Agent 拡張機能を管理して、SQL Server の特定の管理機能を自動化する方法について説明します。 自動バックアップ、自動修正、および Azure Key Vault の統合が含まれます。 このトピックでは、クラシック デプロイ モードを使用します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e58b3f651a7a8f65d3450cf4b069d4e3f252d928
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100278"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>SQL Server Agent 拡張機能 (クラシック) による Azure Virtual Machines での管理タスクの自動化
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [クラシック](../classic/sql-server-agent-extension.md)
> 
>
 
SQL Server IaaS Agent 拡張機能 (SQLIaaSAgent) は、管理タスクを自動化するために Azure 仮想マシン上で実行されます。 このトピックでは、この拡張機能によってサポートされるサービスの概要と、インストール、状態、および削除のための手順について説明します。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 この記事の Resource Manager バージョンを確認するには、[Resource Manager バージョンの SQL Server VM 用 SQL Server Agent 拡張機能](../sql/virtual-machines-windows-sql-server-agent-extension.md)に関する記事をご覧ください。

## <a name="supported-services"></a>サポートされているサービス
SQL Server IaaS Agent 拡張機能は、次の管理タスクをサポートします。

| 管理機能 | 説明 |
| --- | --- |
| **SQL Automated Backup** |VM 内の SQL Server の既定のインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。 詳細については、「 [Azure Virtual Machines での SQL Server の自動バックアップ (クラシック)](../classic/sql-automated-backup.md)」をご覧ください。 |
| **SQL Automated Patching** |VM で重要な更新プログラムを実行できるメンテナンス期間を構成します。これにより、ワークロードのピーク時の更新を回避できます。 詳細については、「[Azure Virtual Machines での SQL Server の自動修正 (クラシック)](../classic/sql-automated-patching.md)」を参照してください。 |
| **Azure Key Vault の統合** |SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。 詳細については、「 [Azure VM 上の SQL Server 向け Azure Key Vault 統合の構成 (クラシック)](../classic/ps-sql-keyvault.md)」を参照してください。 |

## <a name="prerequisites"></a>前提条件
VM で SQL Server IaaS Agent 拡張機能を使用するための要件:

### <a name="operating-system"></a>オペレーティング システム:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>SQL Server のバージョン:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:
[最新の Azure PowerShell コマンドをダウンロードして構成します](/powershell/azure/overview)。

Windows PowerShell を起動し、 **Add-AzureAccount** コマンドを使用して Azure サブスクリプションに接続します。

    Add-AzureAccount

複数のサブスクリプションがある場合は、 **Select-AzureSubscription** を使用して、対象となるクラシック VM が含まれるサブスクリプションを選択します。

    Select-AzureSubscription -SubscriptionName <subscriptionname>

この時点で、 **Get-AzureVM** コマンドにより、クラシック仮想マシンとそれに関連付けられているサービス名の一覧を取得できます。

    Get-AzureVM

## <a name="installation"></a>インストール
クラシック VM の場合は、PowerShell を使用して、SQL Server IaaS Agent 拡張機能のインストールと、関連付けられているサービスの構成を行う必要があります。 拡張機能をインストールするには、 **Set-AzureVMSqlServerExtension** PowerShell コマンドレットを使用します。 たとえば、次のコマンドは、Windows Server VM (クラシック) に拡張機能をインストールし、"SQLIaaSExtension" という名前を付けます。

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

最新バージョンの SQL IaaS Agent 拡張機能に更新する場合は、拡張機能の更新後、仮想マシンを再起動する必要があります。

> [!NOTE]
> クラシック仮想マシンの場合、ポータルを通じて SQL IaaS Agent 拡張機能のインストールや構成を行うためのオプションはありません。

> [!NOTE]
> SQL Server IaaS Agent 拡張機能は、[SQL Server VM ギャラリー イメージ](../sql/virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (従量課金制またはライセンス持ち込み) でのみサポートされます。 SQL Server を OS のみの Windows Server 仮想マシンに手動でインストールする場合や、カスタマイズされた SQL Server VM VHD をデプロイする場合、この拡張機能はサポートされません。 このような場合、PowerShell を使用して手動で拡張機能をインストールして管理することは可能ですが、代わりに、SQL Server VM ギャラリーイメージをインストールしてカスタマイズすることを強くお勧めします。

## <a name="status"></a>Status
拡張機能がインストールされていることを確認する 1 つの方法は、Azure Portal にエージェントの状態を表示することです。 仮想マシン ブレードの一覧で仮想マシンを選び、 **[拡張機能]** をクリックします。 **SQLIaaSAgent** 拡張機能が表示されます。

![Azure Portal での SQL Server IaaS Agent 拡張機能](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

**Get-AzureVMSqlServerExtension** Azure Powershell コマンドレットを使用することもできます。

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>削除
Azure Portal で拡張機能をアンインストールするには、仮想マシンのプロパティの **[拡張機能]** ブレードにある省略記号をクリックします。 **[アンインストール]** をクリックします。

![Azure Portal で SQL Server IaaS Agent 拡張機能をアンインストールします](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

**Remove-AzureVMSqlServerExtension** Powershell コマンドレットを使用することもできます。

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>次の手順
拡張機能によってサポートされるいずれかのサービスの使用を開始します。 詳細については、この記事の「 [サポートされているサービス](#supported-services) 」で参照されているトピックをご覧ください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「 [Azure Virtual Machines における SQL Server の概要](../sql/virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

