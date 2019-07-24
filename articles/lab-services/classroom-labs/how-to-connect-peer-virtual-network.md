---
title: Azure Lab Services でピア ネットワークに接続する | Microsoft Docs
description: ラボのネットワークと別のネットワークをピアとして接続する方法について説明します。 たとえば、オンプレミスの学校/大学のネットワークと Azure のラボの仮想ネットワークを接続します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 465352af52cbc84773e52782233065b3000921e7
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652875"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Azure Lab Services でラボのネットワークとピア仮想ネットワークを接続する 
この記事では、ラボのネットワークと別のネットワークのピアリングに関する情報を提供します。 

## <a name="overview"></a>概要
仮想ネットワーク ピアリングを使用すると、Azure 仮想ネットワーク間をシームレスに接続できます。 ピアリングされた仮想ネットワークは、接続において、見かけ上 1 つのネットワークとして機能します。 ピアリングされた仮想ネットワークに存在する仮想マシン間のトラフィックは Microsoft のバックボーン インフラストラクチャを介して、"プライベート" IP アドレスのみを使った同一仮想ネットワーク内の仮想マシン間のトラフィックと同じようにルーティングされます。 詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」をご覧ください。

次のようなシナリオでは、ラボのネットワークとピア仮想ネットワークの接続が必要なことがあります。

- ラボ内の仮想マシンに、ライセンスを取得するためにオンプレミスのライセンス サーバーに接続するソフトウェアが含まれている
- ラボ内の仮想マシンが、大学のネットワーク共有上のデータ セット (またはその他のすべてのファイル) にアクセスする必要がある 

特定のオンプレミスのネットワークは [ExpressRoute](../../expressroute/expressroute-introduction.md) または [Virtual Network ゲートウェイ](../../vpn-gateway/vpn-gateway-about-vpngateways.md)のどちらかを使用して Azure Virtual Network に接続しています。 これらのサービスは、Azure Lab Services の外部で設定する必要があります。 ExpressRoute を使用してオンプレミスのネットワークを Azure に接続する方法の詳細については、[ExpressRoute の概要] (../expressroute/expressroute-introduction.md) を参照してください。 Virtual Network ゲートウェイを使用してオンプレミスと接続するには、ゲートウェイ、指定された仮想ネットワーク、ラボのアカウントがすべて同じリージョンにある必要があります。


## <a name="configure-at-the-time-of-lab-account-creation"></a>ラボ アカウントの作成時に構成する
新しいラボ アカウントの作成中に、**[仮想ネットワークのピアリング]** ドロップダウン リストに表示される既存の仮想ネットワークを選択することができます。 選択した仮想ネットワークが、そのラボ アカウントによって作成されたラボに接続 (ピアリング) されます。 この変更を行った後に作成されるラボのすべての仮想マシンが、ピアリングされた仮想ネットワーク上のリソースにアクセスできます。 

![ピアリングする VNet を選択する](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> ラボ アカウントを作成する詳細な手順については、[ラボ アカウントの設定](tutorial-setup-lab-account.md)に関する記事を参照してください


## <a name="configure-after-the-lab-is-created"></a>ラボの作成後に構成する
ラボ アカウントの作成時にピア ネットワークを設定しなかった場合、**ラボ アカウント**のページの **[Labs configuration]\(ラボ構成\)** タブから同じプロパティを有効にすることができます。 この設定に加えられた変更は、変更後に作成されたラボにのみ適用されます。

![ラボの作成後に VNet のピアリングを有効または無効にする](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

画像のように、ラボ アカウントでラボに対して **[仮想ネットワークのピアリング]** を有効または無効にすることができます。 

> [!IMPORTANT]
> この設定の変更は、変更後に作成されたラボにのみ適用され、既存のラボには適用されません。 



## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)

