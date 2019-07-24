---
title: IP アドレス 168.63.129.16 とは | Microsoft Docs
description: IP アドレス 168.63.129.16 と、これがリソースでどのように動作するかについて説明します。
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: genli
ms.openlocfilehash: 7f0539e7c2f7e5ae8847b35b47d3708c6c5d6a09
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296040"
---
# <a name="what-is-ip-address-1686312916"></a>IP アドレス 168.63.129.16 とは

IP アドレス 168.63.129.16 は、Azure プラットフォーム リソースへの通信チャネルの使用を容易にするために使用される仮想パブリック IP アドレスです。 お客様は、Azure でプライベート仮想ネットワーク用に任意のアドレス空間を定義できます。 そのため、Azure プラットフォーム リソースは、一意のパブリック IP アドレスとして提供される必要があります。 この仮想パブリック IP アドレスによって、次のことが容易になります。

- VM エージェントが Azure プラットフォームと通信して、それが "準備完了" 状態にあることを通知できるようにする。
- カスタム DNS サーバーが存在しないリソース (VM など) にフィルター処理された名前解決を提供するために、DNS 仮想サーバーとの通信を有効にする。 このフィルター処理により、お客様が自分のリソースのホスト名だけを解決できるようになります。
- ロード バランサーの正常性プローブが負荷分散セット内の VM の正常性状態を特定できるようにする。
- PaaS ロールに対するゲスト エージェントのハートビート メッセージを有効にする。

## <a name="scope-of-ip-address-1686312916"></a>IP アドレス 168.63.129.16 のスコープ

仮想パブリック IP アドレス 168.63.129.16 は、すべてのリージョンおよびすべての国内クラウドで使用されます。 この特殊なパブリック IP アドレスは変更されません。 これは、既定のネットワーク セキュリティ グループの規則によって許可されています。 この IP アドレスは、すべてのローカル ファイアウォール ポリシーで許可することをお勧めします。 この特殊な IP アドレスからメッセージを受信できるのは内部 Azure プラットフォームだけであるため、この IP アドレスとリソースの間の通信は安全です。 このアドレスがブロックされると、さまざまなシナリオで予期しない動作が発生する場合があります。

さらに、トラフィックが仮想パブリック IP アドレス 168.63.129.16 から [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)用に構成されたエンドポイントに流れることを期待できます。 仮想ネットワーク以外のシナリオでは、正常性プローブのソースはプライベート IP です。 

## <a name="next-steps"></a>次の手順

- [セキュリティ グループ](security-overview.md)
- [ネットワーク セキュリティ グループの作成、変更、または削除](manage-network-security-group.md)
