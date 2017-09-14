---
title: "Azure Site Recovery の 2 つの Azure リージョン間でのネットワーク マッピング | Microsoft Docs"
description: "Azure Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、回復を調整します。 Azure またはセカンダリ データセンターへのフェールオーバーについて説明します。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: pratshar
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 34f5caa13aa94dc982ed497d7c8ddeceed61a6d8
ms.contentlocale: ja-jp
ms.lasthandoff: 06/15/2017

---
# <a name="network-mapping-between-two-azure-regions"></a>2 つの Azure リージョン間でのネットワーク マッピング


この記事では、2 つの Azure リージョンの Azure 仮想ネットワークを相互にマップする方法について説明します。 ネットワーク マッピングによって、レプリケートされた仮想マシンがターゲットの Azure リージョンに作成されると、その仮想マシンは、ソース仮想マシンの仮想ネットワークにマップされている仮想ネットワークにも作成されます。  

## <a name="prerequisites"></a>前提条件
ネットワークをマップする前に、ソースとターゲットの両方の Azure リージョンで [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)が作成されていることを確認してください。

## <a name="map-networks"></a>ネットワークのマップ

Azure リージョンの Azure 仮想ネットワークを、別のリージョンの仮想ネットワークにマップするには、[Site Recovery インフラストラクチャ]、[ネットワークのマッピング] \(Azure Virtual Machines の場合) の順に移動し、ネットワーク マッピングを作成してください。

![ネットワーク マッピング](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


次の例では、東アジア リージョンで実行されている仮想マシンが、東南アジアにレプリケートされます。

ソースとターゲットのネットワークを選択し、[OK] をクリックして、東アジアから東南アジアへのネットワーク マッピングを作成します。

![ネットワーク マッピング](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


同じ操作を行って、東南アジアから東アジアへのネットワーク マッピングを作成します。  
![ネットワーク マッピング](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>レプリケーションを有効にするときのネットワーク マッピング

Azure リージョン間で仮想マシンを初めてレプリケートするとき、ネットワーク マッピングが行われていない場合は、そのプロセスの一環としてターゲット ネットワークを選択できます。 Site Recovery では、この選択に基づいて、ソース リージョンからターゲット リージョンへのネットワーク マッピングと、ターゲット リージョンからソース リージョンへのネットワーク マッピングが作成されます。   

![ネットワーク マッピング](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

既定では、ソース ネットワークと同じネットワークが、ソース ネットワークの名前の末尾に "-asr" というサフィックスが追加された名前で、ターゲット リージョンに作成されます。 [カスタマイズ] をクリックすると、作成済みネットワークを選択できます。

![ネットワーク マッピング](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


ネットワーク マッピングが既に行われている場合、レプリケーションが有効になっているときに、ターゲット仮想ネットワークを変更することはできません。 これを変更するには、既存のネットワーク マッピングを変更します。  

![ネットワーク マッピング](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![ネットワーク マッピング](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> リージョン 1 からリージョン 2 へのネットワーク マッピングを変更する場合は、リージョン 2 からリージョン 1 へのネットワーク マッピングも必ず変更してください。
>
>


## <a name="subnet-selection"></a>サブネットの選択
ターゲット仮想マシンのサブネットは、ソース仮想マシンのサブネットの名前に基づいて選択されます。 ターゲット ネットワークに、ソース仮想マシンのサブネットと同じ名前のサブネットがある場合は、そのサブネットがターゲット仮想マシンに対して選択されます。 ターゲット ネットワークに同じ名前のサブネットがない場合は、アルファベット順で先にくるサブネットがターゲット サブネットとして選択されます。 このサブネットを変更するには、仮想マシンの [コンピューティングとネットワーク] 設定に移動します。

![サブネットを変更する](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP アドレス

ターゲット仮想マシンの各ネットワーク インターフェイスの IP アドレスは、次のように選択されます。

### <a name="dhcp"></a>DHCP
ソース仮想マシンのネットワーク インターフェイスが DHCP を使用している場合、ターゲット仮想マシンのネットワーク インターフェイスも DHCP として設定されます。

### <a name="static-ip"></a>静的 IP
ソース仮想マシンのネットワーク インターフェイスが静的 IP を使用している場合、ターゲット仮想マシンのネットワーク インターフェイスも静的 IP を使用するように設定されます。 静的 IP は、次のように選択されます。

#### <a name="same-address-space"></a>同じアドレス空間

ソース サブネットとターゲット サブネットのアドレス空間が同じ場合、ターゲット IP は、ソース仮想マシンのネットワーク インターフェイスと同じ IP に設定されます。 同じ IP が使用できない場合は、他の使用できる IP がいくつか、ターゲット IP として設定されます。

#### <a name="different-address-space"></a>異なるアドレス空間

ソース サブネットとターゲット サブネットのアドレス空間が異なる場合、ターゲット IP は、ターゲット サブネットの使用可能な任意の IP として設定されます。

各ネットワーク インターフェイスのターゲット IP を変更するには、仮想マシンの [コンピューティングとネットワーク] 設定に移動します。

## <a name="next-steps"></a>次のステップ

- [Azure VM のレプリケートに関するネットワーク ガイダンス](site-recovery-azure-to-azure-networking-guidance.md)の詳細を確認する。

