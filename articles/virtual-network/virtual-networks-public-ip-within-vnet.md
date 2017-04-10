---
title: "仮想ネットワークでのパブリック IP アドレスの使用方法"
description: "パブリック IP アドレスが使用されるように仮想ネットワークを構成する方法を学習します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: b6e5dd7c-84ea-491d-8314-88e63a4da108
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6ac3d099430267b33192899dbe9c9c35eb25b02e


---
# <a name="public-ip-address-space-in-a-virtual-network-vnet"></a>Virtual Network (VNet) でのパブリック IP アドレス空間
仮想ネットワーク (VNet) には、パブリックとプライベート (RFC 1918 アドレス ブロック) の両方の IP アドレス空間を含めることができます。 パブリック IP アドレス範囲を追加すると、VNet 内、相互接続された VNet 内、およびオンプレミスの場所からのみ通信可能なプライベート VNet IP アドレス空間の一部として扱われます。

次の図は、パブリックおよびプライベート IP アドレス空間を含む VNet を示しています。

![パブリック IP の概念](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## <a name="how-do-i-add-a-public-ip-address-range"></a>パブリック IP アドレス範囲をどうやって追加するのですか。
パブリック IP アドレス範囲を追加するには、プライベート IP アドレス範囲を追加する場合と同様に、 *netcfg* ファイルを使用するか、 [Azure ポータル](http://portal.azure.com)で構成を追加します。 パブリック IP アドレス範囲は、VNet の作成時に追加することも、後で追加することもできます。 次の例は、同じ VNet 内で構成されたパブリックとプライベートの両方の IP アドレス空間を示しています。

![ポータルでのパブリック IP アドレス](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## <a name="are-there-any-limitations"></a>制限事項はありますか。
許可されていない、いくつかの IP アドレス範囲があります。

* 224.0.0.0/4 (マルチキャスト)
* 255.255.255.255/32 (ブロードキャスト)
* 127.0.0.0/8 (ループバック)
* 169.254.0.0/16 (リンク ローカル)
* 168.63.129.16/32 (内部 DNS)

## <a name="next-steps"></a>次のステップ
[VNet で使用される DNS サーバーを管理する方法](virtual-networks-manage-dns-in-vnet.md)




<!--HONumber=Nov16_HO3-->


