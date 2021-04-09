---
title: VMware VM を Azure に移行する (エージェントレス) - PowerShell
description: Azure Migrate で PowerShell を使用して VMware VM のエージェントレス移行を実行する方法について説明します。
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 03/02/2021
ms.openlocfilehash: 24dd33495915a9f4d47a00fbbfe9e894df839d4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715073"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>VMware VM を Azure に移行する (エージェントレス) - PowerShell

この記事では、Azure PowerShell を使用して、検出された VMware VM をエージェントレスの方法で移行する方法について説明します ([[Azure Migrate: Server Migration]](migrate-services-overview.md#azure-migrate-server-migration-tool) 内)。

以下の方法について説明します。

> [!div class="checklist"]
> * Azure Migrate プロジェクト内の検出された VMware VM を取得します。
> * VM のレプリケートを開始します。
> * VM をレプリケートするためのプロパティを更新します。
> * レプリケーションを監視します。
> * すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。
> * 完全な VM 移行を実行します。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルでは、可能な限り既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="1-prerequisites"></a>1.前提条件

このチュートリアルを始める前に、次の準備が必要です。

1. 「[チュートリアル: Server Assessment を使用して VMware VM を検出する](tutorial-discover-vmware.md)」を完了して、移行のために Azure と VMware を準備します。
2. 「[チュートリアル: Azure VM への移行のために VMware VM を評価する](./tutorial-assess-vmware-azure-vm.md)」を完了してから Azure に移行します。
3. [Az PowerShell モジュールをインストールします](/powershell/azure/install-az-ps)。

## <a name="2-install-azure-migrate-powershell-module"></a>2. Azure Migrate PowerShell モジュールをインストールする

Azure Migrate PowerShell モジュールは Azure PowerShell (`Az`) の一部として利用できます。 `Get-InstalledModule -Name Az.Migrate` コマンドを実行して、Azure Migrate PowerShell モジュールがコンピューターにインストールされているかどうかを確認します。  

## <a name="3-sign-in-to-your-microsoft-azure-subscription"></a>3. Microsoft Azure サブスクリプションにサインインする

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して、Azure サブスクリプションにサインインします。

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-your-azure-subscription"></a>Azure サブスクリプションを選択します。

[Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) コマンドレットを使用して、アクセスできる Azure サブスクリプションのリストを取得します。 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用して、使用する Azure Migrate プロジェクトを含む Azure サブスクリプションを選択します。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="4-retrieve-the-azure-migrate-project"></a>4.Azure Migrate プロジェクトを取得する

Azure Migrate のプロジェクトは、評価または移行しようとしている環境から収集された検出、評価、移行のメタデータを格納するために使用されます。
プロジェクト内で、検出された資産を追跡し、評価を調整して、移行を実行することができます。

前提条件の一部として、Azure Migrate プロジェクトを既に作成しているでしょう。 [Get-AzMigrateProject](/powershell/module/az.migrate/get-azmigrateproject) コマンドレットを使用して、Azure Migrate プロジェクトの詳細を取得します。 Azure Migrate プロジェクトの名前 (`Name`) と Azure Migrate プロジェクトのリソース グループの名前 (`ResourceGroupName`) を指定する必要があります。

```azurepowershell-interactive
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name MyResourceGroup

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name MyMigrateProject -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
Write-Output $MigrateProject
```

## <a name="5-retrieve-discovered-vms-in-an-azure-migrate-project"></a>5.Azure Migrate プロジェクト内の検出された VM を取得する

Azure Migrate には軽量の [Azure Migrate アプライアンス](migrate-appliance-architecture.md)が使用されます。 前提条件の一部として、Azure Migrate アプライアンスを VMware VM としてデプロイしたでしょう。

Azure Migrate プロジェクト内の特定の VMware VM を取得するには、Azure Migrate プロジェクトの名前 (`ProjectName`)、Azure Migrate プロジェクトのリソース グループ (`ResourceGroupName`)、VM 名 (`DisplayName`) を指定します。


```azurepowershell-interactive
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName MyTestVM | Format-Table DisplayName, Name, Type

# View discovered server details
Write-Output $DiscoveredServer
```

このチュートリアルでは、この VM を移行します。

(`ProjectName`) および (`ResourceGroupName`) パラメーターを使用して、Azure Migrate プロジェクト内のすべての VMware VM を取得することもできます。

```azurepowershell-interactive
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```

Azure Migrate プロジェクト内に複数のアプライアンスがある場合は、(`ProjectName`)、(`ResourceGroupName`)、(`ApplianceName`) の各パラメーターを使用することで、特定の Azure Migrate アプライアンスを使用して検出されたすべての VM を取得できます。

```azurepowershell-interactive
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName MyMigrateAppliance

```

## <a name="6-initialize-replication-infrastructure"></a>6.レプリケーション インフラストラクチャを初期化する

[Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) での VM の移行には、複数の Azure リソースを利用します。 Server Migration によって、プロジェクトと同じリソース グループに次のリソースがプロビジョニングされます。

- **サービス バス**: Server Migration では、サービス バスを使用して、レプリケーション オーケストレーション メッセージをアプライアンスに送信します。
- **ゲートウェイ ストレージ アカウント**: Server Migration では、ゲートウェイ ストレージ アカウントを使用して、レプリケートされる VM に関する状態情報を格納します。
- **ログ ストレージ アカウント**: Azure Migrate アプライアンスでは、VM のレプリケーション ログをログ ストレージ アカウントにアップロードします。 Azure Migrate により、レプリケーション情報がレプリカ マネージド ディスクに適用されます。
- **キー コンテナー**: Azure Migrate アプライアンスでは、キー コンテナーを使用して、サービス バスの接続文字列と、レプリケーションで使用されるストレージ アカウントのアクセス キーを管理します。

Azure Migrate プロジェクト内の最初の VM をレプリケートする前に、次のコマンドを実行してレプリケーション インフラストラクチャをプロビジョニングします。 このコマンドを使用して、VMware VM の移行を開始できるように、前述のリソースをプロビジョニングして構成します。

> [!NOTE]
> 1 つの Azure Migrate プロジェクトによって、1 つの Azure リージョンへの移行のみがサポートされます。 このスクリプトを実行すると、VMware VM の移行先となるターゲット リージョンを変更することはできなくなります。
> Azure Migrate プロジェクト内に新しいアプライアンスを構成する場合は、`Initialize-AzMigrateReplicationInfrastructure` コマンドを実行する必要があります。

この記事では、VM を `Central US` リージョンに移行できるようにレプリケーション インフラストラクチャを初期化します。

```azurepowershell-interactive
# Initialize replication infrastructure for the current Migrate project
Initialize-AzMigrateReplicationInfrastructure -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject. Name -Scenario agentlessVMware -TargetRegion "CentralUS" 

```

## <a name="7-replicate-vms"></a>7.VM をレプリケートする

レプリケーションインフラストラクチャの検出と初期化が完了したら、Azure への VMware VM のレプリケーションを開始できます。 最大 500 件のレプリケーションを同時に実行できます。

レプリケーションのプロパティは、次のように指定できます。

- **ターゲット サブスクリプションとリソース グループ** - (`TargetResourceGroupId`) パラメーターを使用してリソース グループ ID を指定することにより、VM の移行先となるサブスクリプションとリソース グループを指定します。
- **ターゲット仮想ネットワークとサブネット** - (`TargetNetworkId`) および (`TargetSubnetName`) パラメーターを使用して、VM の移行先となる Azure 仮想ネットワークの ID とサブネットの名前を指定します。
- **ターゲット VM 名** - (`TargetVMName`) パラメーターを使用して、作成する Azure VM の名前を指定します。
- **ターゲット VM サイズ** - (`TargetVMSize`) パラメーターを使用して、レプリケートする VM に使用する Azure VM のサイズを指定します。 たとえば、VM を Azure の D2_v2 VM に移行するには、(`TargetVMSize`) の値を "Standard_D2_v2" と指定します。
- **ライセンス** - アクティブなソフトウェア アシュアランスまたは Windows Server サブスクリプションの対象となっている Windows Server マシンの Azure ハイブリッド特典を使用するには、(`LicenseType`) パラメーターの値として **WindowsServer** を指定します。 それ以外の場合は、(`LicenseType`) パラメーターの値を "NoLicenseType" と指定します。
- **OS ディスク** - オペレーティング システムのブートローダーとインストーラーがあるディスクの一意識別子を指定します。 使用するディスク ID は、[Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) コマンドレットを使用して取得したディスクの一意識別子 (UUID) プロパティです。
- **ディスクの種類** - (`DiskType`) パラメーターの値を次のように指定します。
    - Premium マネージド ディスクを使用するには、(`DiskType`) パラメーターの値として "Premium_LRS" を指定します。
    - Standard SSD ディスクを使用するには、(`DiskType`) パラメーターの値として "StandardSSD_LRS" を指定します。
    - Standard HDD ディスクを使用するには、(`DiskType`) パラメーターの値として "Standard_LRS" を指定します。
- **インフラストラクチャの冗長性** - 次のようにインフラストラクチャ冗長オプションを指定します。
    - 可用性ゾーン。移行されたマシンをリージョン内の特定の可用性ゾーンにピン留めします。 このオプションを使用して、複数ノードのアプリケーション層を形成するサーバーを可用性ゾーン間で分散させます。 このオプションは、移行用に選択したターゲット リージョンで Availability Zones がサポートされている場合にのみ使用できます。 可用性ゾーンを使用するには、(`TargetAvailabilityZone`) パラメーターの可用性ゾーンの値を指定します。
    - 可用性セット。移行されたマシンを可用性セットに配置します。 このオプションを使用するには、選択したターゲット リソース グループに 1 つ以上の可用性セットが必要です。 可用性セットを使用するには、(`TargetAvailabilitySet`) パラメーターの可用性セット ID を指定します。
 - **ブート診断ストレージ アカウント** - ブート診断ストレージ アカウントを使用するには、(`TargetBootDiagnosticStorageAccount`) パラメーターの ID を指定します。
    -  ブート診断に使用されるストレージ アカウントは、VM の移行先と同じサブスクリプションにある必要があります。  
    - 既定では、このパラメーターに値は設定されません。 

### <a name="replicate-vms-with-all-disks"></a>すべてのディスクを使用して VM をレプリケートする

このチュートリアルでは、検出された VM のすべてのディスクをレプリケートし、Azure での VM の新しい名前を指定します。 検出されたサーバーの最初のディスクを OS ディスクとして指定し、すべてのディスクを Standard HDD として移行します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType Standard_LRS -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>選択したディスクを使用して VM をレプリケートする

[New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) コマンドレットを使用し、[New-AzMigrateServerReplication](/powershell/module/az.migrate/new-azmigrateserverreplication) コマンドレットの (`DiskToInclude`) パラメーターへの入力として指定することで、検出された VM のディスクを選択的にレプリケートすることもできます。 [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) コマンドレットを使用して、レプリケートする個々のディスクに対して異なるターゲット ディスクの種類を指定することもできます。

[New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) コマンドレットの次のパラメーターの値を指定します。

- **DiskId** - 移行するディスクの一意識別子を指定します。 使用するディスク ID は、[Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) コマンドレットを使用して取得したディスクの一意識別子 (UUID) プロパティです。
- **IsOSDisk** - 移行するディスクが VM の OS ディスクである場合は "true" を指定し、それ以外の場合は "false" を指定します。
- **DiskType** - Azure で使用するディスクの種類を指定します。

次の例では、検出された VM の 2 つのディスクのみをレプリケートします。 OS ディスクを指定し、レプリケートするディスクごとに異なるディスクの種類を使用します。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。

```azurepowershell-interactive
# View disk details of the discovered server
Write-Output $DiscoveredServer.Disk

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType StandardSSD_LRS -IsOSDisk true
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType Premium_LRS -IsOSDisk false

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="8-monitor-replication"></a>8.レプリケーションを監視します

レプリケーションは、次のように行われます。

- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーション中に、VM スナップショットが作成されます。 スナップショットのディスク データは、Azure のレプリカ マネージド ディスクにレプリケートされます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。

[Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) コマンドレットを使用して、レプリケーションの状態を追跡します。



```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM

# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

出力の **移行状態 (MigrationState)** および **移行状態の説明 (MigrationStateDescription)** プロパティを追跡できます。

- 初期レプリケーションでは、**移行状態** および **移行状態の説明** プロパティの値は、それぞれ `InitialSeedingInProgress` と `Initial replication` になります。
- 差分レプリケーションの間、**移行状態** および **移行状態の説明** プロパティの値は、それぞれ `Replicating` と `Ready to migrate` になります。
- 移行が完了すると、**移行状態** および **移行状態の説明** プロパティの値は、それぞれ `Migration succeeded` と `Migrated` になります。

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

レプリケーションの進行状況の詳細を確認するには、次のコマンドレットを実行します。

```azurepowershell-interactive
Write-Output $replicatingserver.ProviderSpecificDetail
```

出力の **初期シード処理の割合 (InitialSeedingProgressPercentage)** プロパティを使用して、初期レプリケーションの進行状況を追跡できます。

```Output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

レプリケーションは、次のように行われます。

- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーション中に、VM スナップショットが作成されます。 スナップショットのディスク データは、Azure のレプリカ マネージド ディスクにレプリケートされます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。

## <a name="9-retrieve-the-status-of-a-job"></a>9.ジョブの状態を取得する

[Get-AzMigrateJob](/powershell/module/az.migrate/get-azmigratejob) コマンドレットを使用して、ジョブの状態を監視できます。

```azurepowershell-interactive
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="10-update-properties-of-a-replicating-vm"></a>10。レプリケートする VM のプロパティを更新する

[Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) を使用すると、レプリケートする VM の名前、サイズ、リソース グループ、NIC 構成などのターゲット プロパティを変更できます。

VM に関する次のプロパティを更新できます。

- **VM 名** - [`TargetVMName`] パラメーターを使用して、作成される Azure VM の名前を指定します。
- **VM サイズ** - [`TargetVMSize`] パラメーターを使用して、レプリケートする VM に使用する Azure VM のサイズを指定します。 たとえば、VM を Azure の D2_v2 VM に移行するには、[`TargetVMSize`] の値として `Standard_D2_v2` を指定します。
- **仮想ネットワーク** - [`TargetNetworkId`] パラメーターを使用して、VM の移行先となる Azure 仮想ネットワークの ID を指定します。
- **リソース グループ** - [`TargetResourceGroupId`] パラメーターを使用してリソース グループ ID を指定することにより、VM の移行先となるリソース グループの ID を指定します。
- **ネットワーク インターフェイス** - [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping) コマンドレットを使用して NIC 構成を指定できます。 このオブジェクトは、[Set-AzMigrateServerReplication](/powershell/module/az.migrate/set-azmigrateserverreplication) コマンドレットの [`NicToUpdate`] パラメーターへの入力として渡されます。

    - **IP 割り当ての変更** - NIC の静的 IP を指定するには、[`TargetNicIP`] パラメーターを使用して、VM の静的 IP として使用する IPv4 アドレスを指定します。 NIC の IP を動的に割り当てるには、**TargetNicIP** パラメーターの値として `auto` を指定します。
    - [`TargetNicSelectionType`] パラメーターに、`Primary`、`Secondary`、または `DoNotCreate` を使用して、移行された VM 上で NIC をプライマリにするか、セカンダリにするか、または作成されないようにするかを指定します。 VM のプライマリ NIC として指定できる NIC は 1 つだけです。
    - NIC をプライマリにするには、移行された VM 上でセカンダリにする、または作成されないようにする他の NIC も指定する必要があります。
    - NIC のサブネットを変更するには、[`TargetNicSubnet`] パラメーターを使用してサブネットの名前を指定します。

 - **可用性ゾーン** - 可用性ゾーンを使用するには、[`TargetAvailabilityZone`] パラメーターの可用性ゾーン値を指定します。
 - **可用性セット** - 可用性セットを使用するには、[`TargetAvailabilitySet`] パラメーターの可用性セット ID を指定します。

[Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) コマンドレットにより、操作の状態を監視するために追跡できるジョブが返されます。

```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM

# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```

次の例では、最初の NIC をプライマリにして、それに静的 IP を割り当てることによって、NIC 構成を更新します。 移行対象の 2 番目の NIC を破棄し、ターゲット VM の名前とサイズを更新します。

```azurepowershell-interactive
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP ###.###.###.### -TargetNicSelectionType Primary
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType DoNotCreate

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize Standard_DS13_v2 -TargetVMName MyMigratedVM -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq 'InProgress') -or ($UpdateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $UpdateJob.State
```



## <a name="11-run-a-test-migration"></a>11.テスト移行を実行する

差分レプリケーションが開始されるとき、Azure への完全な移行を実行する前に、VM のテスト移行を実行できます。 各マシンで少なくとも 1 回は、移行前にテスト移行を実行することを強くお勧めします。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。

- テスト移行を実行すると、移行が想定どおりに動作することが確認されます。 テスト移行を行ってもオンプレミスのマシンには影響がなく稼働状態が維持され、レプリケーションが続行されます。
- テスト移行では、レプリケートされたデータを使用して Azure VM を作成することによって、移行がシミュレートされます (通常は、自分の Azure サブスクリプション内の非運用 VNet に移行されます)。
- レプリケートされたテスト Azure VM を使用して、移行を検証し、アプリのテストを実行して、完全な移行前に問題に対処することができます。

[`TestNetworkID`] パラメーターを使用して仮想ネットワークの ID を指定することによって、テストに使用する Azure 仮想ネットワークを選択します。

```azurepowershell-interactive
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq 'InProgress') -or ($TestMigrationJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $TestMigrationJob.State
```

テストが完了したら、[Start-AzMigrateTestMigrationCleanup](/powershell/module/az.migrate/start-azmigratetestmigrationcleanup) コマンドレットを使用してテスト移行をクリーンアップします。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。

```azurepowershell-interactive
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $CleanupTestMigrationJob.State
```

## <a name="12-migrate-vms"></a>12.VM の移行

テスト移行が想定どおりに動作することを確認したら、次のコマンドレットを使用して、レプリケートするサーバーを移行できます。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。

移行元サーバーの電源がオフにならないようにするには、[`TurnOffSourceServer`] パラメーターを使用しないでください。

```azurepowershell-interactive
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="13-complete-the-migration"></a>13.移行を完了する

1. 移行が完了したら、次のコマンドレットを使用して、オンプレミスのマシンのレプリケーションを停止し、VM のレプリケーション状態情報をクリーンアップします。 コマンドレットは、操作の状態を監視するために追跡できるジョブを返します。

   ```azurepowershell-interactive
   # Stop replication for a migrated server
   $StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer

   # Track job status to check for completion
   while (($StopReplicationJob.State -eq 'InProgress') -or ($StopReplicationJob.State -eq 'NotStarted')){
           #If the job hasn't completed, sleep for 10 seconds before checking the job status again
           sleep 10;
           $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
   }
   # Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
   Write-Output $StopReplicationJob.State
   ```

1. マシンの OS が Linux の場合は、移行されたマシンに [Linux](../virtual-machines/extensions/agent-linux.md) エージェントをインストールします。 Windows VM の VM エージェントは、移行中に自動的にインストールされます。
1. データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
1. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
1. 移行された Azure VM インスタンスにトラフィックを切り替えます。
1. ローカル VM インベントリからオンプレミスの VM を削除します。
1. ローカル バックアップからオンプレミスの VM を削除します。
1. Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。

## <a name="14-post-migration-best-practices"></a>14.移行後のベスト プラクティス

- 復元性の向上:
    - Azure Backup サービスを使用して、Azure VM をバックアップすることで、データの安全性を保持します。 [詳細については、こちらを参照してください](../backup/quick-backup-vm-portal.md)。
    - Azure VM を Site Recovery のセカンダリ リージョンにレプリケートし、継続的にワークロードを実行して利用可能にします。 [詳細については、こちらを参照してください](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- セキュリティの強化：
    - [Azure Security Center のジャスト イン タイム管理](../security-center/security-center-just-in-time.md)を利用して、インバウンド トラフィック アクセスをロックダウンして制限します。
    - [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)を使って、ネットワーク トラフィックを管理エンドポイントに制限します。
    - [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) をデプロイして、ディスクをセキュリティ保護し、盗難や不正アクセスからデータを安全に保護します。
    - [IaaS リソースのセキュリティ保護](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)に関する詳細を読み、[Azure Security Center](https://azure.microsoft.com/services/security-center/) を確認してください。
- 監視と管理：
-  [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) をデプロイして、リソースの使用率と消費量を監視します。
