---
title: Azure 仮想ネットワークでのパブリック IPv6 アドレスのプレフィックス
titlesuffix: Azure Virtual Network
description: Azure 仮想ネットワークでのパブリック IPv6 アドレスのプレフィックスについて説明します。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3e3d3c01ba1190871da39ce27de6a95952d09be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99538208"
---
# <a name="reserved-public-ipv6-address-prefix"></a>予約されたパブリック IPv6 アドレスのプレフィックス

Azure 内のデュアル スタック (IPv4+IPv6) 仮想ネットワーク (VNet) と仮想マシン (VM) は、既定ではインターネットに接続されていないため、安全な状態にあります。 Azure から取得したパブリック IPv6 アドレスを持つ Azure Load Balancer および VM には IPv6 インターネット接続を簡単に追加できます。

予約したパブリック IP はいずれも、任意の Azure リージョンと、さらにご利用の Azure サブスクリプションと関連付けられます。 予約済み (静的) の IPv6 パブリック IP は、サブスクリプション内の任意の Azure Load Balancer 間または VM 間で移動できます。 IPv6 パブリック IP の関連付けは完全に解除できますが、IPv6 パブリック IP は準備が整ったときに使用できるように保持されます。

> [!WARNING]
> パブリック IP アドレスを誤って削除しないように注意してください。 パブリック IP を削除すると、サブスクリプションからそれが取り除かれます。これを回復することはできません (Azure サポートの支援を受ける場合でも)。

個々の IPv6 アドレスを予約することに加え、Azure IPv6 アドレスの連続する範囲 (IP プレフィックスと呼ばれます) を予約して使用することもできます。  個別の IP アドレスと同様に、予約されたプレフィックスは、任意の Azure リージョンおよびご利用の Azure サブスクリプションに関連付けられます。 予測可能な連続した範囲のアドレスを予約することには、多くの用途があります。 たとえば、企業と顧客によって行われる、Azure でホストされているアプリケーションの IP の "*フィルター処理*" を大幅に簡素化できます。これは、静的 IP の範囲をオンプレミスのファイアウォールに簡単にプログラミングできるからです。  ご利用の IP プレフィックスから、必要に応じて、個々のパブリック IP を作成することができます。また、これらのパブリック IP を削除した場合、後で再利用できるように予約範囲に "*返されます*"。 ご利用の IP プレフィックス内の IP アドレスはすべて、そのプレフィックスを削除するまで、ご自分専用に予約されています。



## <a name="ipv6-prefix-sizes"></a>IPv6 プレフィックスのサイズ
次のパブリック IP プレフィックスのサイズを使用できます。

-  IPv6 プレフィックスの最小サイズ: /127 = 2 アドレス
-  IPv6 プレフィックスの最大サイズ: /124 = 16 アドレス

プレフィックスのサイズは、クラスレス ドメイン間ルーティング (CIDR) マスク サイズとして指定されます。 たとえば、/128 のマスクの場合、IPv6 アドレスが 128 ビットで構成されるため、個々の IPv6 アドレスを表します。

## <a name="pricing"></a>価格
 
Azure パブリック IP の使用 (個々の IP アドレスと IP の範囲の両方) に関連するコストについては、「[パブリック IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses/)」をご覧ください。

## <a name="limitations"></a>制限事項
IPv6 は、Basic パブリック IP での "動的" 割り当てのみでサポートされます。つまり、Azure 内でご自分のアプリケーション (VM またはロード バランサー) を削除して再デプロイすると、IPv6 アドレスは変更されます。 Standard IPv6 パブリック IP では静的 (予約済み) 割り当てのみがサポートされますが、Standard 内部ロード バランサーでは、割り当て先となるサブネット内からの動的割り当てもサポートできます。  

ベスト プラクティスとして、IPv6 アプリケーションには標準パブリック IP と Standard Load Balancer を使用することをお勧めします。

## <a name="next-steps"></a>次のステップ
- パブリック [IPv6 アドレスのプレフィックス](ipv6-reserve-public-ip-address-prefix.md)を予約します。
- [IPv6 アドレス](ipv6-overview.md)の詳細について学習します。
- Azure で[パブリック IP (IPv4 と IPv6 の両方) を作成し、使用する方法](virtual-network-public-ip-address.md)について学習します。
