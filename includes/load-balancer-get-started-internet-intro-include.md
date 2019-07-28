---
author: kumudd
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181597"
---
Azure Load Balancer は、第 4 層 (TCP、UDP) のロード バランサーです。 ロード バランサーは、ロード バランサー セット内のクラウド サービスまたは仮想マシンの正常なサービス インスタンスに着信トラフィックを分散することによって高可用性を提供します。 さらに、Azure Load Balancer は、これらのサービスを複数のポート、複数の IP アドレス、またはその両方に提供できます。

ロード バランサーは次の目的で構成できます。

* 仮想マシン (VM) への着信インターネット トラフィックを負荷分散します。 このシナリオのロード バランサーを、 [インターネット接続ロード バランサー](../articles/load-balancer/load-balancer-internet-overview.md)と呼びます。
* 仮想ネットワーク (VNet) 内の VM 間、クラウド サービス内の VM 間、またはクロスプレミスの仮想ネットワーク内のオンプレミス コンピューターと VM 間でトラフィックを負荷分散します。 このシナリオのロード バランサーを、 [内部ロード バランサー (ILB)](../articles/load-balancer/load-balancer-internal-overview.md)と呼びます。
* 外部トラフィックを特定の VM インスタンスに転送します。
