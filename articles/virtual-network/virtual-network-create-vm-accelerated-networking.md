---
title: "高速ネットワークを使った Azure 仮想マシンの作成 | Microsoft Docs"
description: "高速ネットワークを使った仮想マシンの作成方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: c852a1297261504015a3a985fe14a38957d1a64a
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>高速ネットワークを使った仮想マシンの作成

このチュートリアルでは、高速ネットワークを使った Azure 仮想マシン (VM) の作成方法について説明します。 高速ネットワークは、Windows および特定の Linux ディストリビューションのパブリック プレビューの GA です。 高速ネットワークによって、VM との間でシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされる VM タイプの最も要件の厳しいネットワーク ワークロードで使用した場合でも、待ち時間、ジッター、CPU 使用率が軽減されます。 次の図は、2 台の仮想マシン (VM) 間の通信を、高速ネットワークを使う場合と使わない場合とで比較したものです。

![比較](./media/virtual-network-create-vm-accelerated-networking/image1.png)

高速ネットワークを使用しない場合は、VM に出入りするすべてのネットワーク トラフィックがホストと仮想スイッチをスキャンする必要があります。 仮想スイッチでは、ネットワーク セキュリティ グループ、アクセス制御リスト、分離、その他のネットワーク仮想化サービスなど、すべてのポリシーが適用されます。 仮想スイッチの詳細については、[Hyper-V ネットワーク仮想化と仮想スイッチ](https://technet.microsoft.com/library/jj945275.aspx)に関する記事を参照してください。

高速ネットワークを使用した場合、ネットワーク トラフィックは、VM のネットワーク インターフェイス (NIC) に到達した後、VM に転送されます。 高速ネットワークを使用しなかった場合に仮想スイッチによって適用されるネットワーク ポリシーはすべてオフロードされ、ハードウェアで適用されます。 ハードウェアにポリシーを適用することによって、ホストに適用されるポリシーをすべて維持したまま、ホストや仮想スイッチをバイパスして、NIC からネットワーク トラフィックを直接 VM に転送できます。

高速ネットワークのメリットが得られるのは、高速ネットワークが有効になっている VM だけです。 最適な結果を得るには、同じ Azure 仮想ネットワーク (VNet) に接続された 2 台以上の VM でこの機能を有効にしておくことをお勧めします。 VNet の境界を越えて通信するときや、オンプレミスで接続する場合、全体的な待ち時間に対してこの機能がもたらす効果は限定的です。

> [!WARNING]
> この Linux Public Preview は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 機能はサポート対象ではなく、機能が制限されることもあります。また、Azure の場所によっては、利用できない場合もあります。 この機能の可用性とステータスに関する最新の通知については、Azure Virtual Network の更新情報に関するページをご覧ください。

## <a name="benefits"></a>メリット
* **待機時間の短縮 / 1 秒あたりのパケット数 (pps)の向上:** データパスから仮想スイッチを削除することで、ホストにおけるパケットのポリシー処理に必要な時間がなくなるため、VM 内で処理できるパケット数が増加します。
* **ジッターの削減:** 仮想スイッチの処理は、適用するポリシーの量と、処理を行う CPU のワークロードによって異なります。 ハードウェアへのポリシーの適用をオフロードすると、パケットが直接 VM に配信され、ホストと VM 間の通信とソフトウェアによる干渉やコンテキスト スイッチがなくなるため、そのばらつきはなくなります。
* **CPU 使用率の削減:** ホストの仮想スイッチをバイパスすることによって、ネットワーク トラフィックを処理するための CPU の使用率を軽減できます。

## <a name="Limitations"></a>制限事項
この機能を使用する場合は、次の制限事項があります。

* **ネットワーク インターフェイスの作成:** 高速ネットワークは、新しい NIC でのみ有効にできます。 既存の NIC に対して有効にすることはできません。
* **VM の作成:** 高速ネットワークを有効にした NIC は、VM の作成時にのみ VM に接続できます。 既存の VM に NIC を接続することはできません。
* **リージョン:** 高速ネットワークを使用した Windows VM は、ほとんどの Azure リージョンで提供されています。 高速ネットワークを使用した Linux VM は、複数のリージョンで提供されています。 この機能が利用できるリージョンは拡大しています。 最新情報については、Azure 仮想ネットワークの更新ブログを参照してください。   
* **サポートされているオペレーティング システム:** Windows では、Microsoft Windows Server 2012 R2 Datacenter と Windows Server 2016 がサポートされます。 Linuxでは、Ubuntu Server 16.04 LTS (カーネル 4.4.0-77 以降)、SLES 12 SP2、RHEL 7.3 と CentOS 7.3 (Rogue Wave Software が公開) がサポートされます。
* **VM サイズ:** "汎用" タイプと "コンピューティングの最適化" タイプのインスタンス サイズ (8 コア以上)。 詳細については、[Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM サイズに関する記事を参照してください。 サポートされる VM のインスタンス サイズは、今後増える予定です。
* **Azure Resource Manager (ARM) のみによるデプロイ:** ASM/RDFE によるデプロイでは高速ネットワークを使用できません。

これらの制限に対する変更については、[Azure 仮想ネットワークの更新情報](https://azure.microsoft.com/updates/accelerated-networking-in-preview)に関するページでお知らせします。

## <a name="create-a-windows-vm"></a>Windows VM の作成
VM の作成には、Azure Portal または Azure [PowerShell](#windows-powershell) を使用できます。

### <a name="windows-portal"></a>ポータル

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)にサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
2. ポータルで、**[+ 新規]** > **[Compute]** > **[Windows Server 2016 Datacenter]** の順にクリックします。 
3. **[Windows Server 2016 Datacenter]** ブレードが表示されたら、**[デプロイ モデルの選択]** の *[リソース マネージャー]* を選択状態のままにし、**[作成]** をクリックします。
4. **[基本]** ブレードが表示されたら、以下の値を入力します。それ以外のオプションは既定値のままにするか、独自の値を選択または入力して、**[OK]** ボタンをクリックします。

    |Setting|値|
    |---|---|
    |名前|MyVm|
    |リソース グループ|**[新規作成]** を選択して「*MyResourceGroup*」と入力します。|
    |場所|米国西部 2|

    Azure を初めてご利用の場合は、[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)、[場所](https://azure.microsoft.com/regions) (リージョン) とは何かを、それぞれのリンク先のページでご覧ください。
5. **[サイズの選択]** ブレードが表示されたら、**[最小コア数]** ボックスに「*8*」と入力し、**[すべて表示]** をクリックします。
6. **[DS4_V2 Standard]** またはサポート対象の任意の VM をクリックし、**[選択]** ボタンをクリックします。
7. **[設定]** ブレードが表示されたら、**[高速ネットワーク]** で **[有効]** をクリックし、それ以外の設定はすべてそのままにして、**[OK]** ボタンをクリックします。 **注:** これまでの手順で、VM サイズ、オペレーティング システム、または場所に、この記事の「[制限事項](#Limitations)」セクションに記載されていない値を選択した場合、**[高速ネットワーク]** は表示されません。
8. **[概要]** ブレードが表示されたら、**[OK]** ボタンをクリックします。 Azure によって VM の作成が開始されます。 VM の作成には数分かかります。
9. Windows 用の高速ネットワークのドライバーをインストールするには、この記事の「[Windows の構成](#configure-windows)」セクションの手順を実行します。

### <a name="windows-powershell"></a>PowerShell
1. Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をお読みください。
2. Windows の [スタート] ボタンをクリックして「**powershell**」と入力し、検索結果の **[PowerShell]** をクリックして PowerShell セッションを開始します。
3. PowerShell ウィンドウで `login-azurermaccount` コマンドを入力し、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)でサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
4. ブラウザーで次のスクリプトをコピーします。
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. PowerShell ウィンドウ内で右クリックし、先ほどのスクリプトを貼り付けて実行します。 ユーザー名とパスワードの入力が求められます。 次の手順で VM に接続する際は、これらの資格情報を使ってログインします。 スクリプト内の値を変更してからスクリプトを実行した結果、エラーが発生した場合、VM サイズ、オペレーティング システム、場所に使用した値が、この記事の「[制限事項](#Limitations)」セクションに記載されていることを確認してください。
6. Windows 用の高速ネットワークのドライバーをインストールするには、この記事の「[Windows の構成](#configure-windows)」セクションの手順を実行します。

### <a name="configure-windows"></a>Windows の構成
Azure に VM を作成したら、Windows 用の高速ネットワークのドライバーをインストールする必要があります。 以下の手順を実行する前に、この記事の「[ポータル](#windows-portal)」または「[PowerShell](#windows-powershell)」の手順に従って、高速ネットワークを使用した Windows VM を作成しておいてください。 

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)にサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
2. Azure Portal の上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに「*MyVm*」と入力します。 検索結果に **MyVm** が表示されたら、それをクリックします。
3. **[MyVm]** ブレードが表示されたら、その左上隅に表示される **[接続]** ボタンをクリックします。 **注:** **[接続]** ボタンの下に **[作成中]** と表示されている場合、Azure による VM の作成がまだ完了していません。 **[接続]** は必ず、**[接続]** ボタンの下の **[作成中]** が見えなくなってからクリックしてください。
4. **MyVm.rdp** ファイルのダウンロードをブラウザーに許可します。  ダウンロードが完了したら、そのファイルをクリックして開きます。 
5. リモート接続の発行元が特定できないことを伝える **[リモート デスクトップ接続]** ボックスが表示されたら、**[接続]** ボタンをクリックします。
6. **[Windows セキュリティ]** ボックスが表示されたら、**[その他]** をクリックし、**[別のアカウントを使う]** をクリックします。 手順 4. で入力したユーザー名とパスワードを入力し、**[OK]** ボタンをクリックします。
7. リモート コンピューターの ID が確認できないことを伝える [リモート デスクトップ接続] ボックスが表示されたら、**[はい]** ボタンをクリックします。
8. Windows の [スタート] ボタンを右クリックし、**[デバイス マネージャー]** をクリックします。 **[ネットワーク アダプター]** ノードを展開します。 **[Mellanox ConnectX-3 Virtual Function Ethernet Adapter]** が表示されていることを確認します (下図参照)。
   
    ![[デバイス マネージャー]](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. VM の高速ネットワークが有効になりました。

## <a name="create-a-linux-vm"></a>Linux VM の作成
Ubuntu または SLES VM の作成には、Azure Portal または Azure [PowerShell](#linux-powershell) を使用できます。 RHEL VM および CentOS VM には、別のワークフローがあります。  次の手順を確認してください。

### <a name="linux-portal"></a>ポータル
1. この記事の「[Linux VM の作成 - PowerShell](#linux-powershell)」セクションの手順. 1 ～ 5. に従って、Linux プレビューの高速ネットワークの利用登録を行います。  プレビューの利用登録をポータルから行うことはできません。
2. この記事の「[Windows VM の作成 - ポータル](#windows-portal)」セクションの手順 1. ～ 8. の手順を実行します。 手順 2. では、**[Windows Server 2016 Datacenter]** の代わりに **[Ubuntu Server 16.04 LTS]** をクリックしてください。 このチュートリアルでは、SSH キーではなくパスワードを使用しますが、運用環境のデプロイでは、どちらでも使用できます。 この記事の「[Windows VM の作成 - ポータル](#windows-portal)」セクションの手順 7. で **[高速ネットワーク]** が表示されない場合、次のいずれかの理由が考えられます。
    - プレビューの利用登録が済んでいない。 この記事の「[Linux VM の作成 - Powershell](#linux-powershell)」セクションの手順 4. で説明したように、登録状態が **[登録済み]** になっていることを確認してください。 **注:** 過去に Windows VM プレビューの高速ネットワークに加入した場合 (Windows VM の高速ネットワークの場合、現在、利用登録は不要です)、Linux VM プレビューの高速ネットワークの利用登録は自動的には行われません。 Linux VM プレビューの高速ネットワークに加入するためには、その利用登録を行う必要があります。
    - 選択した VM サイズ、オペレーティング システム、場所のいずれかが、この記事の「[制限事項](#limitations)」セクションに記載された内容と異なる。
3. Linux 用の高速ネットワークのドライバーをインストールするには、この記事の「[Linux の構成](#configure-linux)」セクションの手順を実行します。

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>高速ネットワークを使用した Linux VM をサブスクリプションに作成した後、同じサブスクリプションに高速ネットワークを使用した Windows VM を作成しようとすると、Windows VM の作成に失敗する場合があります。 このプレビューの期間中は、高速ネットワークを使用した Linux VM と高速ネットワークを使用した Windows VM とを別々のサブスクリプションでテストすることをお勧めします。
>

1. Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) モジュールの最新バージョンをインストールします。 Azure PowerShell を初めてお使いの方は、[Azure PowerShell の概要](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をお読みください。
2. Windows の [スタート] ボタンをクリックして「**powershell**」と入力し、検索結果の **[PowerShell]** をクリックして PowerShell セッションを開始します。
3. PowerShell ウィンドウで `login-azurermaccount` コマンドを入力し、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)でサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
4. 以下の手順に従って、Azure プレビューの高速ネットワークの利用登録を行います。
    - Azure サブスクリプション ID と意図した用途を記載した電子メールを [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) に送信します。 サブスクリプションの有効化に関する Microsoft から確認の電子メールをお待ちください。
    - 次のコマンドを入力して、プレビューの利用登録が完了したことを確認します。
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        上記のコマンドを入力して返される出力結果に "**Registered (登録済み)**" と表示されるまでは、手順 5. に進まないでください。 次のような結果が出力されたときに初めて先に進むことができます。
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >過去に Windows VM プレビューの高速ネットワークに加入した場合 (Windows VM の高速ネットワークの場合、現在、利用登録は不要です)、Linux VM プレビューの高速ネットワークの利用登録は自動的には行われません。 Linux VM プレビューの高速ネットワークに加入するためには、その利用登録を行う必要があります。
      >
5. ブラウザーでは、必要に応じて、Ubuntu または SLES を置き換えて、次のスクリプトをコピーします。  ここでも、Redhat と CentOS には、以下に説明する別のワークフローがあります。

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. PowerShell ウィンドウ内で右クリックし、先ほどのスクリプトを貼り付けて実行します。 ユーザー名とパスワードの入力が求められます。 次の手順で VM に接続する際は、これらの資格情報を使ってログインします。 スクリプトでエラーが発生した場合は、次の点を確認してください。
    - 手順 4. の説明に従ってプレビューの利用登録が済んでいること。
    - VM サイズ、オペレーティング システムの種類、場所のいずれかの値を変更してからスクリプトを実行した場合、それらの値が、この記事の「[制限事項](#Limitations)」セクションに記載されていること。
7. Linux 用の高速ネットワークのドライバーをインストールするには、この記事の「[Linux の構成](#configure-linux)」セクションの手順を実行します。

### <a name="configure-linux"></a>Linux の構成

Azure に VM を作成したら、Linux 用の高速ネットワークのドライバーをインストールする必要があります。 以下の手順を実行する前に、この記事の「[ポータル](#linux-portal)」または「[PowerShell](#linux-powershell)」の手順に従って、高速ネットワークを使用した Linux VM を作成しておいてください。 

1. インターネット ブラウザーから Azure [Portal](https://portal.azure.com) を開いて、ご利用の Azure [アカウント](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)にサインインします。 まだアカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)にサインアップできます。
2. ポータルの上部で、*[リソースの検索]* バーの右側にある **[>_]** アイコンをクリックして Bash Cloud Shell (プレビュー) を起動します。 ポータルの下部に Bash Cloud Shell ウィンドウが表示され、数秒後、**username@Azure:~$** プロンプトが表示されます。 VM には、Cloud Shell ではなく SSH でご利用のコンピューターから接続することもできますが、このチュートリアルの手順では、Cloud Shell の使用を想定しています。
3. ポータルの上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに「*MyVm*」と入力します。 検索結果に **MyVm** が表示されたら、それをクリックします。
4. **[MyVm]** ブレードが表示されたら、その左上隅に表示される **[接続]** ボタンをクリックします。 **注:** **[接続]** ボタンの下に **[作成中]** と表示されている場合、Azure による VM の作成がまだ完了していません。 **[接続]** は必ず、**[接続]** ボタンの下の **[作成中]** が見えなくなってからクリックしてください。
5. `ssh adminuser@<ipaddress>` の入力を求めるボックスが表示されます。 このコマンドを Cloud Shell に入力し (または、手順 4. で表示されたボックスからコピーして Cloud Shell に貼り付け)、Enter キーを押します。
6. 接続を続行するかどうかを確認する質問に対して「**yes**」と入力し、Enter キーを押します。
7. VM の作成時に入力したパスワードを入力します。 VM に正常にログインすると、adminuser@MyVm:~$ プロンプトが表示されます。 これで Cloud Shell セッションを通じて VM にログインした状態となります。 **注:** Cloud Shell セッションは、無操作状態が 10 分を超えるとタイムアウトになります。

この時点では、使用しているディストリビューションに応じて手順が異なります。 

#### <a name="ubuntusles"></a>Ubuntu/SLES

1. プロンプトで、`uname -r` と入力し、バージョンを確認します。

    * Ubuntu は "4.4.0-77-generic、" 以上
    * SLES は "4.4.59-92.20-default" 以上

2. 以下のコマンドを実行して、標準的なネットワーク vNIC と高速ネットワークの vNIC との間にボンディングを作成します。 ネットワーク トラフィックには、パフォーマンス面で有利な高速ネットワークの vNIC が使用されますが、何らかの構成に変更が生じても、ボンディングによって、ネットワーク トラフィックの中断が確実に防止されます。
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. スクリプトを実行した後、60 秒間一時停止した後に、VM が再起動します。
4. VM が再起動したら、もう一度手順 5. ～ 7. を実行して VM に再接続します。
5. `ifconfig` コマンドを実行して、bond0 が表示されていること、また、このインターフェイスが UP として表示されていることを確認します。 
 
 >[!NOTE]
      >高速ネットワークを使用するアプリケーションは、*eth0* ではなく *bond0* インターフェイスを介して通信を行う必要があります。  このインターフェイス名は、高速ネットワークの一般提供が始まる前に変更される可能性があります。

#### <a name="rhelcentos"></a>RHEL/CentOS

Red Hat Enterprise Linux または CentOS 7.3 VM を作成するには、SR-IOV とネットワーク カードの仮想機能 (VF) ドライバーに必要な最新のドライバーをロードする特別な手順が必要です。 手順の最初のフェーズでは、ドライバーが事前にロードされている 1 つまたは複数の仮想マシンの作成に使用できるイメージを準備します。

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>フェーズ 1: Red Hat Enterprise Linux または CentOS 7.3 の基本イメージの準備。 

1.  Azure の非-SRIOV CentOS 7.3 の VM をプロビジョニングします

2.  LIS 4.2.2 をインストールします。
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  構成ファイルをダウンロードします
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  この VM のプロビジョニングを解除します

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  Azure ポータルから、この VM を停止し、VM の「ディスク」に移動し、OSDisk の VHD URI をキャプチャします。 この URI には、基本イメージの VHD の名前とそのストレージ アカウントが含まれています。 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>フェーズ 2: Azure で新しい VM のプロビジョニング

1.  フェーズ 1 でキャプチャした基本イメージの VHD を使用して新しい AzureRMVMConfig に基づく新しい VM をプロビジョニングします。このとき、vNIC で AcceleratedNetworking を有効にします。

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  VM が起動したら、"lspci"によって VF デバイスを確認し、Mellanox エントリを確認します。 たとえば、lspci 出力内でこの項目を参照する必要があります。
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  結合スクリプトを実行します。

    ```bash
    sudo bondvf.sh
    ```

4.  新しい VM を再起動します。

    ```bash
    sudo reboot
    ```

bond0 が構成され、高速のネットワーク パスが有効になっている状態で VM が開始されます。  `ifconfig` を実行して確認します。

