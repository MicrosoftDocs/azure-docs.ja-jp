---
title: Azure の SQL Server 仮想マシンを SQL VM リソースプロバイダーに登録する | Microsoft Docs
description: SQL Server VM を SQL VM リソースプロバイダーに登録して管理性を向上させます。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: beef423d11312f83c988ad15655ec852c6fe7b00
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797996"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Azure の SQL Server 仮想マシンを SQL VM リソースプロバイダーに登録する

この記事では、Azure SQL Server 仮想マシン (VM) を SQL VM リソースプロバイダーに登録する方法について説明します。 

Azure portal を介して SQL Server VM マーケットプレース イメージをデプロイすると、自動的に SQL Server VM がリソースプロバイダーに登録されます。 ただし、Azure Marketplace からイメージを選択するのではなく、SQL Server を Azure 仮想マシンにセルフインストールすることを選択した場合は、以下の目的で、SQL Server VM をすぐにリソースプロバイダーに登録する必要があります。

-  **コンプライアンス** - Microsoft 製品の利用規約に従って、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用されるお客様は、いつ Azure ハイブリッド特典を利用するかを Microsoft に知らせる必要があり、そのために SQL VM リソースプロバイダーへの登録を行わなければなりません。 

-  **機能面の利点** - SQL Server VM をリソースプロバイダーに登録すると、[自動修正](virtual-machines-windows-sql-automated-patching.md)、[自動バックアップ](virtual-machines-windows-sql-automated-backup-v2.md)、監視および管理性機能が利用できるようになるほか、[ライセンス](virtual-machines-windows-sql-ahb.md)や[エディション](virtual-machines-windows-sql-change-edition.md)を柔軟に管理できるようになります。 これらの機能は、以前は Azure Marketplace の SQL Server VM イメージでしか利用できませんでした。

SQL Server を Azure VM にセルフインストールするか、SQL Server を備えたカスタム VHD から Azure VM をプロビジョニングすれば、SQL Server 向け Azure ハイブリッド特典を通じ、お客様がその利用を SQL VM リソースプロバイダーへの登録によって Microsoft に知らせるという条件を満たせます。 

SQL VM リソースプロバイダーを利用するには、SQL VM リソースプロバイダーをサブスクリプションに登録することも必要です。 これは、Azure portal、Azure CLI、および PowerShell で行えます。 

## <a name="prerequisites"></a>前提条件

SQL Server VM をリソースプロバイダーに登録するには、以下のものが必要になります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli) と [PowerShell](/powershell/azure/new-azureps-module-az)。 

## <a name="register-with-sql-vm-resource-provider"></a>SQL VM リソースプロバイダーに登録する
[SQL IaaS 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)が既に VM にインストールされている場合、SQL VM リソースプロバイダーに登録すると、Microsoft.SqlVirtualMachine または SqlVirtualMachines 型のメタデータ リソースが作成されます。 以下は、SQL IaaS 拡張機能が既に VM にインストールされている場合に SQL VM リソースプロバイダーへの登録を行うコード スニペットです。 SQL VM リソースプロバイダーへの登録時に必要な SQL Server ライセンスの種類として "PAYG" または "AHUB" を指定する必要があります。 

PowerShell で次のコード スニペットを使用して、SQL Server VM を登録します。

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenceType='AHUB'}  
  
  ```

SQL IaaS 拡張機能が VM にインストールされていない場合は、軽量 SQL 管理モードを指定することによって SQL VM リソースプロバイダーに登録できます。 軽量 SQL 管理モードでは、SQL VM リソースプロバイダーによって SQL IaaS 拡張機能が[軽量モード](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)で自動インストールされ、SQL Server インスタンス メタデータが検証されます。その際、SQL Server サービスの再起動は行われません。 SQL VM リソースプロバイダーへの登録時に必要な SQL Server ライセンスの種類として "PAYG" または "AHUB" を指定する必要があります。 

PowerShell で次のコード スニペットを使用して、SQL Server VM を軽量 SQL 管理モードで登録します。

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenceType='AHUB';sqlManagement='LightWeight'}  
  
  ```

[軽量モード](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)で SQL VM リソースプロバイダーに登録することにより、コンプライアンスが確保され、柔軟なライセンス管理と SQL Server エディションのインプレース更新が可能になります。 フェールオーバー クラスター インスタンスとマルチインスタンス デプロイは、軽量モードでのみ SQL VM リソースプロバイダーに登録できます。 Azure portal で見つかる手順に従って[フル モード](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation)にアップグレードすれば、いつでも SQL Server を再起動して包括的な管理性機能を有効にすることができます。 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Windows Server 2008 VM 上の SQL Server 2008 および 2008 R2 を登録する

Windows Server 2008 にインストールされている SQL Server 2008 および 2008 R2 は、[NoAgent](virtual-machines-windows-sql-server-agent-extension.md) モードで SQL VM リソースプロバイダーに登録できます。 このオプションにより、コンプライアンスが確保され、SQL Server VM を Azure portal の限られた機能で監視できるようになります。

次の表では、登録時に指定するパラメーターに使用可能な値を詳細に示しています。

| パラメーター | 使用可能な値                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'` または `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` または `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Windows Server 2008 インスタンス上の SQL Server 2008 または 2008 R2 を登録するには、次の Powershell コード スニペットを使用します。  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenceType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>登録状態を確認する
SQL Server が既に SQL VM リソースプロバイダーに登録されているかどうかは、Azure portal、Azure CLI、または PowerShell を使用して確認できます。 

### <a name="azure-portal"></a>Azure Portal 
Azure portal を使用して登録の状態を確認するには、以下を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. [SQL 仮想マシン](virtual-machines-windows-sql-manage-portal.md)に移動します。
1. 一覧から SQL Server VM を選択します。 SQL Server VM がここに表示されていない場合、その SQL Server VM は SQL VM リソースプロバイダーに登録されていない可能性があります。 
1. [`Status`] の値を確認します。 `Status = Succeeded` の場合は、SQL Server VM が SQL VM リソースプロバイダーに正常に登録されています。 

    ![SQL RP 登録による状態を確認する](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>AZ CLI

次の AZ CLI コマンドを使用して現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

次の PowerShell コマンドレットを使用して現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
エラーは、SQL Server VM がリソースプロバイダーに登録されていないことを示します。 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>SQL VM リソース プロバイダーをサブスクリプションに登録する 

自分の SQL Server VM を SQL VM リソースプロバイダーに登録するには、そのリソースプロバイダーを自分のサブスクリプションに登録する必要があります。 これは、Azure portal または Azure CLI を使用して行うことができます。

### <a name="azure-portal"></a>Azure ポータル

以下の手順では、Azure portal を使用して SQL VM リソースプロバイダーを Azure サブスクリプションに登録します。 

1. Azure portal を開き、**すべてのサービス**に移動します。 
1. **サブスクリプション**に移動し、関心のあるサブスクリプションを選択します。  
1. **[サブスクリプション]** ページで、 **[リソースプロバイダー]** に移動します。 
1. フィルター内に`sql`と入力し、SQL 関連のリソース プロバイダーを表示します。 
1. 目的となるアクションに応じて、*登録*、*再登録*、または*登録解除*のいずれかを **Microsoft.SqlVirtualMachine** プロバイダーで選択します。 

   ![プロバイダーの変更](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>AZ CLI
次のコード スニペットは、SQL VM リソース プロバイダーを Azure サブスクリプションに登録します。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

次の PowerShell コード スニペットでは、SQL VM リソースプロバイダーを Azure サブスクリプションに登録します。

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>解説

 - SQL VM リソースプロバイダーでは、"Resource Manager" を使用してデプロイされた SQL Server VM のみがサポートされます。 "クラシック モデル" を使用してデプロイされた SQL Server VM はサポートされません。 
 - SQL VM リソースプロバイダーでは、パブリック クラウドにデプロイされた SQL Server VM のみがサポートされます。 プライベート クラウドや政府機関向けクラウドにデプロイされている場合はサポートされません。 

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM における SQL Server に関するよくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM における SQL Server に関する料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM における SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)


