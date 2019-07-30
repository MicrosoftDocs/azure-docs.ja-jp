---
title: Azure での SAP マルチ SID 構成の作成 | Microsoft Docs
description: Windows 仮想マシン上の高可用性 SAP NetWeaver マルチ SID の構成ガイド
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fddb0f7cceea167885f56cf6ff5e8639bec1d937
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710352"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>SAP NetWeaver マルチ SID 構成の作成

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

Azure 内部ロード バランサーを使用して複数の仮想 IP アドレスを管理できる機能が 2016 年 9 月にリリースされました。 この機能は、Azure 外部ロード バランサーに既に存在しているものです。

[Windows VM 上の SAP NetWeaver の高可用性ガイド][sap-ha-guide]に関するページで説明されているように、SAP をデプロイしている場合、SAP の ASCS/SCS の Windows クラスターの構成を作成するには内部ロード バランサーを使用することができます。

この記事では、追加の SAP ASCS/SCS クラスター化されたインスタンスを既存 Windows Server フェールオーバー クラスタリング (WSFC) クラスターにインストールすることによって、単一の ASCS/SCS インストールから、SAP マルチ SID 構成に移行する方法に焦点を当てます。 このプロレスが完了すると、SAP マルチ SID クラスターの構成が終了です。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件
[Windows VM 上の SAP NetWeaver の高可用性ガイド][sap-ha-guide]に関するページでの説明、およびこのダイアグラムに表示されているとおり、1 つの SAP ASCS/SCS インスタンスに使用される WSFC クラスターが構成済みである。

![高可用性の SAP ASCS/SCS インスタンス][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>ターゲット アーキテクチャ

次の例のように、複数の SAP ABAP ASCS または SAP Java SCS クラスター化インスタンスを同じ WSFC クラスター内にインストールすることが目標です。

![Azure の複数の SAP ASCS/SCS クラスター化されたインスタンス][sap-ha-guide-figure-6002]

> [!NOTE]
>1 つの Azure 内部ロード バランサーごとにプライベート フロントエンド IP の数に制限があります。
>
>1 つの WSFC クラスターにおける SAP ASCS/SCS インスタンスの最大数は、Azure 内部ロード バランサーあたりのプライベート フロントエンド IP の最大数と等しくなります。
>

ロード バランサーの制限の詳細については、[ネットワークの制限 -Azure Resource Manager][networking-limits-azure-resource-manager]。

2 つの高可用性 SAP システムを用いた場合の概要は次のようになります。

![2 つの SAP システム SID を使用した SAP 高可用性マルチ SID 設定][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> セットアップは次の条件を満たしている必要があります。
> - SAP ASCS/SCS インスタンスは同じ WSFC クラスターを共有している必要があります。
> - 各 DBMS SID は、独自の専用 WSFC クラスターを持っている必要があります。
> - 1 つの SAP システム SID に属する SAP アプリケーション サーバーは、独自の専用 VM を持っている必要があります。


## <a name="prepare-the-infrastructure"></a>インフラストラクチャの準備
インフラストラクチャの準備ために、次のパラメーターを持つ追加の SAP ASCS/SCS インスタンスをインストールできます。

| パラメーター名 | 値 |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| SAP DBMS の内部ロード バランサー | PR5 |
| SAP 仮想ホスト名 | pr5-sap-cl |
| SAP ASCS/SCS 仮想ホストの IP アドレス (追加の Azure Load Balancer IP アドレス) | 10.0.0.50 |
| SAP ASCS/SCS インスタンスの番号 | 50 |
| 追加の SAP ASCS/SCS インスタンスの ILB プローブ ポート | 62350 |

> [!NOTE]
> SAP ASCS/SCS のクラスター インスタンスの場合、IP アドレスごとに一意のプローブ ポートが必要です。 たとえば、Azure 内部ロード バランサー上の 1 つの IP アドレスでプローブ ポート 62300 が使用されている場合、そのロード バランサー上の他の IP アドレスではプローブ ポート 62300 を使用できません。
>
>今回の目的上、プローブ ポート 62300 は既に予約されているため、プローブ ポート 62350 を使用します。

ノードを 2 つ持つ既存の WSFC クラスターに追加の SAP ASCS/SCS インスタンスをインストールすることができます。

| 仮想マシンの役割 | 仮想マシンのホスト名 | 静的 IP アドレス |
| --- | --- | --- |
| ASCS/SCS インスタンスの最初のクラスター ノード |pr1-ascs-0 |10.0.0.10 |
| ASCS/SCS インスタンスの 2 番目のクラスター ノード |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>DNS サーバーでのクラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成

次のパラメーターを使用して、ASCS/SCS インスタンスの仮想ホスト名の DNS エントリを作成することができます。

| 新しい SAP ASCS/SCS 仮想ホスト名 | 関連付けられる IP アドレス |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

次のスクリーンショットのとおり、新しいホスト名と IP アドレスが DNS Manager に表示されます。

![新しい SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの定義された DNS エントリを強調表示する DNS Manager リスト][sap-ha-guide-figure-6004]

DNS エントリを作成する手順は、メインの [Windows VM 上の SAP NetWeaver の高可用性ガイド][sap-ha-guide-9.1.1]にも記載されています。

> [!NOTE]
> 追加の ASCS/SCS インスタンスの仮想ホスト名に割り当てる新しい IP アドレスは SAP Azure Load Balancer に割り当てた新しい IP アドレスと同じである必要があります。
>
>このシナリオでは、IP アドレスは 10.0.0.50 です。

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>PowerShell を使用して既存の Azure 内部ロード バランサーに IP アドレスを追加する

同じ WSFC クラスターに複数の SAP ASCS/SCS インスタンスを作成するには、PowerShell を使用して既存の Azure 内部ロード バランサーに IP アドレスを追加します。 各 IP アドレスには、独自の負荷分散規則、プローブ ポート、フロントエンド IP プール、バックエンド プールが必要です。

次のスクリプトは、既存のロード バランサーに新しい IP アドレスを追加します。 環境に合わせて PowerShell 変数を更新してください。 スクリプトでは、すべての SAP ASCS/SCS ポートに必要なすべての負荷分散規則が作成されます。

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
スクリプトが実行された後は、次のスクリーンショットのように、Azure Portal で結果を確認できます。

![Azure Portal の新しいフロントエンド IP プール][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>クラスター マシンにディスクを追加して SIOS クラスター共有ディスクを構成する

追加する各 SAP ASCS/SCS インスタンスには新しいクラスター共有ディスクを追加する必要があります。 Windows Server 2012 R2 WSFC で現在使用されているクラスター共有ディスクは、SIOS DataKeeper ソフトウェア ソリューションです。

以下の手順を実行します。
1. 各クラスター ノードに追加のディスク、または同じサイズのディスク (ストライピングが必要なもの) を追加してフォーマットします。
2. SIOS DataKeeper でストレージのレプリケーションを構成します。

この手順では、WSFC クラスター マシン上に SIOS Datakeeper が既にインストールされていることを前提とします。 インストールしている場合は、それらのマシン間でのレプリケーションを構成する必要があります。 手順は、メインの [Windows VM 上の SAP NetWeaver の高可用性ガイド][sap-ha-guide-8.12.3.3]に記載されています。  

![新しい SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリング][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>SAP アプリケーション サーバーと DBMS クラスターの仮想マシンのデプロイ

2 番目の SAP システムのインフラストラクチャの準備を完了するには、次の操作をします。

1. SAP アプリケーション サーバーに専用の VM をデプロイし、独自の専用高可用性グループに配置する。
2. DBMS クラスターに専用の VM をデプロイし、独自の専用高可用性グループに配置する。


## <a name="install-the-second-sap-sid2-netweaver-system"></a>2 番目の SAP SID2 NetWeaver システムをインストールする

2 番目の SAP SID2 システムをインストールするための完全な手順は、メインの [Windows VM 上の SAP NetWeaver の高可用性ガイド][sap-ha-guide-9]に記載されています。

おおまかな手順は次のとおりです。

1. [最初の SAP クラスター ノードをインストールします][sap-ha-guide-9.1.2]。  
 このステップでは、**EXISTING WSFC クラスター ノード 1** に、高可用性 ASCS/SCS インスタンスを使用した SAP をインストールします。

2. [ASCS/SCS インスタンスの SAP プロファイルを変更します][sap-ha-guide-9.1.3]。

3. [プローブ ポートを構成します][sap-ha-guide-9.1.4]。  
 このステップではPowerShell を使用して、SAP クラスター リソース SAP-SID2-IP プローブ ポートを構成します。 この構成は、SAP ASCS/SCS クラスター ノードのいずれかで実行します。

4. [データベース インスタンスのインストール][sap-ha-guide-9.2]  
 このステップでは、専用の WSFC クラスターに DBMS をインストールします。

5. [第 2 のクラスター ノードのインストール][sap-ha-guide-9.3]  
 このステップでは、既存の WSFC クラスター ノード 2 に、高可用性 ASCS/SCS インスタンスを使用した SAP をインストールします。

6. SAP ASCS/SCS のインスタンスと ProbePort の Windows ファイアウォール ポートを開く  
 SAP ASCS/SCS インスタンスで使用する両方のクラスター ノードで、SAP ASCS/SCS ポートが使用するすべての Windows ファイアウォール ポートを開きます。 これらのポートのリストは、メインの [Windows VM 上の SAP NetWeaver の高可用性ガイド][sap-ha-guide-8.8]に記載されています。  
 また、Azure 内部ロード バランサー プローブ ポート (このシナリオでは 62350) を開きます。

7. [SAP ERS Windows サービスのインスタンスのスタートアップの種類を変更します][sap-ha-guide-9.4]。

8. 新しい専用 VM で [SAP プライマリ アプリケーション サーバーをインストールします][sap-ha-guide-9.5]。

9. 新しい専用 VM で [SAP 追加アプリケーション サーバーをインストールします][sap-ha-guide-9.6]。

10. [SAP ASCS/SCS インスタンスのフェールオーバーと SIOS レプリケーションをテストします][sap-ha-guide-10]。

## <a name="next-steps"></a>次の手順

- [ネットワークの制限:Azure Resource Manager][networking-limits-azure-resource-manager]
- [Azure Load Balancer の複数 VIP][load-balancer-multivip-overview]
- [Windows VM 上の SAP NetWeaver の高可用性ガイド][sap-ha-guide]
