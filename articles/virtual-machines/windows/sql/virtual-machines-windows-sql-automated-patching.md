---
title: SQL Server VM の自動修正 (Resource Manager) | Microsoft Docs
description: Azure で実行されている SQL Server Virtual Machines に対する、Resource Manager を使用した自動修正機能について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 4f0d681c93ab7ac7fef941892a95282a2fd59b89
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075705"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Azure Virtual Machines での SQL Server の自動修正 (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [クラシック](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

自動修正では、SQL Server を実行している Azure 仮想マシンのメンテナンス期間が設定されます。 このメンテナンス期間にのみ、自動更新プログラムをインストールできます。 これにより、SQL Server では、システムの更新とこれに関連する再起動が、データベースに最適な時間帯に実行されるようになります。 

> [!IMPORTANT]
> **重要**と書かれた Windows 更新プログラムのみがインストールされます。 累積更新プログラムなどの他の SQL Server 更新プログラムは、手動でインストールする必要があります。 

自動修正は、 [SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に依存します。

## <a name="prerequisites"></a>前提条件
自動修正を使用するには、次の前提条件を検討してください。

**オペレーティング システム**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server のバージョン**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [最新の Azure PowerShell コマンドをインストールします](/powershell/azure/overview) (PowerShell で自動修正を構成する場合)。

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> 自動修正は、SQL Server IaaS Agent 拡張機能に依存します。 現在の SQL 仮想マシン ギャラリー イメージでは、既定でこの拡張機能が追加されます。 詳細については、[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に関するページをご覧ください。
> 
> 

## <a name="settings"></a>設定
自動修正で構成できるオプションを次の表に示します。 実際の構成手順は、Azure ポータルと Azure Windows PowerShell コマンドのどちらを使用するかによって異なります。

| Setting | 指定できる値 | 説明 |
| --- | --- | --- |
| **自動修正** |有効/無効 (無効) |Azure 仮想マシンの自動修正を有効または無効にします。 |
| **メンテナンス スケジュール** |毎日、月曜日、火曜日、水曜日、木曜日、金曜日、土曜日、日曜日 |仮想マシンの Windows、SQL Server、および Microsoft の更新プログラムをダウンロードしてインストールするスケジュール。 |
| **メンテナンスの開始時間** |0 ～ 24 |仮想マシンを更新するローカルの開始時刻。 |
| **メンテナンス時間** |30 ～ 180 |更新プログラムのダウンロードとインストールを完了するのに許可されている時間 (分単位) |
| **パッチのカテゴリ** |重要 | ダウンロードしてインストールする Windows 更新プログラムのカテゴリ。|

## <a name="configuration-in-the-portal"></a>ポータルでの構成
Azure ポータルを使用して、プロビジョニング中または既存の VM 用に、自動修正を構成することができます。

### <a name="new-vms"></a>新しい VM
Resource Manager デプロイ モデルで新しい SQL Server 仮想マシンを作成するときに、Azure ポータルを使用して、自動修正を構成します。

**[SQL Server の設定]** タブで **[自動修正]** の **[構成の変更]** を選択します。 次の Azure ポータルのスクリーンショットは、 **[SQL 自動修正]** ブレードを示しています。

![Azure ポータルでの SQL 自動修正](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

コンテキストについては、 [Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)に関するページのトピックをご覧ください。

### <a name="existing-vms"></a>既存の VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

既存の SQL Server 仮想マシンの場合、[[SQL 仮想マシン リソース]](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) を開き、 **[設定]** の **[ファイルの部分置換中]** を選択します。 

![既存の VM の SQL 自動修正](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


終了したら、 **[SQL Server の構成]** ブレードの下部にある **[OK]** ボタンをクリックして変更を保存します。

自動修正を初めて有効にすると、バックグラウンドで SQL Server IaaS Agent が構成されます。 この間、自動修正が構成されていることは、Azure ポータルに示されない可能性があります。 エージェントがインストールされ、構成されるまで数分待ちます。 その後、Azure ポータルに新しい設定が反映されます。

## <a name="configuration-with-powershell"></a>PowerShell での構成
SQL VM をプロビジョニングしたら、PowerShell を使用して自動修正を構成します。

次の例では、PowerShell を使用して、既存の SQL Server VM で自動修正を構成しています。 **New-AzVMSqlServerAutoPatchingConfig** コマンドは、自動更新の新しいメンテナンス期間を構成します。

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> 拡張機能がまだインストールされていない場合、拡張機能をインストールすると、SQL Server サービスが再起動されます。

この例に基づいて、対象の Azure VM への実際の影響を次の表に示します。

| パラメーター | 効果 |
| --- | --- |
| **DayOfWeek** |毎週木曜日に修正プログラムがインストールされます。 |
| **MaintenanceWindowStartingHour** |午前 11 時に更新が開始されます。 |
| **MaintenanceWindowsDuration** |修正プログラムを 120 分以内にインストールする必要があります。 開始時刻に基づき、修正プログラムのインストールは午後 1 時までに完了する必要があります。 |
| **PatchCategory** |このパラメーターに指定可能な設定は **Important**だけです。 これにより、"重要" と書かれた Windows 更新プログラムがインストールされます。このカテゴリに含まれていない SQL Server 更新プログラムはインストールされません。 |

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

自動修正を無効にするには、**New-AzVMSqlServerAutoPatchingConfig** の **-Enable** パラメーターを指定せずに、同じスクリプトを実行します。 **-Enable** パラメーターがない場合は、機能を無効にするコマンドが伝えられます。

## <a name="next-steps"></a>次の手順
その他の利用可能なオートメーション タスクについては、 [SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、 [Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)に関するページをご覧ください。

