---
title: SQL Managed Instance 仮想クラスター上で仮想ネットワーク DNS サーバーの設定を同期する
description: SQL Managed Instance 仮想クラスター上で仮想ネットワーク DNS サーバーの設定を同期する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b95afe513dba2f1da9556b27ec17bcccc9fe88e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173553"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>SQL Managed Instance 仮想クラスター上で仮想ネットワーク DNS サーバーの設定を同期する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、SQL Managed Instance 仮想クラスター上で仮想ネットワーク DNS サーバーの設定を同期するタイミングと方法について説明します。

## <a name="when-to-synchronize-the-dns-setting"></a>DNS 設定を同期するタイミング

プライベート ホスト名を SQL Managed Instance から解決する必要のあるシナリオ (データベース メール、クラウドまたはハイブリッド環境内の他の SQL Server インスタンスにリンクされたサーバーなど) がいくつか存在します。 この場合は、Azure の内部でカスタム DNS を構成する必要があります。 詳細については、「[Azure SQL Managed Instance のカスタム DNS を構成する](custom-dns-configure.md)」を参照してください。

Managed Instance をホストする[仮想クラスター](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)の作成後にこの変更を実装する場合は、仮想ネットワーク構成を使用して仮想クラスター上で DNS サーバーの設定を同期する必要があります。

> [!IMPORTANT]
> DNS サーバーの設定を同期すると、仮想クラスター内でホストされているすべての Managed Instances が影響を受けます。

## <a name="how-to-synchronize-the-dns-setting"></a>DNS 設定を同期する方法

### <a name="azure-rbac-permissions-required"></a>必要な Azure RBAC アクセス許可

DNS サーバー構成を同期するユーザーには、次のいずれかの Azure ロールが必要です。

- サブスクリプションの所有者ロール、または
- マネージド インスタンス共同作成者ロール、または
- 次のアクセス許可を持つカスタム ロール:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Azure PowerShell の使用

DNS サーバーの設定が更新された仮想ネットワークを取得します。

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
サブネット内のすべての仮想クラスターの DNS サーバー構成を同期するには、PowerShell コマンド [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) を使用します。

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Azure CLI の使用

DNS サーバーの設定が更新された仮想ネットワークを取得します。

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

サブネット内のすべての仮想クラスターの DNS サーバー構成を同期するには、Azure CLI コマンド [az resource invoke-action](/cli/azure/resource#az_resource_invoke_action) を使用します。

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>次のステップ

- カスタム DNS を構成する方法の詳細については、「[Azure SQL Managed Instance のカスタム DNS を構成する](custom-dns-configure.md)」を参照してください。
- 概要については、「[Azure SQL Managed Instance とは](sql-managed-instance-paas-overview.md)」を参照してください。
