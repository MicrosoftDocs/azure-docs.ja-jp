---
title: SQL Server VM の自動修正 (Resource Manager) | Microsoft Docs
description: この記事では、Azure で実行されている SQL Server 仮想マシンに対する、Resource Manager を使用した自動修正機能について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 429fe39f84a54c22fa97178b85f417d76dc84a8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359474"
---
# <a name="automated-patching-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Azure 仮想マシンでの SQL Server の自動修正 (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

自動修正では、SQL Server を実行している Azure 仮想マシンのメンテナンス期間が設定されます。 このメンテナンス期間にのみ、自動更新プログラムをインストールできます。 これにより、SQL Server では、システムの更新とこれに関連する再起動が、データベースに最適な時間帯に実行されるようになります。 

> [!IMPORTANT]
> **重要** または **クリティカル** とマークされた Windows および SQL Server 更新プログラムのみがインストールされます。 **重要** または **クリティカル** とマークされていない、サービスパックや累積更新プログラムなどのその他の SQL Server の更新プログラムは、手動でインストールする必要があります。 

自動修正は、[SQL Server IaaS (サービスとしてのインフラストラクチャ) Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に依存します。

## <a name="prerequisites"></a>前提条件
自動修正を使用するには、次の前提条件を検討してください。

**[オペレーティング システム]** :

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

**SQL Server のバージョン**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [最新の Azure PowerShell コマンドをインストールします](/powershell/azure/) (PowerShell で自動修正を構成する場合)。

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> 自動修正は、SQL Server IaaS Agent 拡張機能に依存します。 現在の SQL 仮想マシン ギャラリー イメージでは、既定でこの拡張機能が追加されます。 詳細については、[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に関するページをご覧ください。
> 
> 

## <a name="settings"></a>設定
自動修正で構成できるオプションを次の表に示します。 実際の構成手順は、Azure ポータルと Azure Windows PowerShell コマンドのどちらを使用するかによって異なります。

| 設定 | 指定できる値 | 説明 |
| --- | --- | --- |
| **自動修正** |有効/無効 (無効) |Azure 仮想マシンの自動修正を有効または無効にします。 |
| **メンテナンス スケジュール** |毎日、月曜日、火曜日、水曜日、木曜日、金曜日、土曜日、日曜日 |仮想マシンの Windows、SQL Server、および Microsoft の更新プログラムをダウンロードしてインストールするスケジュール。 |
| **メンテナンスの開始時間** |0 ～ 24 |仮想マシンを更新するローカルの開始時刻。 |
| **メンテナンス時間** |30 ～ 180 |更新プログラムのダウンロードとインストールを完了するのに許可されている時間 (分単位) |
| **パッチのカテゴリ** |重要 | ダウンロードしてインストールする Windows 更新プログラムのカテゴリ。|

## <a name="configure-in-the-azure-portal"></a>Azure portal で構成する
Azure ポータルを使用して、プロビジョニング中または既存の VM 用に、自動修正を構成することができます。

### <a name="new-vms"></a>新しい VM
Resource Manager デプロイ モデルで新しい SQL Server 仮想マシンを作成するときに、Azure portal を使用して、自動修正を構成します。

**[SQL Server の設定]** タブで **[自動修正]** の **[構成の変更]** を選択します。 次の Azure ポータルのスクリーンショットは、 **[SQL 自動修正]** ブレードを示しています。

![Azure portal での SQL 自動修正](./media/automated-patching/azure-sql-arm-patching.png)

詳細については、「[Azure ポータルでの SQL Server 仮想マシンのプロビジョニング](create-sql-vm-portal.md)」をご覧ください。

### <a name="existing-vms"></a>既存の VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

既存の SQL Server 仮想マシンの場合、[[SQL 仮想マシン リソース]](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) を開き、 **[設定]** の **[ファイルの部分置換中]** を選択します。 

![既存の VM の SQL 自動修正](./media/automated-patching/azure-sql-rm-patching-existing-vms.png)


終了したら、 **[SQL Server の構成]** ブレードの下部にある **[OK]** ボタンをクリックして変更を保存します。

自動修正を初めて有効にすると、バックグラウンドで SQL Server IaaS Agent が構成されます。 この間、自動修正が構成されていることは、Azure ポータルに示されない可能性があります。 エージェントがインストールされ、構成されるまで数分待ちます。 その後、Azure ポータルに新しい設定が反映されます。

## <a name="configure-with-powershell"></a>PowerShell での構成
SQL VM をプロビジョニングしたら、PowerShell を使用して自動修正を構成します。

次の例では、PowerShell を使用して、既存の SQL Server VM で自動修正を構成しています。 **New-AzVMSqlServerAutoPatchingConfig** コマンドは、自動更新の新しいメンテナンス期間を構成します。

```azurepowershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"
$aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s
Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname
```

> [!IMPORTANT]
> 拡張機能がまだインストールされていない場合、インストールすると、SQL Server が再起動されます。

この例に基づいて、対象の Azure VM への実際の影響を次の表に示します。

| パラメーター | 結果 |
| --- | --- |
| **DayOfWeek** |毎週木曜日に修正プログラムがインストールされます。 |
| **MaintenanceWindowStartingHour** |午前 11 時に更新が開始されます。 |
| **MaintenanceWindowsDuration** |修正プログラムを 120 分以内にインストールする必要があります。 開始時刻に基づき、修正プログラムのインストールは午後 1 時までに完了する必要があります。 |
| **PatchCategory** |このパラメーターに指定可能な設定は **Important** だけです。 これにより、"重要" と書かれた Windows 更新プログラムがインストールされます。このカテゴリに含まれていない SQL Server 更新プログラムはインストールされません。 |

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

自動修正を無効にするには、**New-AzVMSqlServerAutoPatchingConfig** の **-Enable** パラメーターを指定せずに、同じスクリプトを実行します。 **-Enable** パラメーターがない場合は、機能を無効にするコマンドが伝えられます。

## <a name="next-steps"></a>次のステップ
その他の利用可能なオートメーション タスクについては、 [SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、[Azure 仮想マシンにおける SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページをご覧ください。

