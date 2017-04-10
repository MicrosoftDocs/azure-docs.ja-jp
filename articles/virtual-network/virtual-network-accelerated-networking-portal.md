---
title: "仮想マシンの Accelerated Networking - Portal | Microsoft Docs"
description: "Azure Portal を使用して Azure 仮想マシンで Accelerated Networking を構成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: af4515c6-4377-4d4a-a104-18fe1348252c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: a2e8e0dc40a63c363f295149e35f9823c8e90fa8
ms.lasthandoff: 02/28/2017


---
# <a name="accelerated-networking-for-a-virtual-machine-using-the-azure-portal"></a>Azure Portal を使用した仮想マシンの Accelerated Networking
> [!div class="op_single_selector"]
> * [Azure ポータル](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

Accelerated Networking によって、仮想マシン (VM) でシングルルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされる VM タイプの最も要件の厳しいネットワーク ワークロードで使用した場合でも、待機時間、ジッター、CPU 使用率が軽減されます。 この記事では、Azure Resource Manager デプロイメント モデルで Azure Portal を使用して、Accelerated Networking を構成する方法について説明します。 VM は、Azure PowerShell で Accelerated Networking を使用して作成することもできます。 その方法は、この記事の上部にある PowerShell ボックスをクリックすると表示されます。

次の図は、Accelerated Networking を使用した仮想マシンと、使用していない仮想マシンの間の通信を表しています。

![比較](./media/virtual-network-accelerated-networking-portal/image1.png)

Accelerated Networking を使用しない場合は、VM に出入りするすべてのネットワーク トラフィックがホストと仮想スイッチをスキャンする必要があります。 仮想スイッチでは、ネットワーク セキュリティ グループ、アクセス制御リスト、分離、その他のネットワーク仮想化サービスなど、すべてのポリシーが適用されます。 詳細については、「 [Hyper-V Network Virtualization and Virtual Switch (HYPER-V ネットワーク仮想化と仮想スイッチ)](https://technet.microsoft.com/library/jj945275.aspx) 」の記事をご覧ください。

Accelerated Networking を使用したネットワーク トラフィックは、ネットワーク カード (NIC) に到達してから VM に転送されます。 Accelerated Networking がない場合に仮想スイッチによって適用されるネットワークポリシーはすべてオフロードされ、ハードウェアに適用されます。 ハードウェアにポリシーを適用することによって、ホストに適用されるポリシーをすべて維持したまま、ホストや仮想スイッチをバイパスして、NIC からネットワーク トラフィックを直接 VM に転送できます。

VM で Accelerated Networking が有効になっている場合のみ、そのメリットを受けられます。 最適な結果を得るには、同じ VNet に接続された&2;台以上の VM でこの機能を有効にしておくことをお勧めします。 複数の VNet またはオンプレミスの間で通信する場合、全体的な待機時間に対してこの機能が与える影響は最小限のものになります。

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
1. プレビューに登録するには、 [Accelerated Networkのサブスクリプション係](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) まで、メールでサブスクリプション ID と使用目的をご連絡ください。 プレビューへの登録が受諾されたことを通知する電子メールを受け取るまでは、残りの手順は実行しないでください。
2. Azure Portal (http://portal.azure.com) にログインします。
3. 「[Windows VM の作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」の記事にある手順で次のオプションを選択し、VM を作成します。
   
   * この記事の「制限事項」セクションにあるオペレーティング システムを選択します。
   * この記事の「制限事項」セクションにある場所 (リージョン) を選択します。
   * この記事の「制限事項」セクションにあるVM サイズを選択します。 サポートされているのに表示されないサイズがある場合は、**[サイズの選択]**ブレードで **[すべて表示]** をクリックし、展開されたリストからサイズを選びます。
   * **[設定]** ブレードで、下図のように **[Accelerated networking]** で *[有効]* をクリックします。
     
       ![[設定]](./media/virtual-network-accelerated-networking-portal/image3.png)
     
     > [!NOTE]
     > Accelerated Networking のオプションは、次の場合にのみ表示されます。
     > 
     > * プレビューへの登録が受諾されている。
     > * この記事の「制限事項」セクションに記載されている、サポートされるオペレーティング システム、場所、VM のサイズを選択している。
     > 
     > 
4. VM を作成したら、 [Accelerated Networking ドライバー](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)をダウンロードし、VM に接続してログインし、VM 内でドライバーのインストーラーを実行します。
5. Windows ボタンを右クリックし、 **[デバイス マネージャー]**をクリックします。 次の図のように、**[ネットワーク]** オプションを展開したときに、**Mellanox ConnectX-3 Virtual Function Ethernet Adapter** が表示されることを確認します。
   
    ![[デバイス マネージャー]](./media/virtual-network-accelerated-networking-portal/image2.png)


