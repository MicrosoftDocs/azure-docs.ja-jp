---
title: IP アドレス 168.63.129.16 とは | Microsoft Docs
description: IP アドレス 168.63.129.16 と、これがリソースでどのように動作するかについて説明します。
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
ms.openlocfilehash: 0ea8a8ec1a92a7dbc01dddc175f7116825ba00f9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067774"
---
# <a name="what-is-ip-address-1686312916"></a>IP アドレス 168.63.129.16 とは

IP アドレス 168.63.129.16 は、Azure プラットフォーム リソースへの通信チャネルの使用を容易にするために使用される仮想パブリック IP アドレスです。 お客様は、Azure でプライベート仮想ネットワーク用に任意のアドレス空間を定義できます。 そのため、Azure プラットフォーム リソースは、一意のパブリック IP アドレスとして提供される必要があります。 この仮想パブリック IP アドレスによって、次のことが容易になります。

- VM エージェントが Azure プラットフォームと通信して、それが "準備完了" 状態にあることを通知できるようにする。
- カスタム DNS サーバーが存在しないリソース (VM など) にフィルター処理された名前解決を提供するために、DNS 仮想サーバーとの通信を有効にする。 このフィルター処理により、お客様が自分のリソースのホスト名だけを解決できるようになります。
- [Azure Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)が VM の正常性状態を特定できるようにする。
- VM が、Azure の DHCP サービスから動的 IP アドレスを取得できるようにします。
- PaaS ロールに対するゲスト エージェントのハートビート メッセージを有効にする。

## <a name="scope-of-ip-address-1686312916"></a>IP アドレス 168.63.129.16 のスコープ

パブリック IP アドレス 168.63.129.16 は、すべてのリージョンおよびすべての国内クラウドで使用されます。 この特殊なパブリック IP アドレスは Microsoft が所有しており、変化しません。 これは、既定のネットワーク セキュリティ グループの規則によって許可されています。 インバウンドとアウトバウンド両方向のすべてのローカル ファイアウォール ポリシーで、この IP アドレスを許可することをお勧めします。 この特殊な IP アドレスからメッセージを受信できるのは内部 Azure プラットフォームだけであるため、この IP アドレスとリソースの間の通信は安全です。 このアドレスがブロックされると、さまざまなシナリオで予期しない動作が発生する場合があります。

[Azure Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)は、この IP アドレスから送信されます。 この IP アドレスをブロックした場合、プローブは失敗します。

仮想ネットワーク以外のシナリオ (クラシック) では、正常性プローブのソースはプライベート IP であり、168.63.129.16 は使用されません。

## <a name="next-steps"></a>次の手順

- [セキュリティ グループ](security-overview.md)
- [ネットワーク セキュリティ グループの作成、変更、または削除](manage-network-security-group.md)
