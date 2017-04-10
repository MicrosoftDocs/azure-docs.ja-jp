---
title: "SQL Server VM の自動修正 (クラシック) | Microsoft Docs"
description: "Azure でクラシック デプロイメント モデルを使用して実行されている SQL Server Virtual Machines の自動修正機能について説明します。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/17/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 69e3172b1eac844863f51997f3061619f50b68da
ms.lasthandoff: 03/25/2017


---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Azure Virtual Machines での SQL Server の自動修正 (クラシック)
> [!div class="op_single_selector"]
> * [リソース マネージャー](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [クラシック](../classic/sql-automated-patching.md)
> 
> 

自動修正では、SQL Server を実行している Azure 仮想マシンのメンテナンス期間が設定されます。 このメンテナンス期間にのみ、自動更新プログラムをインストールできます。 これにより、SQL Server では、システムの更新とこれに関連する再起動が、データベースに最適な時間帯に実行されるようになります。 自動修正は、 [SQL Server IaaS Agent 拡張機能](../classic/sql-server-agent-extension.md)に依存します。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 この記事の Resource Manager バージョンを確認するには、[Resource Manager バージョンの Azure Virtual Machines での SQL Server の自動修正](../sql/virtual-machines-windows-sql-automated-patching.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件
自動修正を使用するには、次の前提条件を検討してください。

**オペレーティング システム**:

* Windows Server 2012
* Windows Server 2012 R2

**SQL Server のバージョン**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [最新の Azure PowerShell コマンドをインストールします](/powershell/azureps-cmdlets-docs)。

**SQL Server IaaS 拡張機能**:

* [SQL Server IaaS 拡張機能をインストールします](../classic/sql-server-agent-extension.md)。

## <a name="settings"></a>Settings
自動修正で構成できるオプションを次の表に示します。 クラシック VM の場合は、PowerShell を使用してこれらの設定を構成する必要があります。

| Setting | 指定できる値 | Description |
| --- | --- | --- |
| **自動修正** |有効/無効 (無効) |Azure 仮想マシンの自動修正を有効または無効にします。 |
| **メンテナンス スケジュール** |毎日、月曜日、火曜日、水曜日、木曜日、金曜日、土曜日、日曜日 |仮想マシンの Windows、SQL Server、および Microsoft の更新プログラムをダウンロードしてインストールするスケジュール。 |
| **メンテナンスの開始時間** |0 ～ 24 |仮想マシンを更新するローカルの開始時刻。 |
| **メンテナンス時間** |30 ～ 180 |更新プログラムのダウンロードとインストールを完了するのに許可されている時間 (分単位) |
| **パッチのカテゴリ** |重要: |ダウンロードしてインストールする更新プログラムのカテゴリ。 |

## <a name="configuration-with-powershell"></a>PowerShell での構成
次の例では、PowerShell を使用して、既存の SQL Server VM で自動修正を構成しています。 **New-AzureVMSqlServerAutoPatchingConfig** コマンドは、自動更新の新しいメンテナンス期間を構成します。

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

この例に基づいて、対象の Azure VM への実際の影響を次の表に示します。

| パラメーター | 効果 |
| --- | --- |
| **DayOfWeek** |毎週木曜日に修正プログラムがインストールされます。 |
| **MaintenanceWindowStartingHour** |午前 11 時に更新が開始されます。 |
| **MaintenanceWindowsDuration** |修正プログラムを 120 分以内にインストールする必要があります。 開始時刻に基づき、修正プログラムのインストールは午後 1 時までに完了する必要があります。 |
| **PatchCategory** |このパラメーターに指定可能な設定は "Important" だけです。 |

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

自動修正を無効にするには、New-AzureVMSqlServerAutoPatchingConfig の -Enable パラメーターを指定せずに、同じスクリプトを実行します。 インストールと同様に、自動修正の無効化には数分かかる場合があります。

## <a name="next-steps"></a>次のステップ
その他の利用可能なオートメーション タスクについては、 [SQL Server IaaS Agent 拡張機能](../classic/sql-server-agent-extension.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、 [Azure Virtual Machines における SQL Server の概要](../sql/virtual-machines-windows-sql-server-iaas-overview.md)に関するページをご覧ください。


