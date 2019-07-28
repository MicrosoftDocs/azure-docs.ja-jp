---
title: SQL Server VM の自動修正 (クラシック) | Microsoft Docs
description: Azure でクラシック デプロイメント モデルを使用して実行されている SQL Server Virtual Machines の自動修正機能について説明します。
services: virtual-machines-windows
documentationcenter: na
author: mashamsft
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aa912e3eb76d72e7a79c83d7e51d493310bd36b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362137"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Azure Virtual Machines での SQL Server の自動修正 (クラシック)
> [!div class="op_single_selector"]
> * [リソース マネージャー](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [クラシック](../classic/sql-automated-patching.md)
> 
> 

自動修正では、SQL Server を実行している Azure 仮想マシンのメンテナンス期間が設定されます。 このメンテナンス期間にのみ、自動更新プログラムをインストールできます。 これにより、SQL Server では、システムの更新とこれに関連する再起動が、データベースに最適な時間帯に実行されるようになります。 

> [!IMPORTANT]
> **重要**と書かれた Windows 更新プログラムのみがインストールされます。 累積更新プログラムなどの他の SQL Server 更新プログラムは、手動でインストールする必要があります。 

自動修正は、 [SQL Server IaaS Agent 拡張機能](../classic/sql-server-agent-extension.md)に依存します。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 この記事の Resource Manager バージョンを確認するには、[Resource Manager バージョンの Azure Virtual Machines での SQL Server の自動修正](../sql/virtual-machines-windows-sql-automated-patching.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件
自動修正を使用するには、次の前提条件を検討してください。

**オペレーティング システム**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server のバージョン**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [最新の Azure PowerShell コマンドをインストールします](/powershell/azure/overview)。

**SQL Server IaaS 拡張機能**:

* [SQL Server IaaS 拡張機能をインストールします](../classic/sql-server-agent-extension.md)。

## <a name="settings"></a>設定
自動修正で構成できるオプションを次の表に示します。 クラシック VM の場合は、PowerShell を使用してこれらの設定を構成する必要があります。

| Setting | 指定できる値 | 説明 |
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
| **MaintenanceWindowDuration** |修正プログラムを 120 分以内にインストールする必要があります。 開始時刻に基づき、修正プログラムのインストールは午後 1 時までに完了する必要があります。 |
| **PatchCategory** |このパラメーターに指定可能な設定は "Important" だけです。 |

SQL Server IaaS エージェントのインストールと構成には数分かかる場合があります。

自動修正を無効にするには、New-AzureVMSqlServerAutoPatchingConfig の -Enable パラメーターを指定せずに、同じスクリプトを実行します。 インストールと同様に、自動修正の無効化には数分かかる場合があります。

## <a name="next-steps"></a>次の手順
その他の利用可能なオートメーション タスクについては、 [SQL Server IaaS Agent 拡張機能](../classic/sql-server-agent-extension.md)に関するページをご覧ください。

Azure VM で SQL Server を実行する方法の詳細については、 [Azure Virtual Machines における SQL Server の概要](../sql/virtual-machines-windows-sql-server-iaas-overview.md)に関するページをご覧ください。

