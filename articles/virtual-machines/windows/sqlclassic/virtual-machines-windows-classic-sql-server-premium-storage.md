---
title: SQL Server で Azure Premium Storage を使用する | Microsoft Docs
description: この記事では、クラシック デプロイ モデルで作成されたリソースを使用し、Azure Virtual Machines で実行している SQL Server で Azure Premium Storage を使用するガイダンスを提供します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3b3bb206286629a68c14b6444f3f88ffa0af50dd
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540875"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>仮想マシン上での Azure Premium Storage と SQL Server の使用

## <a name="overview"></a>概要

[Azure Premium SSD](../disks-types.md) は、低遅延と高いスループット IO を提供する次世代のストレージです。 IaaS [仮想マシン](https://azure.microsoft.com/services/virtual-machines/)上の SQL Server など、主要な IO 集中型ワークロードに最適です。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。

この記事では、SQL Server を実行する仮想マシンから Premium Storage の使用への移行に関する計画とガイダンスについて説明します。 これには、Azure インフラストラクチャ (ネットワーク、ストレージ) とゲストの Windows VM の手順が含まれます。 [付録](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) の例では、PowerShell を使用して、強化されたローカル SSD ストレージを利用するように大きな VM を移動する方法の詳細な移行を示します。

これは、IAAS VM 上の SQL Server で Azure Premium Storage を利用するエンド ツー エンド プロセスを理解するのに重要です。 次のトピックがあります。

* Premium Storage 使用する前提条件の識別。
* Premium Storage に IaaS 上の SQL Server を新しくデプロイする例。
* 既存のデプロイを移行する例 (スタンドアロン サーバーと、SQL Always On 可用性グループを使用するデプロイの両方)。
* 考えられる移行方法。
* 既存の Always On 実装の移行の Azure、Windows、および SQL Server の詳細な手順を示す例。

Azure Virtual Machines での SQL Server についての背景情報については、[Azure Virtual Machines での SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)に関する記事をご覧ください。

**執筆者:** Daniel Sol **技術校閲者:** Luis Carlos Vargas Herring、Sanjay Mishra、Pravin Mital、Juergen Thomas、Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Premium Storage の前提条件

Premium Storage を使用するにはいくつかの前提条件があります。

### <a name="machine-size"></a>マシンのサイズ

Premium Storage を使用するには、DS シリーズ仮想マシン (VM) を使用する必要があります。 クラウド サービスで DS シリーズ マシンを使用していない場合、既存の VM を削除し、アタッチされたディスクを保持し、新しいクラウド サービスを作成してから、DS* ロール サイズとして VM を再作成する必要があります。 仮想マシンのサイズについて詳しくは、[Azure の仮想マシンおよびクラウド サービスのサイズ](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

### <a name="cloud-services"></a>クラウド サービス

Premium Storage で DS* VM を使用できるのは、新しいクラウド サービスに作成されるときだけです。 Azure で SQL Server Always On を使用する場合、Always On Listener はクラウド サービスと関連付けられた Azure の内部または外部 Load Balancer の IP アドレスを参照します。 この記事では、このシナリオで可用性を維持しながら移行する方法について説明します。

> [!NOTE]
> DS* シリーズは、新しいクラウド サービスにデプロイされる最初の VM である必要があります。
>
>

### <a name="regional-vnets"></a>地域 VNET

DS* VM の場合、VM をホストする Virtual Network (VNET) を地域 VNET として構成する必要があります。 このような VNET を「拡大」するのは、より大きな VM を他のクラスターにプロビジョニングできるようにして、それらの間の通信を可能にするためです。 次のスクリーンショットでは、強調表示されている場所は地域 VNET を示しますが、最初の結果は「狭い」 VNET を示しています。

![RegionalVNET][1]

地域 VNET に移行するには、Microsoft サポート チケットを提出します。 その後、マイクロソフトが変更を行います。 地域 Vnet への移行を完了するには、ネットワーク構成で AffinityGroup プロパティを変更します。 最初に PowerShell でネットワーク構成をエクスポートし、**VirtualNetworkSite** 要素の **AffinityGroup** プロパティを **Location** プロパティで置き換えます。 `Location = XXXX` を指定します。`XXXX` は Azure リージョンです。 次に、新しい構成をインポートします。

たとえば、次のような VNET 構成について考えます。

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

これを西ヨーロッパの地域 VNET に移動するには、構成を次のように変更します。
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>ストレージ アカウント

Premium Storage 用に構成された新しいストレージ アカウントを作成する必要があります。 Premium Storage の使用は、個別の VHD ではなくストレージ アカウントで設定されることに注意してください。ただし、DS* シリーズ VM を使用するときは、Premium および Standard Storage アカウントから VHD をアタッチできます。 Premium Storage アカウントに OS の VHD を配置したくない場合、これを検討できます。

次の **Type** が "Premium_LRS" の **New-AzureStorageAccountPowerShell** コマンドは、Premium Storage アカウントを作成します。

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>VHD キャッシュの設定

Premium Storage アカウントの一部であるディスクの作成の主な違いは、ディスク キャッシュの設定です。 SQL Server データ ボリューム ディスクの場合は、"**Read Caching**" を使用することが推奨されます。 トランザクション ログ ボリュームの場合は、ディスク キャッシュを "**None**" に設定する必要があります。 これは、Standard Storage アカウントに対する推奨事項とは異なります。

VHD をアタッチした後は、キャッシュの設定を変更できません。 VHD をいったんデタッチし、キャッシュの設定を更新して VHD を再アタッチする必要があります。

### <a name="windows-storage-spaces"></a>Windows 記憶域スペース

以前の Standard Storage と同じように、[Windows 記憶域スペース](https://technet.microsoft.com/library/hh831739.aspx)を使用できます。これにより、既に記憶域スペースを使用している VM を移行できます。 [付録](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)の例では (手順 9 以降)、複数の VHD がアタッチされている VM を抽出してインポートする PowerShell コードが示されています。

記憶域プールを Standard Azure ストレージ アカウントと共に使用して、スループットを上げ、遅延を短縮しています。 新しいデプロイ用に Premium Storage で記憶域プールをテストする価値はありますが、記憶域のセットアップが複雑になります。

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>記憶域プールにマップしている Azure 仮想ディスクを確認する方法

アタッチされている VHD には異なるキャッシュ設定の推奨事項があるので、Premium Storage アカウントに VHD をコピーすることがあります。 ただし、新しい DS シリーズ VM に再アタッチするときは、キャッシュの設定を変更する必要がある場合があります。 SQL データ ファイルとログ ファイル用に (両方を含む単一の VHD ではなく) 個別の VHD があるときは、Premium Storage の推奨キャッシュ設定を適用する方が簡単です。

> [!NOTE]
> SQL Server のデータ ファイルとログ ファイルが同じボリュームにある場合は、選択するキャッシュ オプションはデータベース ワークロードの IO アクセス パターンによって異なります。 このシナリオに最適なキャッシュ オプションはテストすることによってのみわかります。
>
>

ただし、複数の VHD で構成される Windows 記憶域スペースを使用している場合は、各ディスクに対して適切にキャッシュを設定できるように、元のスクリプトを見て、アタッチされている各 VHD がどのプールにあるかを確認する必要があります。

どの VHD が記憶域プールにマップしているかがわかる元のスクリプトを使用できない場合は、次の手順を使用して、ディスク/記憶域プールのマッピングを判別できます。

各ディスクについて、次の手順を使用します。

1. **Get-AzureVM** コマンドで、VM にアタッチされているディスクのリストを取得します。

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. ディスク名と LUN に注目します。

    ![DisknameAndLUN][2]
1. VM にリモート デスクトップ接続します。 **[コンピューターの管理]** | **[デバイス マネージャー]** | **[ディスク ドライブ]** の順に選択します。 [Microsoft 仮想ディスク] の各プロパティを確認します。

    ![VirtualDiskProperties][3]
1. この LUN 番号は、VM に VHD をアタッチするときに指定した LUN 番号です。
1. [Microsoft 仮想ディスク] の **[詳細]** タブに移動し、**[プロパティ]** 一覧の **[ドライバー キー]** に移動します。 **[値]** で**オフセット**を確認します (次のスクリーンショットでは 0002)。 0002 は記憶域プールが参照する PhysicalDisk2 を表します。

    ![VirtualDiskPropertyDetails][4]
1. 各記憶域プールで、関連するディスクをダンプします。

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

この情報を使用して、アタッチされているVHD を記憶域プールの物理ディスクに関連付けることができます。

VHD を記憶域プールの物理ディスクにマップした後は、デタッチして Premium Storage アカウントにコピーし、正しいキャッシュ設定でアタッチできます。 [付録](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)の手順 8 ～ 12 の例を参照してください。 これらの手順では、VM にアタッチされた VHD ディスクの構成を CSV ファイルに抽出し、VHD をコピーし、ディスク構成のキャッシュ設定を変更し、最後にすべてのディスクをアタッチして DS シリーズ VM として VM をデプロイする方法が示されています。

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM ストレージの帯域幅と VHD ストレージのスループット

ストレージのパフォーマンスは、指定されている DS* VM のサイズと VHD のサイズによって決まります。 VM により、アタッチできる VHD の数と、サポートする最大帯域幅 (MB/秒) は異なります。 具体的な帯域幅については、[Azure の仮想マシンおよびクラウド サービスのサイズ](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。

ディスク サイズを大きくすると IOPS が向上します。 移行パスについて考えるときはこれを検討する必要があります。 詳しくは、[IOPS とディスク タイプの表](../disks-types.md#premium-ssd)をご覧ください。

最後に、VM はアタッチされるすべてのディスクについてサポートされる最大ディスク帯域幅が異なることを考慮します。 高負荷では、その VM ロール サイズに対して使用可能な最大ディスク帯域幅が飽和状態になる可能性があります。 たとえば、Standard_DS14 は最大 512MB/秒をサポートします。したがって、3 台の P30 ディスクで、VM のディスク帯域幅が飽和する可能性があります。 ただし、この例では、読み取りと書き込みの IO 組み合わせによってはスループット制限を超える場合があります。

## <a name="new-deployments"></a>新しいデプロイ

次の 2 つのセクションでは、SQL Server VM を Premium Storage にデプロイする方法を示します。 前述のように、必ずしも Premium Storage に OS ディスクを配置する必要はありません。 IO 集中型ワークロードを OS VHD に配置する場合、これを行うことができます。

最初の例では、既存の Azure ギャラリー イメージを利用する場合を示します。 2 番目の例では、既存の Standard Storage アカウント内にあるカスタム VM イメージを使用する方法を示します。

> [!NOTE]
> これらの例では、地域 VNET を既に作成してあるものとします。

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>ギャラリー イメージを使用した Premium Storage での新しい VM の作成

次の例では、OS VHD を Premium Storage に配置して Premium Storage VHD をアタッチする方法を示します。 ただし、OS ディスクを Standard Storage アカウントに配置してから、Premium Storage アカウントに存在する VHD をアタッチすることもできます。 両方のシナリオを示します。

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>手順 1:Premium Storage アカウントを作成する

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>手順 2:新しいクラウド サービスを作成する

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>手順 3:クラウド サービス VIP を予約する (省略可能)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = “sqlcloudVIP”
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>手順 4:VM コンテナーを作成する

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>手順 5:Standard または Premium Storage に OS VHD を配置する

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>手順 6:VM を作成する

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>カスタム イメージで Premium Storage を使用する新しい VM の作成

このシナリオでは、既存のカスタマイズされたイメージが Standard Storage アカウントに存在する場合を示します。 前述のように、OS VHD を Premium Storage に配置する場合は、Standard Storage アカウントに存在するイメージをコピーし、使用する前に Premium Storage に転送する必要があります。 オンプレミスにイメージがある場合は、この方法を使用してそれを Premium Storage アカウントに直接コピーすることもできます。

#### <a name="step-1-create-storage-account"></a>手順 1:[ストレージ アカウントを作成]

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>手順 2. クラウド サービスを作成する

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>手順 3:既存のイメージを使用する

既存のイメージを使用できます。 または、[既存のマシンのイメージを取得](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)できます。 イメージを取得するマシンは DS* マシンでなくてもよいことに注意してください。 イメージを用意した後、次に示すように、**Start-AzureStorageBlobCopy** PowerShell コマンドレットで Premium Storage アカウントにコピーします。

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>手順 4:ストレージ アカウント間で BLOB をコピーする

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>手順 5:コピー状態を定期的に確認する

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>手順 6:サブスクリプション内の Azure ディスク リポジトリにイメージ ディスクを追加する

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> ステータス レポートが成功であっても、ディスク リース エラーが発生することがあります。 この場合は、10 分ほど待ちます。

#### <a name="step-7--build-the-vm"></a>手順 7:VM の構築

ここでは、イメージから VM を構築し、2 つの Premium Storage VHD をアタッチします。

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!”


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Always On 可用性グループを使用しない既存のデプロイ

> [!NOTE]
> 既存のデプロイの場合、最初にこの記事の「[前提条件](#prerequisites-for-premium-storage)」セクションをご覧ください。

Always On 可用性グループを使用しない SQL Server のデプロイと使用するデプロイでは考慮事項が異なります。 Always On を使用せず、既存のスタンドアロン SQL Server がある場合、新しいクラウド サービスとストレージ アカウントを使用して Premium Storage にアップグレードできます。 以下のオプションを検討します。

* **新しい SQL Server VM を作成します**。 新しいデプロイに記載されているように、Premium Storage アカウントを使用する新しい SQL Server VM を作成できます。 その後、SQL Server の構成とユーザー データベースをバックアップして復元します。 新しい SQL Server が内部的または外部的にアクセスされる場合、新しい SQL Server を参照するようにアプリケーションを更新する必要があります。 サイド バイ サイド (SxS) SQL Server 移行を行う場合のように、すべての ‘out of db’ オブジェクトをコピーする必要があります。 これには、ログイン、証明書、およびリンク サーバーなどのオブジェクトが含まれます。
* **既存の SQL Server VM を移行します**。 SQL Server VM をオフラインにしてから、新しいクラウド サービスに転送する必要があります。これには、アタッチされているすべての VHD の Premium Storage アカウントへのコピーが含まれます。 VM がオンラインになると、アプリケーションは前と同じサーバー ホスト名を参照します。 既存のディスクのサイズがパフォーマンス特性に影響することに注意してください。 たとえば、400 GB のディスクは P20 に切り上げられます。 そのディスク パフォーマンスが必要ないことがわかっている場合は、VM を DS シリーズ VM として再作成し、必要なサイズ/パフォーマンス仕様の Premium Storage VHD をアタッチできます。 その後、SQL DB ファイルをデタッチして再アタッチすることができます。

> [!NOTE]
> VHD ディスクをコピーするときは、サイズに注意する必要があります。サイズに依存するということは、当てはまる Premium Storage ディスク タイプがディスク パフォーマンス仕様を決定することになります。 Azure は最も近いディスク サイズに切り上げるので、400 GB ディスクがある場合、P20 に切り上げます。 OS VHD の既存の IO 要件によっては、Premium Storage アカウントにこれを移行する必要はありません。

SQL Server が外部からアクセスされる場合、クラウド サービス VIP は変更されます。 エンドポイント、ACL、DNS の設定も更新する必要があります。

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Always On 可用性グループを使用する既存のデプロイ

> [!NOTE]
> 既存のデプロイの場合、最初にこの記事の「[前提条件](#prerequisites-for-premium-storage)」セクションをご覧ください。

このセクションでは最初に、Always On が Azure ネットワークとやり取りする方法を見ていきます。 その後、移行を 2 つのシナリオに分割します。ある程度のダウンタイムを許容できる移行と、ダウンタイムを最小限にする必要がある移行です。

オンプレミス SQL Server Always On 可用性グループは、仮想 DNS 名と共に、1 つまたは複数の SQL Server で共有される IP アドレスを登録する、オンプレミスのリスナーを使用します。 クライアントは、接続するとき、リスナーの IP を経由してプライマリ SQL Server にルーティングされます。 これは、その時点で Always On の IP リソースを所有しているサーバーです。

![DeploymentsUseAlways On][6]

Microsoft Azure ではただ 1 つの IP アドレスを VM の NIC に割り当てることができるので、オンプレミスと同じ抽象階層を実現するため、Azure は内部/外部ロード バランサー (ILB/ELB) に割り当てられた IP アドレスを使用します。 サーバー間で共有される IP リソースは、ILB/ELB と同じ IP に設定されます。 これが、DNS で発行され、クライアント トラフィックは ILB/ELB を介して SQL Server のプライマリ レプリカに渡されます。 ILB/ELB はプローブを使用して Always On の IP リソースを調査するため、どの SQL Server がプライマリかを認識しています。 前の例では、ELB/ILB によって参照されるエンドポイントを持つ各ノードをプローブし、プライマリ SQL Server が応答します。

> [!NOTE]
> ILB と ELB はどちらも特定の Azure クラウド サービスに割り当てられ、そのため、Azure のクラウド移行は多くの場合ロード バランサーの IP が変更されることを意味します。
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>ある程度のダウンタイムが許容される Always On のデプロイの移行

ある程度のダウンタイムが許容される Always On のデプロイを移行するには 2 つの方法があります。

1. **既存の Always On クラスターにセカンダリ レプリカを追加する**
2. **新しい Always On クラスターに移行する**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1.既存の Always On クラスターにセカンダリ レプリカを追加する

1 つの方法は、Always On 可用性グループにセカンダリを追加することです。 新しいクラウド サービスに追加し、新しいロード バランサーの IP でリスナーを更新する必要があります。

##### <a name="points-of-downtime"></a>ダウンタイムのポイント:

* クラスターの検証。
* 新しいセカンダリに対する Always On フェールオーバーのテスト。

高い IO スループットのために VM で Windows 記憶域プールを使用している場合、完全なクラスター検証の間にオフラインになります。 クラスターにノードを追加するときは、検証テストが必要です。 テストにかかる時間はさまざまなので、代表的なテスト環境でこれをテストし、おおよその時間を調べる必要があります。

Always On の高可用性が期待どおりに機能することを確認するため、新しく追加されたノードで手動フェールオーバーと混乱テストを実行できる時間をプロビジョニングする必要があります。

![DeploymentUseAlways On2][7]

> [!NOTE]
> 検証を実行する前に、記憶域プールが使用されているすべての SQL Server インスタンスを停止する必要があります。
>
> ##### <a name="high-level-steps"></a>手順の概要
>

1. Premium Storage をアタッチして、2 つの新しい SQL Server を新しいクラウド サービスに作成します。
2. 完全バックアップをコピーし、 **NORECOVERY**で復元します。
3. ログインなど、‘out of user DB’ 依存オブジェクトをコピーします。
4. 内部ロード バランサー (ILB) を新しく作成するか、または外部ロード バランサー (ELB) を使用して、両方の新しいノードにロード バランサー エンドポイントを設定します。

   > [!NOTE]
   > 続行する前に、すべてのノードのエンドポイント構成が適切であることをチェックしてください
   >
   >
5. (記憶域プールを使用している場合) SQL サーバーへのユーザー/アプリケーション アクセスを停止します。
6. (記憶域プールを使用している場合) SQL Server エンジン サービスをすべてのノードで停止します。
7. クラスターに新しいノードを追加し、完全な検証を実行します。
8. 検証が成功した後、すべての SQL Server サービスを開始します。
9. トランザクション ログをバックアップし、ユーザー データベースを復元します。
10. Always On 可用性グループに新しいノードを追加して、レプリケーションを **同期**に設定します。
11. [付録](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)のマルチサイトの例に基づいて、PowerShell を使用して、新しいクラウド サービス ILB/ELB の IP アドレス リソースを Always On に追加します。 Windows クラスタリングで、新しいノードに対する **IP アドレス** リソースの**実行可能な所有者**を古いものに設定します。 [付録](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)の「同じサブネットの IP アドレス リソースの追加」セクションを参照してください。
12. 新しいノードのいずれかにフェールオーバーします。
13. 新しいノードを自動フェールオーバー パートナーにして、フェールオーバーをテストします。
14. 可用性グループから元のノードを削除します。

##### <a name="advantages"></a>長所

* 新しい SQL サーバーを Always On に追加する前にテストできます (SQL Server とアプリケーション)。
* 要件と厳密に一致するように、VM サイズを変更し、ストレージをカスタマイズできます。 ただし、SQL のすべてのファイル パスを同じままに保持すると便利です。
* セカンダリ レプリカへの DB バックアップの転送が開始するときを制御できます。 これは、Azure **Start-AzureStorageBlobCopy** コマンドレットを使用する VHD のコピーとは異なります。これは非同期のコピーです。

##### <a name="disadvantages"></a>短所

* Windows 記憶域プールを使用する場合、新しい追加ノードの完全なクラスター検証の間にクラスターのダウンタイムが発生します。
* SQL Server のバージョンおよび既存のセカンダリ レプリカの数によっては、既存のセカンダリを削除しないとセカンダリ レプリカを追加できない可能性があります。
* セカンダリ設定中の SQL データ転送時間が長くなる可能性があります。
* 新しいマシンを並行して実行しながらの移行には追加コストがかかります。

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2.新しい Always On クラスターに移行する

もう 1 つの方法は、新しいクラウド サービスに新しいノードで新しい Always On クラスターを作成し、それを使用するようにクライアントをリダイレクトすることです。

##### <a name="points-of-downtime"></a>ダウンタイムのポイント

新しい Always On リスナーにアプリケーションとユーザーを転送するときにダウンタイムがあります。 ダウンタイムは以下に依存します。

* 新しいサーバーのデータベースに最後のトランザクション ログ バックアップを復元するのにかかる時間。
* 新しい Always On リスナーを使用するようにクライアント アプリケーションを更新するのにかかる時間。

##### <a name="advantages"></a>長所

* 実際の運用環境、SQL Server、OS ビルドの変更をテストできます。
* ストレージをカスタマイズでき、VM のサイズを小さくできる可能性があります。 これは、コスト削減につながります。
* この処理中に、SQL Server のビルドまたはバージョンを更新できます。 オペレーティング システムをアップグレードすることもできます。
* 前の Always On クラスターを確実なロールバック ターゲットとして使用できます。

##### <a name="disadvantages"></a>短所

* 両方の Always On クラスターを同時に実行する場合は、リスナーの DNS 名を変更する必要があります。 その場合、クライアント アプリケーションの文字列に新しいリスナー名を反映する必要があるため、移行時の管理オーバーヘッドが増えます。
* 移行前に最終的に必要な同期を最小限に抑えるには、2 つの環境ができるだけ似たものになるように、環境間の同期メカニズムを実装する必要があります。
* 新しい環境を実行しながらの移行には追加コストがかかります。

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>最小限のダウンタイムでの Always On のデプロイの移行

ダウンタイムを最小限にして Always On のデプロイを移行するには 2 つの方法があります。

1. **既存のセカンダリを利用する:シングルサイト**
2. **既存のセカンダリ レプリカを利用する:マルチサイト**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1.既存のセカンダリを利用する:シングルサイト

ダウンタイムを最小限にする 1 つの方法は、既存のクラウドをセカンダリにし、現在のクラウド サービスから削除することです。 その後、新しい Premium Storage アカウントに VHD をコピーし、新しいクラウド サービスに VM を作成します。 次に、クラスターおよびフェールオーバーでリスナーを更新します。

##### <a name="points-of-downtime"></a>ダウンタイムのポイント

* 負荷分散エンドポイントで最後のノードを更新するときにダウンタイムが発生します。
* クライアント/DNS の構成によっては、クライアントの再接続が遅れる可能性があります。
* IP アドレスをスワップするために Always On クラスター グループをオフラインにする場合は、追加のダウンタイムが発生します。 追加される IP アドレス リソースに対して OR 依存関係と実行可能な所有者を使用することで、これを回避できます。 [付録](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)の「同じサブネットの IP アドレス リソースの追加」セクションを参照してください。

> [!NOTE]
> 追加されるノードを Always On フェールオーバー パートナーにする場合は、負荷分散セットへの参照を持つ Azure エンドポイントを追加する必要があります。 **Add-azureendpoint** コマンドを実行してこれを行うと、現在の接続は開いたままになりますが、ロード バランサーが更新されるまでリスナーへの新しい接続は確立できません。 テストではこれは 90 ～ 120 秒継続しましたが、テストする必要があります。

##### <a name="advantages"></a>長所

* 移行中に追加コストが発生しません。
* 1 対 1 の移行です。
* 複雑さが軽減されます。
* Premium Storage SKU による IOPS の増加に対応します。 ディスクを VM からデタッチして新しいクラウド サービスにコピーするとき、サード パーティのツールを使用して VHD のサイズを増やし、スループットを上げることができます。 VHD のサイズの拡大については、この [フォーラム ディスカッション](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows)を参照してください。

##### <a name="disadvantages"></a>短所

* 移行中に、一時的に高可用性と障害復旧が失われます。
* これは 1:1 の移行であるため、VHD の数をサポートする最小限の VM サイズを使用する必要があり、VM をダウンサイズできない可能性があります。
* このシナリオで使用する Azure **Start-AzureStorageBlobCopy** コマンドレットは非同期です。 コピーの完了に対する SLA はありません。 コピーに要する時間は一定ではなく、キューでの待機に依存し、それはまた転送するデータの量に依存します。 別のリージョンの Premium Storage をサポートする別の Azure データ センターへの転送の場合、コピー時間が増加します。 ノードが 2 つだけの場合、コピーがテストより長くかかる場合に可能な移行について考慮します。 これには次のアイデアが含まれます。
  * 合意されたダウンタイムでの移行の前に、HA に第 3 の SQL Server ノードを一時的に追加します。
  * Azure のスケジュールされたメンテナンスの時間外に移行を実行します。
  * クラスター クォーラムを正しく構成したことを確認します。  

##### <a name="high-level-steps"></a>手順の概要

このドキュメントでは完全なエンド ツー エンドの例は説明しませんが、この実行に利用できる詳細な情報を [付録](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) で提供します。

![MinimalDowntime][8]

* ディスクの構成を収集し、ノードを削除します (アタッチされている VHD は削除しないでください)。
* Premium Storage アカウントを作成し、Standard Storage アカウントから VHD をコピーします。
* 新しいクラウド サービスを作成し、そのクラウド サービスに SQL2 VM を再デプロイします。 コピーされた元の OS VHD を使用して VM を作成し、コピーした VHD をアタッチします。
* ILB/ELB を構成し、エンドポイントを追加します。
* 次のいずれかでリスナーを更新します。
  * Always On グループをオフラインにし、新しい ILB/ELB IP アドレスで Always On リスナーを更新します。
  * または、PowerShell を使用して Windows クラスターに新しいクラウド サービスの ILB/ELB の IP アドレス リソースを追加します。 その後、IP アドレス リソースの実行可能な所有者を移行済みのノード、SQL2 に設定し、これをネットワーク名で OR 依存関係として設定します。 [付録](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)の「同じサブネットの IP アドレス リソースの追加」セクションを参照してください。
* DNS の構成およびクライアントへの伝達を確認します。
* SQL1 VM を移行し、手順 2 ～ 4 を実行します。
* 手順 5ii を使用する場合、追加される IP アドレス リソースの実行可能な所有者として SQL1 追加します。
* フェールオーバーをテストします。

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2.既存のセカンダリ レプリカを利用する:マルチサイト

複数の Azure データセンター (DC) にノードがある場合、またはハイブリッド環境がある場合は、その環境で Always On 構成を使用してダウンタイムを最小にできます。

そのためには、オンプレミスまたはセカンダリの Azure DC の Always On 同期を同期に変更し、その SQL Server にフェールオーバーします。 その後、Premium Storage アカウントに VHD をコピーし、新しいクラウド サービスにマシンを再デプロイします。 リスナーを更新し、フェールバックします。

##### <a name="points-of-downtime"></a>ダウンタイムのポイント

ダウンタイムは、代替 DC へのフェールオーバーとフェールバックにかかる時間で構成されます。 また、クライアント/DNS の構成によっては、クライアントの再接続が遅れる可能性があります。
ハイブリッド Always On 構成の次のような例について考えます。

![MultiSite1][9]

##### <a name="advantages"></a>長所

* 既存のインフラストラクチャを利用できます。
* DR Azure DC で最初に Azure ストレージを事前アップグレードできます。
* DR Azure DC ストレージを再構成できます。
* テスト フェールオーバーを除き、移行の間のフェールオーバーが最小限 2 つです。
* バックアップおよび復元で SQL Server のデータを移動する必要がありません。

##### <a name="disadvantages"></a>短所

* SQL Server へのクライアントのアクセスによっては、SQL Server がアプリケーションに対して代替 DC で実行している場合、遅延が増加する可能性があります。
* Premium Storage への VHD のコピーに長い時間がかかります。 これは、可用性グループにノードを保持するかどうかの決定に影響する可能性があります。 プライマリ ノードはトランザクション ログにレプリケートされないトランザクションを保持する必要があるため、移行が必要なときにログが大量に発生する処理を実行している場合は、このことを検討します。 トランザクション ログは非常に大きくなる可能性があります。
* このシナリオで使用する Azure **Start-AzureStorageBlobCopy** コマンドレットは非同期です。 完了に対する SLA はありません。 コピーに要する時間は一定ではなく、キューでの待機に依存し、それはまた転送するデータの量に依存します。 したがって、第 2 のデータ センターにノードが 1 つだけある場合、コピーがテストより長くかかるときは、軽減手順を実行する必要があります。 これらの軽減手順には、次のアイデアが含まれます。
  * 合意されたダウンタイムでの移行の前に、HA に第 2 の SQL ノードを一時的に追加します。
  * Azure のスケジュールされたメンテナンスの時間外に移行を実行します。
  * クラスター クォーラムを正しく構成したことを確認します。

このシナリオでは、インストールを文書化してあり、最適なディスク キャッシュ設定に変更するためにストレージがどのようにマッピングされているのかわかっているものとします。

##### <a name="high-level-steps"></a>手順の概要

![Multisite2][10]

* オンプレミス/代替 Azure DC をプライマリ SQL Server にして、それを他の自動フェールオーバー パートナー (AFP) にします。
* SQL2 からディスク構成情報を収集し、ノードを削除します (アタッチされている VHD は削除しないでください)。
* Premium Storage アカウントを作成し、Standard Storage アカウントから VHD をコピーします。
* 新しいクラウド サービスを作成し、アタッチされた Premium Storage ディスクで SQL2 VM を作成します。
* ILB/ELB を構成し、エンドポイントを追加します。
* 新しい ILB/ELB IP アドレスで Always On リスナーを更新し、フェールオーバーをテストします。
* DNS の構成を確認します。
* AFP を SQL2 に変更した後、SQL1 を移行して、手順 2 ～ 5 を実行します。
* フェールオーバーをテストします。
* AFP を SQL1 および SQL2 に切り替えて戻します。

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>付録:マルチサイト Always On クラスターの Premium Storage への移行

以降では、マルチサイト Always On クラスターを Premium Storage に変換する例について詳細に説明します。 また、リスナーも、外部ロード バランサー (ELB) ではなく内部ロード バランサー (ILB) を使用するように変換します。

### <a name="environment"></a>環境

* Windows 2k12/SQL 2k12
* SP に 1 個の DB ファイル
* ノードごとに 2 個の記憶域プール

![Appendix1][11]

### <a name="vm"></a>VM:

この例では、ELB から ILB への移行を示します。 ELB は ILB より前に使用できたので、これは移行中に ILB に切り替える方法を示しています。

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>前の手順サブスクリプションに接続する

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>手順 1:新しいストレージ アカウントおよびクラウド サービスを作成する

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>手順 2:リソースに対して許可するエラーを増やす \<省略可能>

Always On 可用性グループに属する特定のリソースでは、クラスター サービスがリソース グループの再起動を試みる前に一定期間中に発生できるエラー数に制限があります。 手動によるフェールオーバーおよびマシンのシャットダウンによるフェールオーバーのトリガーを行わない場合はこの制限に近づくことがあるので、この手順を実行する間は制限を大きくすることをお勧めします。

許可するエラーを 2 倍にするのが賢明です。フェールオーバー クラスター マネージャーでこれを行うには、Always On リソース グループのプロパティに移動します。

![Appendix3][13]

最大エラー数 6 に変更します。

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>手順 3:クラスター グループに IP アドレス リソースを追加する \<省略可能>

クラスター グループの IP アドレスが 1 つだけであり、それがクラウドのサブネットに整列されている場合は、そのネットワークのクラウドですべてのクラスター ノードを誤ってオフラインにすると、クラスター IP リソースおよびクラスター ネットワーク名をオンラインにできなくなることに注意してください。 そのような場合には、他のクラスター リソースに対する更新ができなくなります。

#### <a name="step-4-dns-configuration"></a>手順 4:DNS の構成

円滑な移行の実装は、DNS が利用および更新される方法に依存します。
Always On は、インストールされるときに Windows クラスター リソース グループを作成します。フェールオーバー クラスター マネージャーを開くと、少なくとも 3 つのリソースがあることがわかり、次の 2 つがドキュメントで参照されています。

* Virtual Network 名 (VNN) – Always On 経由で SQL Server に接続するときにクライアントが接続する DNS 名。
* IP アドレス リソース – VNN に関連付けられている IP アドレス。複数の IP アドレス リソースを使用できます。マルチサイト構成では、サイト/サブネットごとに IP アドレスがあります。

SQL Server に接続すると、SQL Server クライアント ドライバーは、リスナーに関連付けられている DNS レコードを取得し、Always On に関連付けられている各 IP アドレスに接続を試みます。 次に、これに影響を与える可能性のあるいくつかの要因について説明します。

リスナー名に関連付けられている同時 DNS レコードの数は、関連付けられている IP アドレスの数だけでなく、Always On VNN リソースに対するフェールオーバー クラスターでの "RegisterAllIpProviders" の設定にも依存します。

Always On を Azure にデプロイするときは、リスナーおよび IP アドレスを作成する異なる手順があります。"RegisterAllIpProviders" を 1 に手動で構成する必要があります。これは、既に 1 に設定されているオンプレミスの Always On のデプロイとは異なります。

"RegisterAllIpProviders" が 0 の場合、リスナーに関連付けられている DNS では DNS レコードが 1 つだけ表示されます。

![Appendix4][14]

"RegisterAllIpProviders" が 1 の場合:

![Appendix5][15]

次のコードは、VNN 設定をダンプし、それを自動的に設定します。 変更を反映するには、VNN をオフラインにし、その後オンラインに戻す必要があります。 これを行うと、リスナーがオフラインになり、クライアント接続が中断されます。

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

後の移行手順では、ロード バランサーを参照する更新された IP アドレスで Always On リスナーを更新する必要があります。これには、IP アドレス リソースの削除および追加が含まれます。 IP の更新の後、DNS ゾーンで新しい IP アドレスが更新されたこと、およびクライアントがローカル DNS キャッシュを更新していることを、確認する必要があります。

クライアントが異なるネットワーク セグメントに存在し、別の DNS サーバーを参照している場合、アプリケーションの再接続時間が少なくともリスナーの新しい IP アドレスのゾーン転送時間によって制限されるため、移行の間に DNS ゾーン転送に何が起きるかを考慮する必要があります。 ここでの時間制約下にある場合は、Windows チームと増分ゾーン転送について検討して強制的にテストを行い、クライアントが更新するように DNS ホスト レコードを低い Time To Live (TTL) にする必要もあります。 詳しくは、[増分ゾーン転送](https://technet.microsoft.com/library/cc958973.aspx)に関する記事と「[Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer)」をご覧ください。

既定では、Azure の Always On でリスナーと関連付けられている DNS レコードの TTL は、1200 秒です。 移行の間に、リスナーの更新された IP アドレスでクライアントが DNS を更新するのに時間的な制約がある場合は、この値を減らすことができます。 VNN の構成をダンプして、構成を確認および変更できます。

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> "HostRecordTTL" を小さくすると DNS トラフィックの量が増えます。

##### <a name="client-application-settings"></a>クライアント アプリケーションの設定

ご自分の SQL クライアント アプリケーションが .NET 4.5 SQLClient をサポートしている場合は、"MULTISUBNETFAILOVER = TRUE" キーワードを使用できます。 このキーワードを適用すると、フェールオーバー時の SQL Always On 可用性グループへの接続が高速化されるので、可能な場合には適用するようにしてください。 このキーワードは、Always On リスナーに関連付けられているすべての IP アドレスを並列に列挙し、フェールオーバー中により積極的な TCP 接続の再試行速度を実行します。

上記の設定について詳しくは、「[MultiSubnetFailover のキーワードおよび関連機能](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)」をご覧ください。 また、「[高可用性障害復旧のための SqlClient サポート](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx)」も参照してください。

#### <a name="step-5-cluster-quorum-settings"></a>手順 5:クラスター クォーラムを設定する

一度に少なくとも 1 つの SQL Server を停止する場合、クラスターのクォーラム設定を変更する必要があります。2 つのノードでファイル共有監視 (FSW) を使用している場合、ノード マジョリティを許可して動的な投票を利用するようにクォーラムを設定する必要があります。これにより、1 つのノードは稼働したままになります。

```powershell
Set-ClusterQuorum -NodeMajority  
```

クラスター クォーラムの管理と構成の詳細については、「 [Configure and Manage the Quorum in a Windows Server 2012 Failover Cluster (Windows Server 2012 フェールオーバー クラスターでのクォーラムの構成と管理)](https://technet.microsoft.com/library/jj612870.aspx)」を参照してください。

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>手順 6:既存のエンドポイントと ACL を抽出する

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

このテキストをファイルに保存します。

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>手順 7:フェールオーバー パートナーとレプリケーション モードを変更する

3 つ以上の SQL Server がある場合、別の DC またはオンプレミスの別のセカンダリのフェールオーバーを「同期」に変更し、それを自動フェールオーバー パートナー (AFP) にする必要があります。これは、変更を行っている間に HA を維持するためです。 これは、SSMS を変更して TSQL によって行うことができます。

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>ステップ 8:クラウド サービスからセカンダリ VM を削除する

クラウドのセカンダリ ノードを最初に移行するように計画してください。 このノードが現在プライマリの場合は、手動フェールオーバーを開始する必要があります。

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>手順 9:CSV ファイルでディスク キャッシュの設定を変更して保存する

データ ボリュームの場合、これらを READONLY に設定する必要があります。

TLOG ボリュームの場合、これらを NONE に設定する必要があります。

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>手順 10: VHD をコピーする

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


Premium Storage アカウントへの VHD のコピー状態を確認できます。

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix8][18]

これらすべてが成功として記録されるまで待ちます。

個々の BLOB の詳細について:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>手順 11: OS ディスクを登録する

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>手順 12:新しいクラウド サービスにセカンダリをインポートする

次のコードでは追加オプションも使用します。ここでは、マシンをインポートし、持続可能な VIP を使用できます。

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>手順 13:新しいクラウド SVC に ILB を作成し、負荷分散されるエンドポイントと ACL を追加する

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>手順 14:Always On を更新する

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Appendix9][19]

ここでは、古いクラウド サービスの IP アドレスを削除します。

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>手順 15:DNS の更新を確認する

SQL Server クライアント ネットワーク上の DNS サーバーを確認し、クラスタリングが追加された IP アドレスの追加ホスト レコードを追加したことを確認します。 これらの DNS サーバーが更新されていない場合、DNS ゾーン転送を強制し、サブネットのクライアントが両方の Always On IP アドレスを解決できるようにすることを検討します。これは、自動 DNS レプリケーションを待つ必要がないようにするためです。

#### <a name="step-16-reconfigure-always-on"></a>手順 16:Always On を再構成する

この時点で、移行されたセカンダリ ノードがオンプレミス ノードと完全に再同期し、同期レプリケーション ノードに切り替えてそれを AFP にするまで待ちます。  

#### <a name="step-17-migrate-second-node"></a>手順 17:2 番目のノードを移行する

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>手順 18:CSV ファイルでディスク キャッシュの設定を変更して保存する

データ ボリュームの場合、キャッシュ設定を READONLY に設定する必要があります。

TLOG ボリュームの場合、キャッシュ設定を NONE に設定する必要があります。

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>手順 19:セカンダリ ノード用に独立したストレージ アカウントを新規作成する

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>手順 20:VHD をコピーする

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

すべての VHD について VHD コピー状態を確認できます。

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix12][22]

これらすべてが成功として記録されるまで待ちます。

個々の BLOB の詳細について:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>手順 21:OS ディスクを登録する

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>手順 22:負荷分散されるエンドポイントと ACL を追加する

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>手順 23:[テスト フェールオーバー]

移行されたノードがオンプレミスの Always On ノードと同期するのを待ちます。 ノードを同期レプリケーション モードにし、同期されるまで待ちます。 その後、オンプレミスから移行された最初のノードにフェールオーバーします。これは AFP です。 正しく動作した後は、最後に移行されたノードを AFP に変更します。

すべてのノード間のフェールオーバーをテストし、混乱テストを実行してフェールオーバーが意図したとおりにタイムリーに動作することを確認する必要があります。

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>手順 24:クラスター クォーラムの設定/DNS TTL/フェールオーバー パートナー/同期設定を元に戻す

##### <a name="adding-ip-address-resource-on-same-subnet"></a>同じサブネットの IP アドレス リソースの追加

SQL Server が 2 つだけであり、それらを同じサブネットのまま新しいクラウド サービスに移行する場合は、元の Always On IP アドレスを削除して新しい IP アドレスを追加するためにリスナーをオフラインにする必要はありません。 VM を別のサブネットに移行する場合は、そのサブネットを参照している追加のクラスター ネットワークが存在するので、これを行う必要はありません。

既存のプライマリをフェールオーバーする前に、移行されたセカンダリを起動し、新しいクラウド サービス用の新しい IP アドレス リソースを追加した後、クラスター フェールオーバー マネージャーで次の手順を実行します。

IP アドレスの追加については、「付録」の手順 14 を参照してください。

1. 現在の IP アドレス リソースについて、実行可能な所有者を "既存のプライマリ SQL Server" に変更します。例では "dansqlams4" です。

    ![Appendix13][23]
2. 新しい IP アドレス リソースについて、実行可能な所有者を "移行されたセカンダリ SQL Server" に変更します。例では "dansqlams5" です。

    ![Appendix14][24]
3. これを設定した後、フェールオーバーを実行し、最後のノードが移行されるときに、そのノードが実行可能な所有者として追加されるように、実行可能な所有者を編集する必要があります。

    ![Appendix15][25]

## <a name="additional-resources"></a>その他のリソース

* [Azure Premium Storage](../disks-types.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [Azure Virtual Machines における SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
