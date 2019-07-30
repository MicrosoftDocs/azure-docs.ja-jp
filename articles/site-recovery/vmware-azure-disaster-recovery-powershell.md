---
title: Azure Site Recovery で PowerShell を使用して VMware VM の Azure へのディザスター リカバリーを設定する | Microsoft Docs
description: Azure Site Recovery で PowerShell を使用して VMware VM のディザスター リカバリーのために Azure へのレプリケーションとフェールオーバーを設定する方法を説明します。
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 06/30/2019
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: 7c13bb8586995a82ee240df39a9c95a67743e2a8
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503343"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>PowerShell を使用して VMware VM の Azure へのディザスター リカバリーを設定する

この記事では、Azure PowerShell を使って VMware の仮想マシンを Azure にレプリケートおよびフェールオーバーする方法を説明します。

学習内容は次のとおりです。

> [!div class="checklist"]
> - Recovery Services のコンテナーを作成し、コンテナーのコンテキストを設定する。
> - コンテナー内のサーバー登録を検証する。
> - レプリケーション ポリシーなど、レプリケーションを設定する。 vCenter サーバーを追加して VM を検出する。
> - vCenter サーバーを追加して検出する
> - レプリケーション ログまたはデータを保持するストレージ アカウントを作成し、VM をレプリケートする。
> - フェールオーバーを実行する。 仮想マシンをレプリケートするためのフェールオーバーの設定を構成し、設定を実行する。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

開始する前に次の操作を実行してください。

- [シナリオのアーキテクチャとコンポーネント](vmware-azure-architecture.md)を理解している。
- すべてのコンポーネントの[サポート要件](site-recovery-support-matrix-to-azure.md)を確認する。
- Azure PowerShell `Az` モジュールがある。 Azure PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成のガイド](/powershell/azure/install-az-ps)に関するページをご覧ください。

## <a name="log-into-azure"></a>Azure にログインする

Connect-AzAccount コマンドレットを使用して、Azure サブスクリプションにログインします。

```azurepowershell
Connect-AzAccount
```
VMware 仮想マシンをレプリケートする先の Azure サブスクリプションを選びます。 Get-AzSubscription コマンドレットを使って、アクセスできる Azure サブスクリプションの一覧を取得します。 Select-AzSubscription コマンドレットを使って、利用する Azure サブスクリプションを選びます。

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Recovery Services コンテナーの設定

1. Recovery Services コンテナーを作成するリソース グループを作成します。 次の例では、VMwareDRtoAzurePS という名前のリソース グループを東アジア リージョンに作成しています。

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Recovery Services コンテナーを作成します。 次の例では、VMwareDRToAzurePs という名前の Recovery Services コンテナーを、前のステップで作成した東アジア リージョンのリソース グループに作成します。

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. コンテナーの登録キーをダウンロードします。 コンテナー登録キーを使って、このコンテナーにオンプレミスの構成サーバーを登録します。 登録は、構成サーバーのソフトウェア インストール プロセスの一部です。

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. ダウンロードしたコンテナー登録キーを使い、以下の記事の手順に従って、構成サーバーのインストールと登録を行います。
   - [保護の目標を選択する](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [ソース環境をセットアップする](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>コンテナーのコンテキストを設定する

Set-ASRVaultContext コマンドレットを使って、コンテナーのコンテキストを設定します。 コンテキストを設定した後、PowerShell セッションでのそれ以降の Azure Site Recovery 操作は、選んだコンテナーのコンテキストで実行されます。

> [!TIP]
> Azure Site Recovery PowerShell モジュール (Az.RecoveryServices モジュール) のほとんどのコマンドレットには、使いやすいエイリアスがあります。 このモジュールのコマンドレットの形式は *\<操作>-**AzRecoveryServicesAsr**\<オブジェクト>* であり、これに対応するエイリアスの形式は *\<操作>-**ASR**\<オブジェクト>* です。 使いやすくするために、コマンドレットのエイリアスを置き換えることができます。

次の例では、$vault 変数に設定されているコンテナーの詳細を使って、PowerShell セッションに対するコンテナーのコンテキストを指定しています。

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Set-ASRVaultContext コマンドレットの代替として、Import-AzRecoveryServicesAsrVaultSettingsFile コマンドレットを使用してコンテナー コンテキストを設定することもできます。 コンテナー登録キー ファイルが -path パラメーターとして置かれているパスを Import-AzRecoveryServicesAsrVaultSettingsFile コマンドレットに指定します。 例:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
この記事の以降のセクションでは、Azure Site Recovery 操作のコンテナーのコンテキストは既に設定されているものとします。

## <a name="validate-vault-registration"></a>コンテナーの登録を検証する

この例の状態は次のとおりです。

- 構成サーバー (**ConfigurationServer**) が、このコンテナーに登録されています。
- 追加のプロセス サーバー (**ScaleOut-ProcessServer**) が、*ConfigurationServer* に登録されています。
- アカウント (**vCenter_account**、**WindowsAccount**、**LinuxAccount**) が構成サーバーに設定されています。 これらのアカウントを使って、仮想マシンを検出するための vCenter サーバーを追加し、レプリケートされる Windows サーバーと Linux サーバーにモビリティ サービス ソフトウェアをプッシュ インストールします。

1. 登録された構成サーバーは、Site Recovery ではファブリック オブジェクトによって表されます。 コンテナー内のファブリック オブジェクトのリストを取得して、構成サーバーを識別します。

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. マシンのレプリケートに使うことができるプロセス サーバーを識別します。

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   上の出力で、***$ProcessServers[0]*** は *ScaleOut-ProcessServer* に対応し、***$ProcessServers[1]*** は *ConfigurationServer* 上のプロセス サーバーの役割に対応します

3. 構成サーバーで設定されているアカウントを識別します。

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   上の出力で、***$AccountHandles[0]*** はアカウント *vCenter_account* に、***$AccountHandles[1]*** はアカウント *WindowsAccount* に、***$AccountHandles[2]*** はアカウント *LinuxAccount* にそれぞれ対応します

## <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

このステップでは、2 つのレプリケーション ポリシーを作成します。 1 つのポリシーは VMware 仮想マシンを Azure にレプリケートするためのものであり、もう 1 つは Azure で実行しているフェールオーバーされた仮想マシンをオンプレミスの VMware サイトにレプリケートして戻すためのものです。

> [!NOTE]
> Azure Site Recovery のほとんどの操作は非同期に実行されます。 操作を開始すると、Azure Site Recovery ジョブが送信されて、ジョブ追跡オブジェクトが返されます。 このジョブ追跡オブジェクトを使って、操作の状態を監視できます。

1. 指定したプロパティを持つ Azure に VMware 仮想マシンをレプリケートするための、*ReplicationPolicy* という名前のレプリケーション ポリシーを作成します。

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Azure からオンプレミスの VMware サイトへのフェールバックに使うレプリケーション ポリシーを作成します。

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   *$Job_FailbackPolicyCreate* のジョブの詳細を使って、操作の完了を追跡します。

   * レプリケーション ポリシーを構成サーバーとマップするための保護コンテナー マッピングを作成します。

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>vCenter サーバーを追加して VM を検出する

IP アドレスまたはホスト名で vCenter サーバーを追加します。 **-port** パラメーターは vCenter サーバー上の接続先ポート、 **-Name** パラメーターは vCenter サーバーに使用するフレンドリ名、 **-Account** パラメーターは vCenter サーバーによって管理される仮想マシンの検出に使う構成サーバー上のアカウント ハンドルを指定します。

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>レプリケーションのストレージ アカウントを作成する

**マネージド ディスクに書き込むには、[Powershell Az.RecoveryServices モジュール 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 以降を使用します。** 必要なのは、ログ ストレージ アカウントの作成のみです。 このアカウントは、一時ログの格納にのみ使用されるため、標準のアカウントの種類と LRS 冗長性を使用することを推奨します。 コンテナーと同じ Azure リージョンにストレージ アカウントが作成されることを確認します。

2\.0.0 より以前のバージョンの Az.RecoveryServices モジュールを使用する場合は、次の手順を使用してストレージ アカウントを作成します。 これらのストレージ アカウントは、後で仮想マシンのレプリケートに使われます。 コンテナーと同じ Azure リージョンにストレージ アカウントが作成されることを確認します。 既存のストレージ アカウントをレプリケーションに使う場合は、このステップを省略できます。

> [!NOTE]
> オンプレミスの仮想マシンを Premium Storage アカウントにレプリケートするときは、追加の Standard Storage アカウント (ログ ストレージ アカウント) を指定する必要があります。 ログ ストレージ アカウントは、Premium Storage ターゲットでログを適用できるようになるまでの中間ストアとして、レプリケーション ログを保持します。
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>VMware VM をレプリケートする

仮想マシンが vCenter サーバーから検出されるまでに約 15 - 20 分かかります。 検出が済むと、検出された仮想マシンごとに保護可能項目オブジェクトが Azure Site Recovery に作成されます。 このステップでは、検出された 3 つの仮想マシンが、前のステップで作成された Azure ストレージ アカウントにレプリケートされます。

検出された仮想マシンを保護するには、以下の詳細が必要です。

* レプリケートする保護可能項目。
* 仮想マシンをレプリケートするストレージ アカウント (ストレージ アカウントにレプリケートする場合のみ)。 
* Premium ストレージ アカウントまたはマネージド ディスクに仮想マシンを保護するには、ログ ストレージが必要です。
* レプリケーションに使われるプロセス サーバー。 利用可能なプロセス サーバーの一覧が取得されて、***$ProcessServers[0]*** *(ScaleOut-ProcessServer)* 変数および ***$ProcessServers[1]*** *(ConfigurationServer)* 変数に格納されています。
* マシンにモビリティ サービス ソフトウェアをプッシュ インストールするために使うアカウント。 利用可能なアカウントの一覧が取得されて、***$AccountHandles*** 変数に格納されています。
* レプリケーションに使われるレプリケーション ポリシーの保護コンテナー マッピング。
* フェールオーバー時に仮想マシンを作成する必要があるリソース グループ。
* 必要に応じて、フェールオーバーされた仮想マシンを接続する必要がある Azure 仮想ネットワークとサブネット。

それでは、次の表で指定されている設定を使って、以下の仮想マシンをレプリケートします


|仮想マシン  |プロセス サーバー        |ストレージ アカウント              |ログ ストレージ アカウント  |ポリシー           |モビリティ サービス インストール用アカウント|ターゲット リソース グループ  | ターゲット仮想ネットワーク  |ターゲット サブネット  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |該当なし| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| 該当なし                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

レプリケーション有効化ジョブが正常に完了すると、仮想マシンの初期レプリケーションが開始されます。 レプリケートされるデータの量およびレプリケーションに利用可能な帯域幅によっては、初期レプリケーションにしばらく時間がかかる可能性があります。 初期レプリケーションが完了した後、仮想マシンは保護された状態に移行します。 仮想マシンが保護された状態に達した後は、仮想マシンのテスト フェールオーバーを実行したり、復旧計画に仮想マシンを追加したりできます。

Get-ASRReplicationProtectedItem コマンドレットを使って、仮想マシンのレプリケーションの状態と正常性を確認することができます。

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>フェールオーバー設定を構成する

保護されたマシンのフェールオーバーの設定は、Set-ASRReplicationProtectedItem コマンドレットを使って更新できます。 このコマンドレットでは次のような設定を更新できます。
* フェールオーバー時に作成される仮想マシンの名前
* フェールオーバー時に作成される仮想マシンの VM サイズ
* フェールオーバー時に仮想マシンの NIC を接続する必要がある Azure 仮想ネットワークとサブネット
* マネージド ディスクへのフェールオーバー
* Azure Hybrid Use Benefit の適用
* フェールオーバー時に仮想マシンに割り当てられるターゲット仮想ネットワークからの静的 IP アドレスの割り当て。

この例では、フェールオーバー時に仮想マシン *Win2K12VM1* に対して作成される仮想マシンの VM サイズを更新し、フェールオーバー時に仮想マシンがマネージド ディスクを使うことを指定します。

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

1. 次のように DR ドリル (テスト フェールオーバー) を実行します。

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. テスト フェールオーバー ジョブが正常に完了すると、名前に " *-Test*" というサフィックスが付いた仮想マシン (この例では Win2K12VM1-Test) が、Azure に作成されます。
3. テスト フェールオーバーが行われた仮想マシンに接続して、テスト フェールオーバーを検証できるようになります。
4. Start-ASRTestFailoverCleanupJob コマンドレットを使って、テスト フェールオーバーをクリーンアップします。 この操作により、テスト フェールオーバー操作の一部として作成された仮想マシンが削除されます。

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Azure にフェールオーバーする

このステップでは、仮想マシン Win2K12VM1 を特定の復旧ポイントにフェールオーバーします。

1. フェールオーバーに使用することができる復旧ポイントの一覧を取得します。
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. フェールオーバーが正常に完了すると、フェールオーバー操作をコミットし、Azure からオンプレミスの VMware サイトへのレプリケーションの反転を設定できます。

## <a name="next-steps"></a>次の手順
[Azure Site Recovery PowerShell リファレンス](https://docs.microsoft.com/powershell/module/Az.RecoveryServices)を使用して、他のタスクを自動化する方法について学びます。
