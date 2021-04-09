---
title: Azure での SQL VM のライセンス モデルを変更する
description: Azure ハイブリッド特典を使用して、Azure の SQL Server VM のライセンスを従量課金制からライセンス持ち込みに切り替える方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5813331d5eafd953d776dd19d9cc885ff71b8be0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361555"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Azure で SQL 仮想マシンのライセンス モデルを変更する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


この記事では、[SQL IaaS Agent 拡張機能](./sql-server-iaas-agent-extension-automate-management.md)を使用して Azure 内の SQL Server 仮想マシン (VM) のライセンス モデルを変更する方法について説明します。

## <a name="overview"></a>概要

SQL Server をホストする Azure VM には、従量課金制、Azure ハイブリッド特典 (AHB)、および高可用性/ディザスター リカバリー (HA/DR) の 3 種類のライセンス モデルがあります。 ご利用の SQL Server VM のライセンス モデルは、Azure portal、Azure CLI、または PowerShell を使用して変更することができます。 

- **従量課金制** モデルでは、Azure VM を実行する秒単位のコストに、SQL Server ライセンスのコストが含まれています。
- [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)では、SQL Server を実行する VM に対して独自の SQL Server ライセンスを使用することができます。 
- **HA/DR** ライセンスの種類は、Azure での [無料 HA/DR レプリカ](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)に使用します。 

Azure ハイブリッド特典では、Azure 仮想マシン上で SQL Server ライセンスをソフトウェア アシュアランス ("条件を満たしたライセンス") 付きで使用できます。 Azure ハイブリッド特典の場合、VM 上での SQL Server ライセンスの使用に対してお客様は課金されません。 ただし、基になるクラウド コンピューティング (基本料金)、ストレージ、およびバックアップのコストについては、引き続き料金を支払う必要があります。 また、サービスの使用に関連付けられている I/O についても支払う必要があります (該当する場合)。

Microsoft [製品の利用規約](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS)に従って:"お客様は、Azure 上でワークロードを構成時に、SQL Server 向け Azure ハイブリッド特典に基づいて Azure SQL Database (Managed Instance、Elastic Pool、Single Database)、Azure Data Factory、SQL Server Integration Services、または SQL Server Virtual Machines を使用していることを示す必要があります。"

Azure VM 上の SQL Server 向け Azure ハイブリッド特典を使用していることと、準拠していることを示すには、次の 3 つのオプションがあります。

- Azure Marketplace からのライセンス持ち込み SQL Server イメージを使用して、仮想マシンをプロビジョニングします。 このオプションは、マイクロソフト エンタープライズ契約を結んでいるお客様のみが利用できます。
- Azure Marketplace からの従量課金制の SQL Server イメージを使用して、仮想マシンをプロビジョニングし、Azure ハイブリッド特典をアクティブにします。
- Azure VM に SQL Server をセルフインストールし、手動で [SQL IaaS Agent 拡張機能に登録](sql-agent-extension-manually-register-single-vm.md)して、Azure ハイブリッド特典をアクティブにします。

SQL Server のライセンスの種類は、VM がプロビジョニングされるとき、またはその後の任意のときに構成できます。 ライセンス モデル間の切り替えを行っても、ダウンタイムは発生せず、VM や SQL Serverサービスが再起動されたり、追加コストが追加されることもなく、すぐに有効になります。 実際には、Azure ハイブリッド特典をアクティブにするとコストが *削減* されます。

## <a name="prerequisites"></a>前提条件

SQL Server VM のライセンスモデルを変更するには、次の要件があります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL IaaS Agent 拡張機能](./sql-server-iaas-agent-extension-automate-management.md)に登録された [SQL Server VM](./create-sql-vm-portal.md)。
- [ソフトウェアアシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) は、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用するための要件です。 


## <a name="change-license-model"></a>ライセンス モデルの変更

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

ライセンス モデルは、ポータルから直接変更できます。 

1. [Azure portal](https://portal.azure.com) を開き、ご利用の SQL Server VM 用の [SQL 仮想マシン リソース](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)を開きます。 
1. **[設定]** の **[構成]** を選択します。 
1. **[Azure ハイブリッド特典]** オプションを選択し、ソフトウェア アシュアランス付きの SQL Server ライセンスがあることを確認するチェックボックスをオンにします。 
1. **[構成]** ページの下部にある **[適用]** を選択します。 

![ポータル内の Azure ハイブリッド特典](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、ご利用のライセンス モデルを変更することができます。  

**license-type** に次の値を指定します。
- Azure ハイブリッド特典の場合は `AHUB`
- 従量課金制の場合は `PAYG`
- 無料 HA/DR レプリカをアクティブにする場合は `DR`


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して、ご利用のライセンス モデルを変更できます。

**license-type** に次の値を指定します。
- Azure ハイブリッド特典の場合は `AHUB`
- 従量課金制の場合は `PAYG`
- 無料 HA/DR レプリカをアクティブにする場合は `DR`


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>解説

- Azure Cloud Solution Provider (CSP) のお客様は、アクティブなソフトウェア アシュアランスを所有している場合、従量課金制の VM をデプロイしてからそれをライセンス持ち込みに変換することで、Azure ハイブリッド特典を利用できるようになります。
- SQL 仮想マシン リソースを削除する場合は、イメージにハード コーディングされたライセンス設定に戻ります。 
- ライセンス モデルを変更する機能は、SQL IaaS Agent 拡張機能の機能です。 Azure portal を介して Azure Marketplace イメージをデプロイすると、SQL Server VM が拡張機能に自動的に登録されます。 ただし、SQL Server をセルフインストールするお客様は、手動で [SQL Server VM を登録](sql-agent-extension-manually-register-single-vm.md)する必要があります。 
- SQL Server VM を可用性セットに追加するには、VM を再作成する必要があります。 そのため、可用性セットに追加された VM はいずれも、既定のライセンスの種類である従量課金制に戻ることになります。 Azure ハイブリッド特典を再び有効にする必要があります。 


## <a name="limitations"></a>制限事項

ライセンスモデルの変更は次のとおりです。
   - [ソフトウェアアシュアランス](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview) をお持ちのお客様のみご利用いただけます。
   - SQL Server の Standard および Enterprise エディションでのみサポートされています。 Express、Web、Developer のライセンス変更はサポートされていません。 
   - Azure Resource Manager モデルを介してデプロイされた仮想マシンでのみサポートされます。 クラシック モデルを介してデプロイされた仮想マシンはサポートされていません。 
   - パブリック クラウドまたは Azure Government クラウドでのみ使用できます。 

> [!Note]
> Azure ハイブリッド特典の対象となるのは、ソフトウェア アシュアランスまたはサブスクリプション ライセンス付きの SQL Server コア ベース ライセンスのみです。 SQL Server に Server + CAL ライセンスを使用していて、ソフトウェア アシュアランスをお持ちの場合は、Azure SQL Server 仮想マシン イメージに対してライセンス持ち込みを使用することで、これらのサーバーのライセンス モビリティを利用できますが、Azure ハイブリッド特典の他の機能は利用できません。 

## <a name="known-errors"></a>既知のエラー

よく知られているエラーとその解決策を確認します。 

**リソース 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' がリソース グループ '\<resource-group>' の下で見つかりませんでした。**

このエラーは、SQL Server VM 上で SQL Server IaaS Agent 拡張機能に登録されていないライセンス モデルを変更しようとしたときに発生します。

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

リソース プロバイダーにサブスクリプションを登録してから、[SQL Server IaaS Agent 拡張機能にご利用の SQL Server VM を登録する](sql-agent-extension-manually-register-single-vm.md)必要があります。 



## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](../../database/doc-changes-updates-release-notes.md)
* [SQL IaaS Agent 拡張機能の概要](./sql-server-iaas-agent-extension-automate-management.md)
