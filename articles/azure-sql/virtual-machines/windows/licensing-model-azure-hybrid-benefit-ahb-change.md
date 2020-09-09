---
title: Azure での SQL VM のライセンス モデルを変更する
description: Azure ハイブリッド特典を使用して、Azure の SQL Server VM のライセンスを従量課金制からライセンス持ち込みに切り替える方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f02f31e0fc8943682af77ca6f506d15f36e88146
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668901"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Azure で SQL 仮想マシンのライセンス モデルを変更する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


この記事では、新しい SQL Server VM リソース プロバイダーである **Microsoft.SqlVirtualMachine** を使用して Azure 内の SQL Server 仮想マシン (VM) のライセンス モデルを変更する方法について説明します。

SQL Server をホストする VM には、従量課金制、Azure ハイブリッド特典 (AHB)、およびディザスター リカバリー (DR) の 3 種類のライセンス モデルがあります。 ご利用の SQL Server VM のライセンス モデルは、Azure portal、Azure CLI、または PowerShell を使用して変更することができます。 

- **従量課金制**モデルでは、Azure VM を実行する秒単位のコストに、SQL Server ライセンスのコストが含まれています。
- [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)では、SQL Server を実行する VM に対して独自の SQL Server ライセンスを使用することができます。 
- **ディザスター リカバリー** ライセンスは、Azure での[無料 DR レプリカ](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)に使用します。 

Azure ハイブリッド特典では、Azure 仮想マシン上で SQL Server ライセンスをソフトウェア アシュアランス ("条件を満たしたライセンス") 付きで使用できます。 Azure ハイブリッド特典の場合、VM 上での SQL Server ライセンスの使用に対してお客様は課金されません。 ただし、基になるクラウド コンピューティング (基本料金)、ストレージ、およびバックアップのコストについては、引き続き料金を支払う必要があります。 また、サービスの使用に関連付けられている I/O についても支払う必要があります (該当する場合)。

Microsoft 製品の利用規約に従って、"お客様は、Azure 上でワークロードを構成時に、SQL Server 向け Azure ハイブリッド特典に基づいて Azure SQL Database (Managed Instance、Elastic Pool、Single Database)、Azure Data Factory、SQL Server Integration Services、または SQL Server Virtual Machines を使用していることを示す必要があります。"

Azure VM 上の SQL Server 向け Azure ハイブリッド特典を使用していることと、準拠していることを示すには、次の 3 つのオプションがあります。

- Azure Marketplace からのライセンス持ち込み SQL Server イメージを使用して、仮想マシンをプロビジョニングします。 このオプションは、マイクロソフト エンタープライズ契約を結んでいるお客様のみが利用できます。
- Azure Marketplace からの従量課金制の SQL Server イメージを使用して、仮想マシンをプロビジョニングし、Azure ハイブリッド特典をアクティブにします。
- Azure VM に SQL Server をセルフインストールし、手動で [SQL VM リソース プロバイダーに登録](sql-vm-resource-provider-register.md)して、Azure ハイブリッド特典をアクティブにします。

SQL Server のライセンスの種類は、VM がプロビジョニングされるとき、またはその後の任意のときに構成できます。 ライセンス モデル間の切り替えを行っても、ダウンタイムは発生せず、VM や SQL Serverサービスが再起動されたり、追加コストが追加されることもなく、すぐに有効になります。 実際には、Azure ハイブリッド特典をアクティブにするとコストが*削減*されます。

## <a name="prerequisites"></a>前提条件

SQL Server VM のライセンスモデルを変更するには、次の要件があります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL VM リソース プロバイダー](sql-vm-resource-provider-register.md)に登録された [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。
- [ソフトウェアアシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) は、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用するための要件です。 


## <a name="vms-already-registered-with-the-resource-provider"></a>リソースプロバイダーに登録済みの VM 

# <a name="the-azure-portal"></a>[Azure ポータル](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

ライセンス モデルは、ポータルから直接変更できます。 

1. [Azure portal](https://portal.azure.com) を開き、ご利用の SQL Server VM 用の [SQL 仮想マシン リソース](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)を開きます。 
1. **[設定]** の **[構成]** を選択します。 
1. **[Azure ハイブリッド特典]** オプションを選択し、ソフトウェア アシュアランス付きの SQL Server ライセンスがあることを確認するチェックボックスをオンにします。 
1. **[構成]** ページの下部にある **[適用]** を選択します。 

![ポータル内の Azure ハイブリッド特典](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して、ご利用のライセンス モデルを変更することができます。  


**Azure ハイブリッド特典**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**従量課金制**: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**ディザスター リカバリー (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して、ご利用のライセンス モデルを変更できます。

**Azure ハイブリッド特典**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**従量課金制**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**ディザスター リカバリー** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>リソースプロバイダーに未登録の VM

従量課金制 Azure Marketplace イメージから SQL Server VM をプロビジョニングした場合、SQL Server ライセンスの種類は従量課金制になります。 Azure Marketplace からのライセンス持ち込みイメージを使用して SQL Server VM をプロビジョニングした場合、ライセンスの種類は AHUB になります。 既定 (従量課金制) またはライセンス持ち込み Azure Marketplace イメージからプロビジョニングされた SQL Server VM はすべて、SQL VM リソース プロバイダーに自動的に登録されます。その結果、[ライセンスの種類](#vms-already-registered-with-the-resource-provider)の変更が可能になります。

SQL Server については、Azure ハイブリッド特典を介して Azure VM にセルフインストールすることのみが可能です。 Microsoft 製品の利用規約に従って Azure ハイブリッド特典の使用状況を示すには、SQL Server ライセンスを Azure ハイブリッド特典に設定して、[これらの VM を SQL VM リソース プロバイダーに登録](sql-vm-resource-provider-register.md)する必要があります。

SQL Server VM が SQL VM リソースプロバイダーに登録されている場合にのみ、SQL Server VM のライセンスの種類を従量課金制または Azure ハイブリッド特典に変更できます。

## <a name="remarks"></a>解説

- Azure Cloud Solution Provider (CSP) のお客様は、アクティブなソフトウェア アシュアランスを所有している場合、従量課金制の VM をデプロイしてからそれをライセンス持ち込みに変換することで、Azure ハイブリッド特典を利用できるようになります。
- SQL Server VM リソースを削除する場合は、イメージのハード コーディングされたライセンス設定に戻ります。 
- ライセンス モデルを変更する機能は、SQL VM リソース プロバイダーの機能です。 Azure portal を介して Azure Marketplace イメージをデプロイすると、SQL Server VM がリソース プロバイダーに自動的に登録されます。 ただし、SQL Server をセルフインストールするお客様は、手動で [SQL Server VM を登録](sql-vm-resource-provider-register.md)する必要があります。 
- SQL Server VM を可用性セットに追加するには、VM を再作成する必要があります。 そのため、可用性セットに追加された VM はいずれも、既定のライセンスの種類である従量課金制に戻ることになります。 Azure ハイブリッド特典を再び有効にする必要があります。 


## <a name="limitations"></a>制限事項

ライセンスモデルの変更は次のとおりです。
   - [ソフトウェアアシュアランス](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview) をお持ちのお客様のみご利用いただけます。
   - SQL Server の Standard および Enterprise エディションでのみサポートされています。 Express、Web、Developer のライセンス変更はサポートされていません。 
   - Azure Resource Manager モデルを介してデプロイされた仮想マシンでのみサポートされます。 クラシック モデルを介してデプロイされた仮想マシンはサポートされていません。 
   - パブリック クラウドまたは Azure Government クラウドでのみ使用できます。 
   - 1 つのネットワークインターフェイス (NIC) を持つ仮想マシンでのみサポートされます。 


## <a name="known-errors"></a>既知のエラー

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>リソース 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' がリソース グループ '\<resource-group>' の下で見つかりませんでした。

このエラーは、SQL Server VM 上で SQL VM リソース プロバイダーに登録されていないライセンス モデルを変更しようとしたときに発生します。

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

リソース プロバイダーをサブスクリプションに登録してから、[そのリソース プロバイダーにご利用の SQL Server VM を登録する](sql-vm-resource-provider-register.md)必要があります。 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>仮想マシン '\<vmname\>' に複数の NIC が関連付けられています

このエラーは、複数の NIC を持つ仮想マシンで発生します。 ライセンスモデルを変更する前に、いずれかの NIC を削除します。 ライセンスモデルを変更した後に NIC を VM に再び追加することはできますが、自動バックアップや修正プログラム適用などの Azure portal の操作はサポートされなくなります。 


## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](../../database/doc-changes-updates-release-notes.md)


