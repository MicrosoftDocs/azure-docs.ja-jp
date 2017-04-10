---
title: "仮想マシンの Accelerated Networking - PowerShell | Microsoft Docs"
description: "PowerShell を使用して Azure 仮想マシンで Accelerated Networking を構成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a0b63599-c23b-40b5-a8ab-23af8b07dded
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 2dd6e9ba1d08d3528cdf8194e1e338422361d1d5
ms.lasthandoff: 02/28/2017


---
# <a name="accelerated-networking-for-a-virtual-machine-using-powershell"></a>PowerShell を使用した仮想マシンの Accelerated Networking
> [!div class="op_single_selector"]
> * [Azure ポータル](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

Accelerated Networking によって、仮想マシン (VM) でシングルルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされる VM タイプの最も要件の厳しいネットワーク ワークロードで使用した場合でも、待機時間、ジッター、CPU 使用率が軽減されます。 この記事では、Azure Resource Manager デプロイメント モデルで Azure PowerShell を使用して、Accelerated Networking を構成する方法について説明します。 VM は、Azure ポータルで Accelerated Networking を使用して作成することもできます。 その方法は、この記事の上部にある Azure Portal ボックスをクリックすると表示されます。

次の図は、Accelerated Networking を使用した仮想マシンと、使用していない仮想マシンの間の通信を表しています。

![比較](./media/virtual-network-accelerated-networking-powershell/image1.png)

Accelerated Networking を使用しない場合は、VM に出入りするすべてのネットワーク トラフィックがホストと仮想スイッチをスキャンする必要があります。 仮想スイッチでは、ネットワーク セキュリティ グループ、アクセス制御リスト、分離、その他のネットワーク仮想化サービスなど、すべてのポリシーが適用されます。 詳細については、「 [Hyper-V Network Virtualization and Virtual Switch (HYPER-V ネットワーク仮想化と仮想スイッチ)](https://technet.microsoft.com/library/jj945275.aspx) 」の記事をご覧ください。

Accelerated Networking を使用したネットワーク トラフィックは、ネットワーク カード (NIC) に到達してから VM に転送されます。 Accelerated Networking がない場合に仮想スイッチによって適用されるネットワークポリシーはすべてオフロードされ、ハードウェアに適用されます。 ハードウェアにポリシーを適用することによって、ホストに適用されるポリシーをすべて維持したまま、ホストや仮想スイッチをバイパスして、NIC からネットワーク トラフィックを直接 VM に転送できます。

VM で Accelerated Networking が有効になっている場合のみ、そのメリットを受けられます。 最適な結果を得るには、同じ VNet に接続された&2;台以上の VM でこの機能を有効にしておくことをお勧めします。  複数の VNet またはオンプレミスの間で通信する場合、全体的な待機時間に対してこの機能が与える影響は最小限のものになります。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>メリット
* **待機時間の短縮 /&1; 秒あたりのパケット数 (pps)の向上:** データパスから仮想スイッチを削除することで、ホストにおけるパケットのポリシー処理に必要な時間がなくなるため、VM 内で処理できるパケット数が増加します。
* **ジッターの削減:** 仮想スイッチの処理は、適用するポリシーの量と、処理を行う CPU のワークロードによって異なります。 ハードウェアへのポリシーの適用をオフロードすると、パケットが直接 VM に配信され、ホストと VM 間の通信とソフトウェアによる干渉やコンテキスト スイッチがなくなるため、そのばらつきはなくなります。
* **CPU 使用率の削減:** ホストの仮想スイッチをバイパスすることによって、ネットワーク トラフィックを処理するための CPU の使用率を軽減できます。

## <a name="limitations"></a>制限事項
この機能を使用する場合は、次の制限事項があります。

* **ネットワーク インターフェイスの作成:** Accelerated Networking は、新しいネットワーク インターフェイスでのみ有効にできます。  既存のネットワーク インターフェイスで有効にすることはできません。
* **VM の作成:** Accelerated Networking を有効にしたネットワーク インターフェイスは、VM の作成時にのみ VM にアタッチできます。 ネットワーク インターフェイスを既存の VM にアタッチすることはできません。
* **リージョン:** 米国中西部と西ヨーロッパの Azure のリージョンでのみ提供されます。 リージョンは今後拡大する予定です。
* **サポートされているオペレーティング システム:** Microsoft Windows Server 2012 R2、Windows Server 2016 テクニカル プレビュー 5。 Linux と Windows Server 2012 もまもなくサポートされる予定です。
* **VM サイズ:** サポートされる VM のインスタンス サイズは、Standard_D15_v2 と Standard_DS15_v2 のみです。 詳細については、「 [Windows VM のサイズ](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 」の記事をご覧ください。 サポートされる VM のインスタンス サイズは、今後増える予定です。

これらの制限に対する変更については、「[Azure 仮想ネットワークの更新情報](https://azure.microsoft.com/updates/accelerated-networking-in-preview)」のページでお知らせします。

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Accelerated Networking を使った Windows VM を作成する
1. PowerShell コマンド プロンプトを開き、1 つの PowerShell セッション内で、このセクションの残りの手順を完了します。 まだ PowerShell をインストール、構成していない場合は、「 [Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs) 」の手順を実行してください。
2. プレビューに登録するには、 [Accelerated Networkのサブスクリプション係](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) まで、メールでサブスクリプション ID と使用目的をご連絡ください。 プレビューへの登録が受諾されたことを通知する電子メールを受け取るまでは、残りの手順は実行しないでください。
3. 次のコマンドを入力し、サブスクリプションに機能を登録します。
   
        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
4. 必要に応じて、 *westcentralus* を、サポートされている別の場所 (この記事の「 [制限](#limitations) 」セクションを参照) の名前で置き換えます。 次のコマンドを入力し、場所の変数を設定します。
   
        $locName = "westcentralus"
5. *RG1* を新しいネットワーク インターフェイスを含んだリソース グループの名前で置き換え、次のコマンドを入力して作成します。
   
        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
6. *VM1-NIC1* を、ネットワーク インターフェイスに付ける名前に変更し、次のコマンドを入力します。
   
        $NICName = "VM1-NIC1"
7. ネットワーク インターフェイスは、ネットワーク インターフェイスと同じ場所、同じ [サブスクリプション](../azure-glossary-cloud-terminology.md#subscription) にある既存 Azure の仮想ネットワーク (VNet) 内のサブネットに接続する必要があります。 VNet の詳細については、「 [仮想ネットワークの概要](virtual-networks-overview.md) 」の記事をご覧ください。 VNet を作成するには、「 [VNet を作成する](virtual-networks-create-vnet-arm-ps.md) 」記事の手順を完了します。 次の $ 変数の "値" を、ネットワーク インターフェイスの接続先である VNet とサブネットの名前に変更します。
   
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
   
    手順 3 で選択した場所にある既存の VNet の名前がわからない場合は、次のコマンドを入力します。
   
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
   
    返される一覧が空の場合は、その場所に VNet を作成する必要があります。 VNet を作成するには、「 [仮想ネットワークを作成する](virtual-networks-create-vnet-arm-ps.md) 」記事の手順を完了します。
   
    VNet 内のサブネットの名前を取得するには、次のコマンドを入力し、上述の *Subnet1* をサブネットの名前に置き換えます。
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix
8. 次のコマンドを入力して VNet とサブネットを取得し、変数に代入します。
   
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }
9. ネットワーク インターフェイスに関連付けられる既存のパブリック IP アドレス リソースがわかれば、インターネット経由で接続できます。 ネットワーク インターフェイスのある VM にインターネット経由でアクセスしない場合は、この手順を省略できます。 パブリック IP アドレスを指定しない場合は、同じ VNet に接続されている別の VM から VM に接続する必要があります。 
   
    *PIP1* を、ネットワーク インターフェイスを作成する場所に存在し、別のネットワーク インターフェイスに関連付けられていない既存のパブリック IP アドレス リソースの名前に変更します。 必要に応じて、$rgName をパブリック IP アドレス リソースのあるリソース グループの名前に変更し、次のコマンドを入力します。
   
        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName
   
    既存のパブリック IP アドレス リソースの名前がわからない場合は、次のコマンドを入力します。
   
        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration
   
    返された出力の **IPConfiguration** 列に値がない場合、パブリック IP アドレス リソースは既存のネットワーク インターフェイスに関連付けられていないため、使用できます。 リストが空または使用できるパブリック IP アドレス リソースがない場合は、New-AzureRmPublicIPAddress コマンドを使用して作成できます。
   
   > [!NOTE]
   > パブリック IP アドレスには、わずかな費用がかかります。 料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。
   > 
   > 
10. インターフェイスにパブリック IP アドレス リソースを追加しない場合は、次に続くコマンドの最後の *- PublicIPAddress $PIP1* を削除します。 次のコマンドを入力して、Accelerated Networking を使ったネットワーク インターフェイスを作成します。
    
        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 
11. 「 [VM を作成する](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 」 記事の手順 3 と 6 の手順に従って VM を作成するときに、ネットワーク インターフェイスを VM に割り当てます。 手順 6-2 で、*Standard_A1* を、この記事の「[制限](#limitations)」セクションにリストされているいずれかの VM のサイズに置き換えます。
    
    > [!NOTE]
    > この記事の $locName、$rgName、$nic の変数の *名前* を変更した場合は、「VM を作成する」記事の手順 6 は使用できません。 ただし、変数の *値* は変更できます。
    > 
    > 
12. VM を作成したら、 [Accelerated Networking ドライバー](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)をダウンロードし、VM に接続して、VM 内でドライバーのインストーラーを実行します。
13. Windows ボタンを右クリックし、 **[デバイス マネージャー]**をクリックします。 次の図のように、**[ネットワーク]** オプションを展開したときに、**Mellanox ConnectX-3 Virtual Function Ethernet Adapter** が表示されることを確認します。
    
    ![[デバイス マネージャー]](./media/virtual-network-accelerated-networking-powershell/image2.png)


