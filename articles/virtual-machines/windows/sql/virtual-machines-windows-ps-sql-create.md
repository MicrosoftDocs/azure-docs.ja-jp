---
title: "Azure PowerShell を使用した SQL Server 仮想マシンの作成 (Resource Manager) | Microsoft Docs"
description: "SQL Server 仮想マシン ギャラリー のイメージを使用して Azure VM を作成するための手順と PowerShell スクリプトを提供します。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/17/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 218d7dcf14ebbb28ac043e50e6f9248ecb942aaa
ms.lasthandoff: 03/25/2017


---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Azure PowerShell を使用した SQL Server 仮想マシンのプロビジョニング (Resource Manager)
> [!div class="op_single_selector"]
> * [ポータル](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
>
>

## <a name="overview"></a>概要
このチュートリアルでは、 **Azure Resource Manager** デプロイメント モデルと Azure PowerShell コマンドレットを使用して単一の Azure 仮想マシンを作成する方法について説明します。 このチュートリアルでは、SQL ギャラリー内のイメージから、単一のディスク ドライブを使用して 1 台の仮想マシンを作成します。 仮想マシンで使用するストレージ、ネットワーク、コンピューティング リソース用のプロバイダーは新たに作成します。 これらのリソースのプロバイダーが既にある場合は、既存のプロバイダーを使用してもかまいません。

同じ作業をクラシック デプロイメント モデルで行う方法については、「 [Azure PowerShell を使用した SQL Server 仮想マシンのプロビジョニング (クラシック)](../classic/ps-sql-create.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルには次のものが必要です。

* Azure アカウントとサブスクリプション (作業の開始前)。 お持ちでない場合は、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)1.4.0 以降 (このチュートリアルの執筆に使用したバージョンは 1.5.0)。
  * バージョンを確認するには、「 **Get-Module Azure -ListAvailable**」と入力します。

## <a name="configure-your-subscription"></a>サブスクリプションの構成
Windows PowerShell を開いて次のコマンドレットを実行し、Azure アカウントにアクセスできる状態にします。 資格情報を入力するためのサインイン画面が表示されます。 Azure ポータルへのサインインに使用しているものと同じ電子メールとパスワードを使用します。

    Add-AzureRmAccount

正常にサインインすると、サインインに使用した SubscriptionId を含む情報が画面に表示されます。 別のサブスクリプションに変更しない限り、このサブスクリプションでこのチュートリアルのリソースを作成することになります。 複数の SubscriptionId がある場合は、次のコマンドレットを実行して、すべての SubscriptionId のリストを取得してください。

    Get-AzureRmSubscription

別の SubscriptionID に変更するには、目的の SubscriptionId で次のコマンドレットを実行します。

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>イメージの変数の定義
このチュートリアルでは、作成するスクリプトを簡潔に読みやすくするために、さまざまな変数を最初に定義します。 パラメーターの値は適宜変更してください。ただし、指定した値に変更を加える際は、名前の長さや特殊文字に関連した制限に注意してください。

### <a name="location-and-resource-group"></a>場所とリソース グループ
データのリージョンと、仮想マシンの各種リソースの作成先となるリソース グループは、2 つの変数を使用して定義します。

適宜変更を加えたうえで次のコマンドレットを実行し、これらの変数を初期化します。

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>ストレージのプロパティ
以下の変数を使用して、ストレージ アカウントと、仮想マシンで使用するストレージの種類を定義します。

適宜変更を加えたうえで次のコマンドレットを実行し、これらの変数を初期化します。 この例では [Premium Storage](../../../storage/storage-premium-storage.md) を使用しています。運用環境のワークロードには Premium Storage の使用をお勧めします。 この指針とその他の推奨事項については、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)」を参照してください。

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>Network properties
ネットワーク インターフェイス、TCP/IP 割り当て方式、仮想ネットワーク名、仮想サブネット名、仮想ネットワークの IP アドレス範囲、サブネットの IP アドレス範囲、仮想マシン内のネットワークに使用するパブリック ドメイン名ラベルは、以下の変数を使用して定義します。

適宜変更を加えたうえで次のコマンドレットを実行し、これらの変数を初期化します。

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"

### <a name="virtual-machine-properties"></a>仮想マシン プロパティ
仮想マシン名、コンピューター名、仮想マシン サイズ、仮想マシンのオペレーティング システム ディスク名は、以下の変数を使用して定義します。

適宜変更を加えたうえで次のコマンドレットを実行し、これらの変数を初期化します。

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>イメージのプロパティ
仮想マシンに使用するイメージは、以下の変数を使用して定義します。 この例では、SQL Server 2016 Enterprise イメージを使用しています。

適宜変更を加えたうえで次のコマンドレットを実行し、これらの変数を初期化します。

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Get-AzureRmVMImageOffer コマンドを使用すると、提供されているすべての SQL Server イメージを一覧表示できます。

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

また、提供されている SKU については、Get-AzureRmVMImageSku コマンドで確認できます。 次のコマンドを実行すると、 **SQL2014SP1-WS2012R2** に関して提供されているすべての SKU が表示されます。

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>リソース グループの作成
Resource Manager デプロイメント モデルで最初に作成するオブジェクトはリソース グループです。 ここでは [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt759837.aspx) コマンドレットを使用して、Azure リソース グループとそのリソースを作成します。引数には、先ほど初期化した変数で定義したリソース グループの名前と場所を指定します。

次のコマンドレットを実行すると、新しいリソース グループが作成されます。

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
仮想マシンには、オペレーティング システム ディスク用と SQL Server (データおよびログ ファイル) 用のストレージ リソースが必要となります。 単純化するために、ここではその両方の用途を兼ねた単一のディスクを作成します。 SQL Server のデータ ファイルとログ ファイルを専用のディスクに格納する場合は、後から [Add-Azure Disk](https://msdn.microsoft.com/library/azure/dn495252.aspx) コマンドレットを使用して別途ディスクを接続することができます。 ここでは [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) コマンドレットを使用して、新しいリソース グループに Standard ストレージ アカウントを作成します。引数には、先ほど初期化した変数で定義したストレージ アカウント名、ストレージ SKU 名、場所を指定します。

次のコマンドレットを実行すると、新しいストレージ アカウントが作成されます。

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>ネットワーク リソースを作成する
ネットワーク接続を確保するためには、仮想マシンにさまざまなネットワーク リソースが必要となります。

* 個々の仮想マシンに仮想ネットワークが必要となります。
* 仮想ネットワークには少なくとも 1 つのサブネットが定義されている必要があります。
* パブリックまたはプライベートの IP アドレスを持つネットワーク インターフェイスが定義されている必要があります。

### <a name="create-a-virtual-network-subnet-configuration"></a>仮想ネットワークのサブネット構成の作成
まず仮想ネットワークのサブネット構成を作成しましょう。 このチュートリアルでは、 [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) コマンドレットを使用して既定のサブネットを作成します。 先ほど初期化した変数で定義したサブネット名とアドレス プレフィックスを引数に指定して、仮想ネットワークのサブネット構成を作成します。

> [!NOTE]
> サブネット名とアドレス プレフィックス以外のプロパティもこのコマンドレットで定義できますが、このチュートリアルの範囲を超えるため、ここでは取り上げません。
>
>

次のコマンドレットを実行して、仮想サブネットの構成を作成します。

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
次に、 [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) コマンドレットを使用して仮想ネットワークを作成します。 仮想ネットワークは、先ほど初期化した変数で定義した名前、場所、アドレス プレフィックスに加え、前の手順で定義したサブネット構成を引数に指定して、新しいリソース グループに作成します。

次のコマンドレットを実行して、仮想ネットワークを作成します。

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>パブリック IP アドレスの作成
仮想ネットワークを定義したら、仮想マシンとの接続に使用する IP アドレスを構成する必要があります。 このチュートリアルでは、インターネット接続をサポートするために、動的 IP アドレス指定を使用してパブリック IP アドレスを作成します。 パブリック IP アドレスは、[New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) コマンドレットを使用して、既に作成したリソース グループに作成します。引数には、先ほど初期化した変数で定義した名前、場所、割り当て方式、DNS ドメイン名ラベルを指定します。

> [!NOTE]
> パブリック IP アドレスのプロパティもこのコマンドレットで定義できますが、このチュートリアルの範囲を超えるため、ここでは取り上げません。 またプライベート アドレスや静的アドレスを作成することもできますが、このチュートリアルの範囲を超えるため、同様に説明を省略します。
>
>

パブリック IP アドレスを作成するには、次のコマンドレットを実行します。

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>ネットワーク インターフェイスの作成
次に、仮想マシンで使用するネットワーク インターフェイスを作成します。 作成済みのリソース グループにネットワーク インターフェイスを作成するには、[New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) コマンドレットを使用します。引数には、先ほど定義した名前、場所、サブネット、パブリック IP アドレスを指定します。

次のコマンドレットを実行して、ネットワーク インターフェイスを作成します。

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>VM オブジェクトの構成
ストレージとネットワーク リソースを定義したら、仮想マシンのコンピューティング リソースを定義することができます。 このチュートリアルではまず、仮想マシン サイズと各種オペレーティング システムのプロパティを指定し、先ほど作成したネットワーク インターフェイスを指定して、Blob Storage を定義した後、オペレーティング システム ディスクを指定します。

### <a name="create-the-vm-object"></a>VM オブジェクトの作成
最初に、仮想マシンのサイズを指定しましょう。 このチュートリアルでは DS13 を指定します。 構成可能な仮想マシン オブジェクトを作成するには、[New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) コマンドレットを使用します。引数には、先ほど初期化した変数で定義した名前とサイズを指定します。

次のコマンドレットを実行して、仮想マシン オブジェクトを作成します。

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>ローカル管理者の資格情報の名前とパスワードを保持する資格情報オブジェクトを作成する
仮想マシンに使用するオペレーティング システムのプロパティを設定するには、あらかじめローカル管理者アカウントの資格情報をセキュリティで保護された文字列として指定する必要があります。 ここでは、 [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) コマンドレットを使用します。

次のコマンドレットを実行して、Windows 仮想マシンのローカル管理者アカウントの名前とパスワードを Windows PowerShell の資格情報要求ウィンドウに入力します。

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>仮想マシンに使用するオペレーティング システムのプロパティ設定
次に、仮想マシンに使用するオペレーティング システムのプロパティを設定します。 この設定には [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) コマンドレットを使用します。オペレーティング システムの種類に Windows を指定し、[仮想マシン エージェント](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)のインストールを要求すると共に、自動更新を有効にします。仮想マシン名、コンピューター名、資格情報は、先ほど初期化した変数で設定しています。

次のコマンドレットを実行することで、仮想マシンに使用するオペレーティング システムのプロパティが設定されます。

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>仮想マシンにネットワーク インターフェイスを追加する
次に、先ほど作成したネットワーク インターフェイスを仮想マシンに追加しましょう。 [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) コマンドレットを使用してネットワーク インターフェイスを追加します。その際、定義済みのネットワーク インターフェイス変数を使います。

次のコマンドレットを実行すると、仮想マシンにネットワーク インターフェイスが追加されます。

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>仮想マシンで使用するディスクの Blob Storage の場所を設定する
次に、仮想マシンで使用するディスクの Blob Storage の場所を、既に定義してある変数を使用して設定します。

Blob Storage の場所は、次のコマンドレットを実行して設定します。

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>仮想マシンに使用するオペレーティング システムのディスクのプロパティ設定
次に、仮想マシンに使用するオペレーティング システムのディスクのプロパティを設定します。 この設定には、[Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) コマンドレットを使用します。仮想マシンのオペレーティング システムをイメージから作成するように設定し、キャッシュは読み取り専用に設定します (SQL Server を同じディスクにインストールするため)。また、仮想マシンの名前とオペレーティング システム ディスクは、定義済みの変数を使用して指定します。

次のコマンドレットを実行することで、仮想マシンに使用するオペレーティング システムのディスクのプロパティが設定されます。

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>仮想マシンのプラットフォーム イメージを指定する
最後の構成手順は、仮想マシンに使用するプラットフォーム イメージの指定です。 このチュートリアルでは、最新の SQL Server 2016 CTP イメージを使用します。 [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) コマンドレットの引数に、先に定義しておいた変数を指定して、このイメージが使用されるようにします。

次のコマンドレットを実行すると、仮想マシンのプラットフォーム イメージが指定されます。

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>SQL VM の作成
以上で構成関連の手順は終了です。いよいよ仮想マシンを作成することになります。 [New-AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) コマンドレットの引数に、先に定義しておいた変数を指定して、仮想マシンを作成します。

次のコマンドレットを実行すると仮想マシンが作成されます。

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

これで仮想マシンが作成されました。 仮想マシンのディスク用に指定したストレージ アカウントが Premium Storage アカウントであるため、ブート診断用に Standard Storage アカウントが作成されることに注意してください。

Azure ポータルでこのマシンを表示すると、 [対応するパブリック IP アドレスとその完全修飾ドメイン名](virtual-machines-windows-portal-sql-server-provision.md)を確認できます。

## <a name="example-script"></a>サンプル スクリプト
このチュートリアルで使用した PowerShell スクリプト全体は、次のようになっています。 Azure サブスクリプションについては、既に **Add-AzureRmAccount** コマンドと **Select-AzureRmSubscription** コマンドでセットアップ済みであることを想定しています。

    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>次のステップ
仮想マシンを作成したら、RDP を使用してその仮想マシンに接続し、接続のセットアップを行うことができます。 詳細については、「 [Azure での SQL Server 仮想マシンへの接続 (リソース マネージャー)](virtual-machines-windows-sql-connect.md)」を参照してください。


