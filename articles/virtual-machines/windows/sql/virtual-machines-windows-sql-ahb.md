---
title: Azure で SQL Server VM のライセンス モデルを変更する方法 |Microsoft Docs
description: Azure での SQL 仮想マシンのライセンスを切り替える方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 667a696e96234aca33981946a5b063ab5bfb080b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075918"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure での SQL Server 仮想マシンのライセンス モデルを変更する方法
このアーティクルでは、新しい SQL VM リソース プロバイダーである、**Microsoft.SqlVirtualMachine** を使用して Azure での SQL Server 仮想マシンのライセンス モデルを変更する方法を説明します。 SQL Server をホストする仮想マシン (VM) には、従量課金制とライセンス持ち込み (BYOL) の 2 種類のライセンス モデルがあります。 そして現在は、Azure portal、Azure CLI、または PowerShell を使用して、SQL Server VM で使用するライセンス モデルを変更することができます。 

**従量課金制** (PAYG) モデルでは、Azure VM を実行する秒単位のコストに SQL Server ライセンスのコストが含まれます。

**ライセンス持ち込み** (BYOL) モデルは [Azure ハイブリッド特典 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) とも呼ばれ、ユーザーは SQL Server を実行する VM で自分の SQL Server ライセンスを使用できます。 価格の詳細については、[SQL Server VM 料金ガイド](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)をご参照ください。

2 種類のライセンスモデル間の切り替えによる**ダウンタイム**および VM の再起動は、**追加コスト**は発生せず (実際には、AHB の有効化によるコスト*削減*)、**ただちに有効となります**。 

## <a name="remarks"></a>解説


 - Azure Cloud Solution Partner (CSP) のお客様は、最初に従量課金制の VM をデプロイした後、それをライセンス持ち込みに変換することによって、Azure ハイブリッド特典を利用できます。 
 - カスタム SQL Server VM イメージをリソース プロバイダーに登録するとき、ライセンスの種類を 'AHUB' として指定します。 ライセンスの種類を空白のままにするか、'PAYG' を指定すると、登録は失敗します。 
 - SQL Server VM リソースを削除する場合は、イメージのハード コーディングされたライセンス設定に戻ります。 
 - SQL Server VM を可用性セットに追加するには、VM を再作成する必要があります。 そのため、可用性セットに追加されたすべての VM は既定の従量課金制ライセンス タイプに戻ることになります。さらに、AHB をもう一度有効にする必要があります。 
 - ライセンス モデルを変更する機能は、SQL VM リソース プロバイダーの機能です。 Azure portal を介してマーケットプレース イメージをデプロイすると、SQL Server VM がリソース プロバイダーに自動的に登録されます。 ただし、SQL Server を自分でインストールするお客様は、手動で [SQL Server VM を登録](#register-sql-server-vm-with-the-sql-vm-resource-provider)する必要があります。 
 

 
## <a name="limitations"></a>制限事項

 - 現在、ライセンス モデルの変換は、従量課金制の SQL Server VM イメージで始めた場合にのみ可能です。 ポータルからライセンス持ち込みイメージで始めた場合は、そのイメージを従量課金制に変換することはできません。
 - ライセンス モデルの変更は、Resource Manager モデルを使用してデプロイされた仮想マシンについてのみサポートされます。 クラシック モデルを使用してデプロイされた VM はサポートされません。 
 - ライセンス モデルの変更は、パブリック クラウドのインストールでのみ有効です。
 - ライセンス モデルの変更は、1 つの NIC (ネットワーク インターフェイス) を持つ仮想マシンでのみサポートされます。 複数の NIC を持つ仮想マシンでは、手順を実行する前に、まず (Azure portal を使用して) NIC の 1 つを削除する必要があります。 そうしないと、次のようなエラーが発生します。`The virtual machine '\<vmname\>' has more than one NIC associated.` ライセンス モードを変更した後に NIC を再び VM に追加できる可能性はありますが、SQL 構成ブレードを介して実行された操作は、自動パッチや自動バックアップと同様に、もうサポートされていると見なされなくなります。

## <a name="prerequisites"></a>前提条件

SQL VM リソース プロバイダーを利用するには、SQL IaaS 拡張機能が必要です。 そのため、SQL VM リソース プロバイダーの利用を続けるには、以下が必要です。
- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)。 
- [SQL IaaS 拡張機能](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)がインストールされている*従量課金制*の [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 

## <a name="with-the-azure-portal"></a>Azure Portal の場合

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

ライセンス モデルをポータルから直接変更できます。 

1. [Azure portal](https://portal.azure.com) を開き、お使いの SQL Server VM 用の [SQL 仮想マシン リソース](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource)を起動します。 
1. **[設定]** の **[構成]** を選択します。 
1. **[Azure ハイブリッド特典]** オプションを選択し、ソフトウェア アシュアランス付きの SQL Server ライセンスがあることを確認します。 
1. **[構成]** ページの下部にある **[適用]** を選択します。 

![ポータルの AHB](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > このオプションは、ライセンス持ち込みイメージには使用できません。 

## <a name="with-azure-cli"></a>Azure CLI の場合

Azure CLI を使用して、ライセンス モデルを変更することができます。  

次のコード スニペットでは、従量課金制ライセンス モデルを BYOL (Azure ハイブリッド特典の使用) に切り替えます。

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

次のコード スニペットは、BYOL モデルを従量課金制に切り替えます。 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

## <a name="with-powershell"></a>PowerShell の場合
PowerShell を使用してライセンス モデルを変更できます。

次のコード スニペットでは、従量課金制ライセンス モデルを BYOL (Azure ハイブリッド特典の使用) に切り替えます。

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

次のコード スニペットでは、BYOL モデルを従量課金制に切り替えます。

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>SQL Server VM を SQL VM リソース プロバイダーに登録する
ある特定の状況で、SQL Server VM を SQL VM リソース プロバイダーに手動で登録する必要がある場合があります。 これを行うには、リソース プロバイダーをサブスクリプションに手動で登録する必要がある場合もあります。 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>SQL VM リソース プロバイダーをサブスクリプションに登録する 

自分の SQL Server VM を SQL リソース プロバイダーに登録するには、リソース プロバイダーを自分のサブスクリプションに登録する必要があります。 これは、Azure portal または Azure CLI を使用して行うことができます。 

#### <a name="with-the-azure-portal"></a>Azure Portal の場合
以下の手順では、Azure portal を使用して SQL リソース プロバイダーを Azure サブスクリプションに登録します。 

1. Azure portal を開き、**すべてのサービス**に移動します。 
1. **サブスクリプション**に移動し、関心のあるサブスクリプションを選択します。  
1. **[サブスクリプション]** ブレードで、 **[リソース プロバイダー]** に移動します。 
1. フィルター内に`sql`と入力し、SQL 関連のリソース プロバイダーを表示します。 
1. 目的となるアクションに応じて、*登録*、*再登録*、または*登録解除*のいずれかを **Microsoft.SqlVirtualMachine** プロバイダーで選択します。 

   ![プロバイダーの変更](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>Azure CLI の場合
次のコード スニペットは、SQL VM リソース プロバイダーを Azure サブスクリプションに登録します。 

```azurecli-interactive
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>PowerShell の場合

次のコード スニペットでは、SQL リソース プロバイダーが自分の Azure サブスクリプションに登録されます。

```powershell-interactive
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>SQL Server VM を SQL リソース プロバイダーに登録する
SQL リソース プロバイダーがサブスクリプションに登録されたら、Azure CLI を使用して SQL Server VM をリソース プロバイダーに登録することができます。 

#### <a name="with-azure-cli"></a>Azure CLI の場合

Azure CLI で次のコード スニペットを使用して、SQL Server VM を登録します。 

```azurecli-interactive
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation> --license-type <AHUB or PAYG>
```

#### <a name="with-powershell"></a>PowerShell の場合
PowerShell で次のコード スニペットを使用して、SQL Server VM を登録します。

```powershell-interactive
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```



## <a name="known-errors"></a>既知のエラー

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>SQL IaaS 拡張機能が仮想マシンにインストールされていない
SQL IaaS 拡張機能は、SQL Server VM を SQL VM リソース プロバイダーに登録するために必要な前提条件です。 SQL IaaS 拡張機能をインストールする前に SQL Server VM を登録しようとすると、次のエラーが発生します。

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

この問題を解決するには、SQL Server VM の登録を試みる前に、SQL IaaS 拡張機能をインストールします。 

  > [!NOTE]
  > SQL IaaS 拡張機能をインストールすると SQL Server サービスが再起動されるため、メンテナンス期間中にのみ実行する必要があります。 詳細については、[SQL IaaS 拡張機能のインストール](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation)に関するページを参照してください。 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>リソース 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' が見つかりませんでした。 このオブジェクトにプロパティ 'sqlServerLicenseType' が見つかりません。 プロパティが存在し、設定可能であることを確認してください。
このエラーは、SQL Server VM で SQL リソース プロバイダーに登録されていないライセンス モデルを変更しようとしたときに発生します。 リソース プロバイダーを[サブスクリプション](#register-sql-vm-resource-provider-with-subscription)に登録した後、SQL Server VM を SQL [リソース プロバイダー](#register-sql-server-vm-with-sql-resource-provider)に登録する必要があります。 

### <a name="cannot-validate-argument-on-parameter-sku"></a>パラメーター 'Sku' の引数を検証できない
4\.0 より後の Azure PowerShell を使用している場合に、SQL Server VM のライセンス モデルを変更しようとすると、このエラーが発生する可能性があります。Set-AzResource :パラメーター 'Sku' の引数を検証できません。 この引数は null か空です。 null または空でない引数を指定して、コマンドを再度実行してください。
このエラーを解決するには、ライセンス モデルを切り替えるときに、前に説明した PowerShell コード スニペットの以下の行のコメントを解除します。

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Azure PowerShell のバージョンを確認するには、次のコードを使用します。

```powershell-interactive
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM における SQL Server に関するよくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM における SQL Server に関する料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM における SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


