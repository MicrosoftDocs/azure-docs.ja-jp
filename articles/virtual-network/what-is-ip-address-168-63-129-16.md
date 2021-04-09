---
title: IP アドレス 168.63.129.16 とは | Microsoft Docs
description: IP アドレス 168.63.129.16 について、特にこの IP アドレスが Azure プラットフォーム リソースへの通信チャネルの使用を容易にするために使用されることについて説明します。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 1e304bc30a48c92fdff576723dae6af1e26ef3da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222583"
---
# <a name="what-is-ip-address-1686312916"></a>IP アドレス 168.63.129.16 とは

IP アドレス 168.63.129.16 は、Azure プラットフォーム リソースへの通信チャネルの使用を容易にするために使用される仮想パブリック IP アドレスです。 お客様は、Azure でプライベート仮想ネットワーク用に任意のアドレス空間を定義できます。 そのため、Azure プラットフォーム リソースは、一意のパブリック IP アドレスとして提供される必要があります。 この仮想パブリック IP アドレスによって、次のことが容易になります。

- VM エージェントが Azure プラットフォームと通信して、それが "準備完了" 状態にあることを通知できるようにする。
- カスタム DNS サーバーが存在しないリソース (VM など) にフィルター処理された名前解決を提供するために、DNS 仮想サーバーとの通信を有効にする。 このフィルター処理により、お客様が自分のリソースのホスト名だけを解決できるようになります。
- [Azure Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)が VM の正常性状態を特定できるようにする。
- VM が、Azure の DHCP サービスから動的 IP アドレスを取得できるようにします。
- PaaS ロールに対するゲスト エージェントのハートビート メッセージを有効にする。

> [!NOTE]
> 非仮想ネットワーク シナリオ (クラシック) の場合、168.63.129.16 ではなくプライベート IP アドレスが使用されます。 このプライベート IP アドレスは、DHCP を介して動的に検出されます。 168.63.129.16 に固有のファイアウォール規則は、必要に応じて調整する必要があります。

## <a name="scope-of-ip-address-1686312916"></a>IP アドレス 168.63.129.16 のスコープ

パブリック IP アドレス 168.63.129.16 は、すべてのリージョンおよびすべての国内クラウドで使用されます。 この特殊なパブリック IP アドレスは Microsoft が所有しており、変化しません。 (VM 内のアウトバウンド方向の) ローカル ファイアウォール ポリシーで、この IP アドレスを許可することをお勧めします。 この特殊な IP アドレスからメッセージを受信できるのは内部 Azure プラットフォームだけであるため、この IP アドレスとリソースの間の通信は安全です。 このアドレスがブロックされると、さまざまなシナリオで予期しない動作が発生する場合があります。 168.63.129.16 は[ホスト ノードの仮想 IP](./network-security-groups-overview.md#azure-platform-considerations) であるため、ユーザー定義ルートの対象にはなりません。

- VM エージェントでは、ポート 80/tcp と 32526/tcp を介した WireServer (168.63.129.16) とのアウトバウンド通信が必要です。 これらは、VM 上のローカル ファイアウォールでは開いている必要があります。 これらのポート上での 168.63.129.16 との通信は、構成されたネットワーク セキュリティ グループの対象ではありません。

- 168.63.129.16 では、VM に DNS サービスを提供できます。 これが望ましくない場合は、168.63.129.16 ポート 53/udp および 53/tcp へのアウトバウンド トラフィックを VM 上のローカル ファイアウォールでブロックできます。

  既定では、[AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) サービス タグを活用して具体的にターゲットを絞らない限り、DNS 通信は構成されたネットワーク セキュリティ グループの対象になりません。 NSG 経由での Azure DNS への DNS トラフィックをブロックするには、[AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) へのトラフィックを拒否するアウトバウンド規則を作成し、"宛先ポート範囲" として "*" を、プロトコルとして "すべて" を指定します。

- VM がロード バランサー バックエンド プールの一部である場合、[正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)通信を、168.63.129.16 から発信できるようにする必要があります。 既定のネットワーク セキュリティ グループの構成には、この通信を許可する規則があります。 この規則では、[AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) サービス タグが活用されます。 必要に応じて、このトラフィックは、ネットワーク セキュリティ グループを構成することによってブロックできます。ただし、これによって失敗するプローブが発生します。

## <a name="next-steps"></a>次のステップ

- [セキュリティ グループ](./network-security-groups-overview.md)
- [ネットワーク セキュリティ グループの作成、変更、または削除](manage-network-security-group.md)