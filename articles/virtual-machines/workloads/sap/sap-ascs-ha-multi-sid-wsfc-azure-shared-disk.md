---
title: WSFC と Azure 共有ディスクを使用した SAP ASCS/SCS のマルチ SID HA | Microsoft Docs
description: WSFC と Azure 共有ディスクを使用した SAP ASCS/SCS インスタンスのマルチ SID 高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cef0630cfc7ec7d080073085e577153ae7a47ecf
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504458"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>Windows Server フェールオーバー クラスタリングと Azure 共有ディスクを使用した SAP ASCS/SCS インスタンスのマルチ SID 高可用性

> ![Windows OS][Logo_Windows] Windows
>

この記事では、追加の SAP ASCS/SCS クラスター化インスタンスを、Azure 共有ディスクを持つ既存の Windows Server フェールオーバー クラスタリング (WSFC) クラスターにインストールすることによって、単一の ASCS/SCS インストールから SAP マルチ SID 構成に移行する方法について説明します。 このプロセスが完了したら、SAP マルチ SID クラスターの構成は完了です。

## <a name="prerequisites-and-limitations"></a>前提条件と制限事項

現時点では、SAP ASCS/SCS インスタンスの Azure 共有ディスクとして Azure Premium SSD ディスクを使用できます。 次の制限事項が適用されます。

-  [Azure Ultra Disk](../../disks-types.md#ultra-disk) は、SAP ワークロード用の Azure 共有ディスクとしてはサポートされていません。 現時点では、Azure Ultra Disk を可用性セット内で使用して、Azure VM を配置することはできません
-  Premium SSD ディスクを使用した [Azure 共有ディスク](../../disks-shared.md)は、可用性セット内の VM でのみサポートされています。 Availability Zones のデプロイではサポートされていません。 
-  Azure 共有ディスクの値 [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) によって、その共有ディスクを使用できるクラスター ノードの数が決まります。 通常、SAP ASCS/SCS インスタンスには、Windows フェールオーバー クラスターに 2 つのノードを構成するため、`maxShares` の値は 2 に設定する必要があります。
-  すべての SAP ASCS/SCS クラスター VM が、同じ [Azure 近接配置グループ](../../windows/proximity-placement-groups.md)にデプロイされる必要があります。   
   Windows クラスター VM を、PPG を使用せずに Azure 共有ディスクがある可用性セット内にデプロイすることはできますが、PPG を使用すると Azure 共有ディスクとクラスター VM の物理的近距離を確保できるため、VM とストレージ層の間の待機時間が短くなります。    

Azure 共有ディスクの制限事項の詳細については、Azure 共有ディスクのドキュメントの「[制限事項](../../disks-shared.md#limitations)」セクションを参照してください。  

> [!IMPORTANT]
> Azure 共有ディスクを使用して SAP ASCS/SCS Windows フェールオーバー クラスターをデプロイする場合は、デプロイが 1 つの記憶域クラスター内の単一の共有ディスクを使用して動作することに注意してください。 SAP ASCS/SCS インスタンスは、Azure 共有ディスクがデプロイされている記憶域クラスターで問題が発生した場合に影響を受けます。  

> [!IMPORTANT]
> セットアップは次の条件を満たしている必要があります。
> * 各データベース管理システム (DBMS) SID には、独自の専用 WSFC クラスターが必要です。  
> * 1 つの SAP システム SID に属する SAP アプリケーション サーバーは、独自の専用 VM を持っている必要があります。  
> * エンキュー レプリケーション サーバー 1 とエンキュー レプリケーション サーバー 2 を同じクラスター内に混在させることは、サポートされていません。  


## <a name="supported-os-versions"></a>サポートされている OS のバージョン

Windows Server 2016 と Windows Server 2019 の両方がサポートされています (最新のデータ センター イメージを使用してください)。

次の理由から、**Windows Server 2019 Datacenter** の使用を強くお勧めします。
- Windows 2019 フェールオーバー クラスター サービスは Azure に対応しています
- Azure ホスト メンテナンスの統合と認識が追加され、Azure のスケジュール イベントを監視することでエクスペリエンスが向上しました。
- 分散ネットワーク名を使用することができます (これは既定のオプションです)。 そのため、クラスター ネットワーク名に専用の IP アドレスを設定する必要がありません。 また、この IP アドレスを Azure 内部ロード バランサーで構成する必要もありません。 

## <a name="architecture"></a>アーキテクチャ

マルチ SID 構成では、エンキュー レプリケーション サーバー 1 (ERS1) とエンキュー レプリケーション サーバー 2 (ERS2) の両方がサポートされています。  ERS1 と ERS2 を同じクラスターに混在させることは、サポートされていません。 

1. 最初の例には 2 つの SAP SID が示され、どちらも、次の ERS1 アーキテクチャで使用されます。

   - SAP SID1 は共有ディスクにデプロイされ、ERS1 で使用されます。 この ERS インスタンスは、ローカル ホストとローカル ドライブにインストールされます。
     SAP SID1 には独自の (仮想) IP アドレス (SID1 (A)SCS IP1) が含まれ、これは Azure 内部ロード バランサー上に構成されます。

   - SAP SID2 は共有ディスクにデプロイされ、ERS1 で使用されます。 この ERS インスタンスは、ローカル ホストとローカル ドライブにインストールされます。
     SAP SID2 には独自の (仮想) IP アドレス (SID2 (A)SCS IP2) が含まれ、これも Azure 内部ロード バランサー上で構成されます。

![高可用性の SAP ASCS/SCS インスタンス - ERS1 構成で使用される 2 つのインスタンス][sap-ha-guide-figure-6007]

2. 2 番目の例には 2 つの SAP SID が示され、どちらも、次の ERS2 アーキテクチャで使用されます。 

   - SAP SID1 は、ERS2 で使用され、これもクラスター化され、ローカル ドライブにデプロイされます。  
     SAP SID1 には独自の (仮想) IP アドレス (SID1 (A)SCS IP1) が含まれ、これは Azure 内部ロード バランサー上で構成されます。
     SAP SID1 システムによって使用さるれ SAP ERS2 には独自の (仮想) IP アドレス (SID1 ERS2 IP2) が含まれ、これは Azure 内部ロード バランサー上で構成されます。

   - SAP SID2 は、ERS2 で使用され、これもクラスター化され、ローカル ドライブにデプロイされます。  
     SAP SID2 には独自の (仮想) IP アドレス (SID2 (A)SCS IP3) が含まれ、これは Azure 内部ロード バランサー上で構成されます。
     SAP SID2 システムによって使用される SAP ERS2 には独自の (仮想) IP アドレス (SID2 ERS2 IP4) が含まれ、これは Azure 内部ロード バランサー上で構成されます。

   これで、仮想 IP アドレスが合計 4 つになります。  
   - SID1 (A)SCS IP1
   - SID2 ERS2 IP2
   - SID2 (A)SCS IP3
   - SID2 ERS2 IP4

![高可用性の SAP ASCS/SCS インスタンス - ERS1 および ERS2 構成で使用される 2 つのインスタンス][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>インフラストラクチャの事前準備

**既存のクラスター化された** SAP **PR1** ASCS/SCS インスタンスに加えて、新しい SAP SID **PR2** をインストールします。  

### <a name="host-names-and-ip-addresses"></a>ホスト名と IP アドレス

| ホスト名の役割 | ホスト名 | 静的 IP アドレス | 可用性セット | 近接配置グループ |
| --- | --- | --- |---| ---|
| 最初のクラスター ノードの ASCS/SCS クラスター |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| 2 番目のクラスター ノードの ASCS/SCS クラスター |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| クラスター ネットワーク名 | pr1clust |10.0.0.42 (Win 2016 クラスターの場合 **のみ**) | 該当なし | 該当なし |
| **SID1** ASCS クラスターのネットワーク名 | pr1-ascscl |10.0.0.43 | 該当なし | 該当なし |
| **SID1** ERS クラスターのネットワーク名 (ERS2 の場合 **のみ**) | pr1-erscl |10.0.0.44 | 該当なし | 該当なし |
| **SID2** ASCS クラスターのネットワーク名 | pr2-ascscl |10.0.0.45 | 該当なし | 該当なし |
| **SID2** ERS クラスターのネットワーク名 (ERS2 の場合 **のみ**) | pr1-erscl |10.0.0.46 | 該当なし | 該当なし |

### <a name="create-azure-internal-load-balancer"></a>Azure 内部ロード バランサーを作成する

SAP ASCS、SAP SCS、および新しい SAP ERS2 により、仮想ホスト名と仮想 IP アドレスが使用されます。 Azure 上で仮想 IP アドレスを使用するには、[ロード バランサー](../../../load-balancer/load-balancer-overview.md)が必要です。 [Standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) の使用を強くお勧めします。 

2 番目の SAP SID ASCS/SCS/ERS インスタンス **PR2** について、既存のロード バランサーに構成を追加する必要があります。 1 番目の SAP SID **PR1** の構成が既に準備されている必要があります。  

**(A)SCS PR2 [インスタンス番号 02]**
- フロントエンドの構成
    - 静的 ASCS/SCS IP アドレス **10.0.0.45**
- バックエンドの構成  
    既に準備済み - SAP SID **PR1** の構成中に、VM が既にバックエンド プールに追加されています
- プローブ ポート
    - ポート 620 **nr** **[62002]** プロトコル (TCP)、間隔 (5)、異常しきい値 (2) の既定のオプションのままにします
- 負荷分散規則
    - Standard Load Balancer を使用する場合は、 [HA ポート] を選択します
    - Basic Load Balancer を使用する場合は、次のポートの負荷分散規則を作成します
        - 32 **nr** TCP **[3202]**
        - 36 **nr** TCP **[3602]**
        - 39 **nr** TCP **[3902]**
        - 81 **nr** TCP **[8102]**
        - 5 **nr** 13 TCP **[50213]**
        - 5 **nr** 14 TCP **[50214]**
        - 5 **nr** 16 TCP **[50216]**
        - **PR2** ASCS フロントエンド IP、正常性プローブ、および既存のバックエンド プールに関連付けます。  

    - [アイドルタイムアウト (分)] が最大値の 30 に設定され、[フローティング IP (ダイレクト サーバー リターン)] が有効になっていることを確認します。

**ERS2 PR2 [インスタンス番号 12]** 

エンキュー レプリケーション サーバー 2 (ERS2) もクラスター化されているため、上記の SAP ASCS/SCS IP に加え、ERS2 仮想 IP アドレスも Azure ILB で構成する必要があります。 このセクションは、**PR2** にエンキュー レプリケーション サーバー 2 のアーキテクチャを使用している場合にのみ適用されます。  
- 新しいフロントエンドの構成
    - 静的 SAP ERS2 IP アドレス **10.0.0.46**

- バックエンドの構成  
  VM は ILB バックエンド プールに既に追加されています。  

- 新しいプローブ ポート
    - ポート 621 **nr** **[62112]** プロトコル (TCP)、間隔 (5)、異常しきい値 (2) の既定のオプションのままにします

- 新しい負荷分散規則
    - Standard Load Balancer を使用する場合は、 [HA ポート] を選択します
    - Basic Load Balancer を使用する場合は、次のポートの負荷分散規則を作成します
        - 32 **nr** TCP **[3212]**
        - 33 **nr** TCP **[3312]**
        - 5 **nr** 13 TCP **[51212]**
        - 5 **nr** 14 TCP **[51212]**
        - 5 **nr** 16 TCP **[51212]**
        - **PR2** ERS2 フロントエンド IP、正常性プローブ、および既存のバックエンド プールに関連付けます。  

    - [アイドルタイムアウト (分)] が最大値 (例: 30) に設定され、[フローティング IP (ダイレクト サーバー リターン)] が有効になっていることを確認します。


### <a name="create-and-attach-second-azure-shared-disk"></a>2 番目の Azure 共有ディスクを作成して接続する

クラスター ノードのいずれかでこのコマンドを実行します。 リソース グループ、Azure リージョン、SAPSID などの値を調整する必要があります。  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>PowerShell を使用して共有ディスクをフォーマットする
1. ディスク番号を取得します。 クラスター ノードのいずれかで PowerShell コマンドを実行します。

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. ディスクをフォーマットします。 この例では、ディスク番号 3 です。 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. ディスクがクラスター ディスクとして表示されるようになったことを確認します。  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. クラスターにディスクを登録します。  
   ```powershell
     # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output 
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成

1. Windows DNS マネージャーで、新しい SAP ASCS/SCS インスタンスに対して仮想ホスト名の DNS エントリを作成します。  
   DNS で仮想ホスト名に割り当てる IP アドレスは、Azure Load Balancer で割り当てた IP アドレスと同じである必要があります。  

   ![SAP ASCS/SCS クラスターの仮想名と IP アドレスの DNS エントリを定義する][sap-ha-guide-figure-6009]
 
   _SAP ASCS/SCS クラスターの仮想名と IP アドレスの DNS エントリを定義する_

2. SAP エンキュー レプリケーション サーバー 2 を使用し、これもクラスター化されたインスタンスである場合、ERS2 の仮想ホスト名も DNS で予約する必要があります。 
   DNS で ERS2 の仮想ホスト名に割り当てる IP アドレスは、Azure Load Balancer で割り当てた IP アドレスと同じである必要があります。  

   ![SAP ERS2 クラスターの仮想名と IP アドレスの DNS エントリを定義する][sap-ha-guide-figure-6010]
 
   _SAP ERS2 クラスターの仮想名と IP アドレスの DNS エントリを定義する_

3. 仮想ホスト名に割り当てる IP アドレスを定義するには、 **[DNS マネージャー]**  >  **[ドメイン]** の順に選びます。

   ![SAP ASCS/SCS および ERS2 クラスター構成の新しい仮想名と IP アドレス][sap-ha-guide-figure-6011]

   _SAP ASCS/SCS および ERS2 クラスター構成の新しい仮想名と TCP/IP アドレス_

## <a name="sap-installation"></a>SAP のインストール 

### <a name="install-the-sap-first-cluster-node"></a> 最初の SAP クラスター ノードのインストール

SAP の説明に従ってインストール手順を行います。 インストールの開始オプションで [First Cluster Node]\(最初のクラスター ノード\) オプションを選択し、構成オプションとして [Cluster Shared Disk]\(クラスターの共有ディスク\) を選択します。  
新しく作成した共有ディスクを選択します。  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a> ASCS/SCS インスタンスの SAP プロファイルの変更

エンキュー レプリケーション サーバー 1 を実行している場合は、次に示すように SAP プロファイル パラメーター `enque/encni/set_so_keepalive` を追加します。 このプロファイル パラメーターにより、SAP ワーク プロセスとエンキュー サーバー間の接続が長時間にわたってアイドル状態のときに、接続が閉じられるのを防ぐことができます。 ERS2 の場合、SAP パラメーターは必要ありません。 

1. ERS1 を使用する場合は、このプロファイル パラメーターを SAP ASCS/SCS インスタンス プロファイルに追加します。

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   ERS1 と ERS2 の両方について、`keepalive` OS パラメーターが SAP ノート [1410736](https://launchpad.support.sap.com/#/notes/1410736) の説明に従って設定されていることを確認します。   

2. SAP プロファイル パラメーターの変更を適用するには、SAP ASCS/SCS インスタンスを再起動します。

### <a name="configure-probe-port-on-the-cluster-resource"></a>クラスター リソースでプローブ ポートを構成する

内部ロード バランサーのプローブ機能を使用して、クラスター全体の構成が Azure Load Balancer で動作するようにします。 通常、Azure 内部ロード バランサーは、参加している仮想マシン間に受信ワークロードを均等に分散させます。

ただし、アクティブなインスタンスが 1 つだけであるため、一部のクラスター構成では動作しません。 他のインスタンスはパッシブであり、ワークロードを受け付けることができません。 プローブ機能は、Azure 内部ロード バランサーによって、アクティブなインスタンスが検出され、アクティブなインスタンスのみが対象とされる場合に役立ちます。  

> [!IMPORTANT]
> この例の構成では、**ProbePort** は 620 **Nr** に設定されます。 番号が **02** の SAP ASCS インスタンスの場合は、620 **02** です。
> SAP インスタンス番号と SAP SID に合わせて構成を調整する必要があります。

プローブ ポートを追加するには、次のいずれかのクラスター VM でこの PowerShell モジュールを実行します。

- インスタンス番号が **02** の SAP ASCS/SCS インスタンスの場合 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- ERS2 を使用し、インスタンス番号が **12** で、これがクラスター化されている場合。 ERS1 については、クラスター化されていないため、プローブ ポートを構成する必要はありません。  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 関数 `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` のコードは次のようになります。
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manually restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>SAP のインストールを続行する

1. SAP インストール ガイドに記載されている手順に従って、データベース インスタンスをインストールします。  
2. SAP インストール ガイドに記載されている手順に従って、2 番目のクラスター ノードに SAP をインストールします。  
3. SAP プライマリ アプリケーション サーバー (PAS) のインスタンスを、PAS のホストとして指定した仮想マシンにインストールします。   
   SAP インストール ガイドに記載されている手順に従います。 Azure に対する依存関係はありません。
4. 追加の SAP アプリケーション サーバーを、SAP アプリケーション サーバー インスタンスのホストとして指定した仮想マシンにインストールします。  
   SAP インストール ガイドに記載されている手順に従います。 Azure に対する依存関係はありません。 

## <a name="test-the-sap-ascsscs-instance-failover"></a>SAP ASCS/SCS インスタンス フェールオーバーをテストする
このフェールオーバー テストの概要では、SAP ASCS がノード A でアクティブであることを前提としています。  

1. SAP システムがノード A からノード B に正常にフェールオーバーできることを確認します。この例では、SAPSID **PR2** に対してテストが行われます。  
   各 SAPSID が他のクラスター ノードに正常に移動できることを確認します。   
   次のいずれかの方法を選んで、クラスター ノード A からクラスター ノード B への SAP \<SID\> クラスター グループのフェールオーバーを開始します。
    - フェールオーバー クラスター マネージャー  
    - フェールオーバー クラスター PowerShell

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Windows ゲスト オペレーティング システムでクラスター ノード A を再起動します。 これにより、ノード A からノード B への SAP \<SID\> クラスター グループの自動フェールオーバーが開始されます。  
3. Azure Portal からクラスター ノード A を再起動します。 これにより、ノード A からノード B への SAP \<SID\> クラスター グループの自動フェールオーバーが開始されます。  
4. Azure PowerShell を使ってクラスター ノード A を再起動します。 これにより、ノード A からノード B への SAP \<SID\> クラスター グループの自動フェールオーバーが開始されます。

## <a name="next-steps"></a>次のステップ

* [SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP HA 向けに Azure インフラストラクチャを準備する][sap-high-availability-infrastructure-wsfc-shared-disk]
* [SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクに SAP NetWeaver HA をインストールする][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../availability.md