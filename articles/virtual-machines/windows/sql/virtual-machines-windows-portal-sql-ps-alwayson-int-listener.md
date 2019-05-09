---
title: AlwaysOn 可用性グループ リスナーの構成 – Microsoft Azure | Microsoft Docs
description: 1 つ以上の IP アドレスを持つ内部ロード バランサーを使用して、Azure Resouce Manager モデルで可用性グループ リスナーを構成します。
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.openlocfilehash: 5b647af7925ceb81c524deb0accf90f9e895080e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58876991"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>1 つ以上の AlwaysOn 可用性グループ リスナーの構成 - Resource Manager
このトピックでは、以下のことを行う方法を示します。

* PowerShell コマンドレットを使用して、SQL Server 可用性グループ用の内部ロード バランサーを作成します。
* 複数の可用性グループをサポートするために、ロード バランサーに追加の IP アドレスを追加します。 

可用性グループ リスナーは、クライアントがデータベース アクセスのために接続する仮想ネットワーク名です。 Azure 仮想マシンでは、ロード バランサーによってリスナーの IP アドレスが保持されます。 ロード バランサーは、プローブ ポートでリッスンしている SQL Server のインスタンスにトラフィックをルーティングします。 通常、可用性グループでは内部ロード バランサーが使用されます。 Azure 内部ロード バランサーは、1 つ以上の IP アドレスをホストできます。 各 IP アドレスで、特定のプローブ ポートが使用されます。 このドキュメントでは、PowerShell を使用してロード バランサーを作成したり、SQL Server 可用性グループ用の既存のロード バランサーに IP アドレスを追加したりする方法を説明します。 

内部ロード バランサーに複数の IP アドレスを割り当てる機能は Azure の新機能であり、Resource Manager モデルでのみ利用できます。 この作業を行うには、SQL Server 可用性グループが Resource Manager モデルの Azure 仮想マシンにデプロイされている必要があります。 両方の SQL Server 仮想マシンが同じ可用性セットに属している必要があります。 [Microsoft のテンプレート](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) を使用すると、Azure Resource Manager で自動的に可用性グループを作成することができます。 このテンプレートでは、内部ロード バランサーを含む可用性グループが自動的に作成されます。 必要に応じて [AlwaysOn 可用性グループを手動で構成](virtual-machines-windows-portal-sql-availability-group-tutorial.md)することもできます。

このトピックは、可用性グループの構成が既に済んでいることを前提としています。  

関連トピック:

* [Azure VM での AlwaysOn 可用性グループの構成 (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Azure リソース マネージャーと PowerShell を使用した VNet 間の接続の構成](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>PowerShell のバージョンを確認する

この記事の例は、Azure PowerShell モジュールのバージョン 5.4.1 を使用してテストされています。

お使いの PowerShell モジュールが 5.4.1 以降であることを確認してください。

[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)に関するページを参照してください。

## <a name="configure-the-windows-firewall"></a>Windows ファイアウォールの構成

SQL Server のアクセスを許可するように Windows ファイアウォールを構成します。 ファイアウォール規則では、SQL Server インスタンスによって使用されるポートとリスナー プローブによって使用されるポートへの TCP 接続を許可します。 詳細については、「[データベース エンジン アクセスを有効にするための Windows ファイアウォールを構成する](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1)」をご覧ください。 SQL Server のポートとプローブ ポート用の受信規則を作成します。

Azure ネットワーク セキュリティ グループを使用してアクセスを制限する場合は、バックエンド SQL Server VM の IP アドレス、AG リスナーのロード バランサー フローティング IP アドレス、および該当する場合はクラスター コア IP アドレスが許可ルールに含まれていることを確認します。

## <a name="determine-the-load-balancer-sku-required"></a>必要なロード バランサーの SKU を決定する

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) は、2 つの SKU (Basic と Standard) で利用できます。 Standard Load Balancer をお勧めします。 仮想マシンが可用性セット内にある場合は、Basic Load Balancer が許可されます。 Standard Load Balancer では、すべての VM IP アドレスで標準 IP アドレスが使用されている必要があります。

可用性グループ用の現在の [Microsoft テンプレート](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)では、Basic Load Balancer と基本の IP アドレスが使用されています。

この記事の例では、Standard Load Balancer が指定されています。 例のスクリプトには、`-sku Standard` が含まれています。

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Basic Load Balancer を作成するには、ロード バランサーを作成する行から `-sku Standard` を削除します。 例: 

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>サンプル スクリプト: PowerShell を使用して内部ロード バランサーを作成する

> [!NOTE]
> [Microsoft テンプレート](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) を使用して可用性グループを作成した場合、内部ロード バランサーは既に作成されています。

次の PowerShell スクリプトでは、内部ロード バランサーを作成し、負荷分散規則を構成した後、ロード バランサーの IP アドレスを設定します。 スクリプトを実行するには、Windows PowerShell ISE を開いてスクリプト ウィンドウにスクリプトを貼り付けます。 `Connect-AzAccount` を使用して PowerShell にログインします。 複数の Azure サブスクリプションがある場合は、 `Select-AzSubscription` を使用してサブスクリプションを設定します。 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a> サンプル スクリプト: PowerShell を使用して既存のロード バランサーに IP アドレスを追加する
複数の可用性グループを使用するには、ロード バランサーに追加の IP アドレスを追加します。 各 IP アドレスには、独自の負荷分散規則、プローブ ポート、およびフロント ポートが必要です。

フロントエンド ポートは、アプリケーションが SQL Server インスタンスに接続するために使用するポートです。 複数の異なる可用性グループの IP アドレスで同じフロントエンド ポートを使用できます。

> [!NOTE]
> SQL Server 可用性グループの場合は、各 IP アドレスに特定のプローブ ポートが必要です。 たとえば、ロード バランサー上の 1 つの IP アドレスでプローブ ポート 59999 が使用されている場合、そのロード バランサー上の他の IP アドレスではプローブ ポート 59999 を使用できません。

* ロード バランサーの制限については、「**ネットワークの制限 - Azure Resource Manager**」にある「[ロード バランサーごとのプライベート フロント エンド IP](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)」をご覧ください。
* 可用性グループの制限については、「[制限 (可用性グループ)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG)」をご覧ください。

次のスクリプトは、既存のロード バランサーに新しい IP アドレスを追加します。 ILB では、負荷分散フロントエンド ポート用のリスナー ポートが使用されます。 このポートには、SQL Server がリッスンしているポートを使用できます。 SQL Server の既定のインスタンスでは、このポートは 1433 です。 バックエンド ポートがフロントエンド ポートと同じになるように、可用性グループの負荷分散規則ではフローティング IP (Direct Server Return) が必要です。 環境に合わせて変数を更新してください。 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>リスナーの構成

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>SQL Server Management Studio でリスナー ポートを設定する

1. SQL Server Management Studio を起動し、プライマリ レプリカに接続します。

1. **[AlwaysOn 高可用性]** | 、**[可用性グループ]** | 、**[可用性グループ リスナー]** の順に移動します。 

1. フェールオーバー クラスター マネージャーで作成したリスナー名が表示されます。 リスナー名を右クリックし、 **[プロパティ]** をクリックします。

1. **[ポート]** ボックスで、以前に使用した $EndpointPort (既定値は 1433) を使用し、可用性グループ リスナーのポート番号を指定して、**[OK]** をクリックします。

## <a name="test-the-connection-to-the-listener"></a>リスナーへの接続をテストする

接続をテストするには、次の手順に従います。

1. 同じ仮想ネットワークに存在する、レプリカを所有していない SQL Server に RDP で接続します。 クラスター内の別の SQL Server がそれに該当します。

1. **sqlcmd** ユーティリティを使用して接続をテストします。 たとえば次のスクリプトは、Windows 認証を使用し、リスナー経由でプライマリ レプリカとの **sqlcmd** 接続を確立しています。
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    リスナーが既定のポート (1433) 以外のポートを使用している場合は、そのポートを接続文字列で指定します。 たとえば、次の sqlcmd コマンドは、ポート 1435 でリスナーに接続します。 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD 接続では、プライマリ レプリカをホストしている SQL Server インスタンスに対して自動的に接続されます。 

> [!NOTE]
> 指定したポートは、両方の SQL Server のファイアウォールで必ず開放してください。 使用する TCP ポートに対する入力方向の規則が両方のサーバーに必要となります。 詳細については、「 [ファイアウォール規則を追加または編集する](https://technet.microsoft.com/library/cc753558.aspx) 」を参照してください。 
> 
> 

## <a name="guidelines-and-limitations"></a>ガイドラインと制限
内部ロード バランサーを使用した Azure の可用性グループ リスナーに関する次のガイドラインを確認してください。

* 内部ロード バランサーでは、同じ仮想ネットワーク内からしかリスナーにアクセスできません。

* Azure ネットワーク セキュリティ グループを使用してアクセスを制限する場合は、バックエンド SQL Server VM の IP アドレス、AG リスナーのロード バランサー フローティング IP アドレス、および該当する場合はクラスター コア IP アドレスが許可ルールに含まれていることを確認します。

## <a name="for-more-information"></a>BLOB の詳細
詳細については、「[Azure VM での AlwaysOn 可用性グループの手動構成](virtual-machines-windows-portal-sql-availability-group-tutorial.md)」をご覧ください。

## <a name="powershell-cmdlets"></a>PowerShell コマンドレット
Azure 仮想マシン用の内部ロード バランサーを作成するには、次の PowerShell コマンドレットを使用します。

* [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) は、ロード バランサーを作成します。 
* [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) は、ロード バランサー用のフロントエンド IP 構成を作成します。 
* [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) は、ロード バランサー用の規則の構成を作成します。 
* [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) は、ロード バランサー用のバックエンド アドレス プール構成を作成します。 
* [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) は、ロード バランサー用のプローブの構成を作成します。
* [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) は、Azure リソース グループからロード バランサーを削除します。
