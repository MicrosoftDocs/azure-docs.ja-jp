---
title: Azure の高可用性ポートの概要
titlesuffix: Azure Load Balancer
description: 内部ロード バランサーでの高可用性ポートの負荷分散について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: allensu
ms.openlocfilehash: 89deedd3ef99ba76d0bb133bac37c0acee0a9f73
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275032"
---
# <a name="high-availability-ports-overview"></a>高可用性ポートの概要

内部ロード バランサーを使用するときは、Azure Standard Load Balancer によって、すべてのポートで TCP フローと UDP フローの負荷分散を同時に行うことができます。 

高可用性 (HA) ポート負荷分散規則は負荷分散規則の一種であり、内部 Standard Load Balancer 上に構成されます。 内部 Standard Load Balancer のすべてのポートに到着するすべての TCP フローと UDP フローを負荷分散する単一の規則を指定することで、ロード バランサーの使用を単純化できます。 負荷分散の決定は、フローごとに行われます。 このアクションは、5 タプル接続 (送信元 IP アドレス、送信元ポート、送信先 IP アドレス、送信先ポート、およびプロトコル) に基づいて行われます。

HA ポート負荷分散規則は、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) の高可用性と拡張性のような、重要なシナリオで役に立ちます。 この機能は、多数のポートを負荷分散する必要がある場合にも役立ちます。 

HA ポート負荷分散規則は、フロントエンド ポートとバックエンド ポートを **0** に、プロトコルを**すべて**に設定すると構成されます。 その後は、内部 Load Balancer リソースによって、ポート番号に関係なく、すべての TCP フローおよび UDP フローが負荷分散されます。

## <a name="why-use-ha-ports"></a>HA ポートを使う理由

### <a name="nva"></a>ネットワーク仮想アプライアンス

NVA を使うことで、さまざまなセキュリティの脅威から Azure ワークロードを保護できます。 こうしたシナリオで使用する NVA は、信頼性と可用性に優れ、必要に応じてスケールアウトする必要があります。

NVA インスタンスを内部ロード バランサーのバックエンド プールに追加して、HA ポートのロード バランサー規則を構成するだけで、このような目標を実現できます。

NVA HA のシナリオでは、HA ポートは次の利点を提供します。
- インスタンスごとの正常性プローブによる、正常なインスタンスへの高速フェールオーバーを提供する
- *n* 個のアクティブ インスタンスへのスケールアウトによるパフォーマンスの向上を保証する
- *n* 通りのアクティブおよびアクティブ/パッシブのシナリオを提供する
- アプライアンスを監視するための Apache ZooKeeper ノードのような複雑なソリューションを不要にする

次の図は、ハブとスポークによる仮想ネットワークのデプロイを示したものです。 トラフィックは、スポークによってハブの仮想ネットワークに強制的にトンネリングされ、NVA を通過した後、信頼される空間を離れます。 NVA は、HA ポート構成の内部 Standard Load Balancer の背後にあります。 すべてのトラフィックを、適切に処理して転送できます。 次の図に示すように構成すると、HA ポートの負荷分散規則ではさらに、イングレスおよびエグレス トラフィックのフロー対称を提供できます。

<a node="diagram"></a>
![HA モードで NVA がデプロイされているハブとスポークの仮想ネットワークの図](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> NVA を使用している場合は、HA ポートを最大限に活用し、サポートされているシナリオを理解する方法について、プロバイダーにご確認ください。

### <a name="load-balancing-large-numbers-of-ports"></a>多数のポートの負荷分散

多数のポートの負荷分散が必要なアプリケーションにも、HA ポートを使うことができます。 HA ポートで内部 [Standard Load Balancer ](load-balancer-standard-overview.md) を使うことにより、これらのシナリオが簡単になります。 ポートごとに 1 つずつ、複数の負荷分散規則を使う代わりに、1 つの負荷分散規則で済みます。

## <a name="region-availability"></a>利用可能なリージョン

HA ポート機能は、すべてのグローバル Azure リージョンで使用できます。

## <a name="supported-configurations"></a>サポートされている構成

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>内部 Standard Load Balancer 上の単一の非フローティング IP (非 Direct Server Return) HA ポートの構成

この構成は、基本的な HA ポートの構成です。 次の手順によって、単一フロントエンド IP アドレスでの HA ポート負荷分散規則を構成できます:
1. Standard Load Balancer を構成するときに、Load Balancer 規則の構成で **[HA ポート]** チェック ボックスをオンにします。
2. **[フローティング IP]** で、 **[無効]** を選択します。

この構成は、現在のロード バランサー リソース上で他のいかなる負荷分散規則の構成も許可しません。 また、バックエンド インスタンスの特定のセットについて、他の内部ロード バランサー リソース構成を許可しません。

ただし、この HA ポート規則に加えて、バックエンド インスタンス用のパブリックな Standard Load Balancer を構成できます。

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>内部 Standard Load Balancer 上の単一のフローティング IP (Direct Server Return) HA ポートの構成

同様に、**HA ポート**と単一フロントエンドの負荷分散規則を使用するようにロード バランサーを構成できます。それには、 **[フローティング IP]** を **[有効]** に設定します。 

この構成を使用することにより、複数のフローティング IP 負荷分散規則またはパブリック ロード バランサー、あるいはその両方を追加できます。 ただし、この構成の上で、非フローティング IP と HA ポートを使用する負荷分散構成は使用できません。

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>内部 Standard Load Balancer 上の複数の HA ポート構成

同じバックエンド プールに対して複数の HA ポート フロントエンドを構成する必要があるシナリオの場合は、次の手順を実行できます: 
- 単一の内部 Standard Load Balancer リソース用の複数のフロントエンドのプライベート IP アドレスを構成します。
- 複数の負荷分散規則を構成します。各規則には、1 つの一意のフロントエンド IP アドレスが選択されます。
- **[HA ポート]** オプションを選択し、すべての負荷分散規則で **[フローティング IP]** を **[有効]** に設定します。

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>同じバックエンド インスタンス上で HA ポートとパブリック ロード バランサーを使用する内部ロード バランサー

バックエンド リソース用の *1 つの*パブリック Standard Load Balancer リソースと、HA ポートを使用する単一の内部 Standard Load Balancer を構成できます。

>[!NOTE]
>この機能は現在、Azure Resource Manager テンプレート経由で使用できますが、Azure Portal 経由では使用できません。

## <a name="limitations"></a>制限事項

- HA ポートの構成は内部ロード バランサーに対してのみ使用できます。 パブリック ロード バランサーに対しては使用できません。

- HA ポートの負荷分散規則と非 HA ポートの負荷分散規則の組み合わせはサポートされていません。

- HA ポート機能は、IPv6 では使うことができません。

- フロー対称 (主に NVA シナリオの場合) がバックエンド インスタンスと 1 つの NIC (および 1 つの IP 構成) でサポートされるのは、上の図に示すように使用し、HA ポート負荷分散規則が使用されている場合のみです。 これは他のシナリオでは提供されません。 つまり、2 つ以上のロード バランサー リソースと個別の規則を使用した場合、それぞれ独立した意思決定が行われるため調整することはできません。 [ネットワーク仮想アプライアンス](#nva)の説明と図をご覧ください。 複数の NIC を使用しているか、またはパブリック ロード バランサーおよび内部ロード バランサーの間に NVA を配置している場合は、フロー対称を使用できません。  イングレス フローをアプライアンスの IP にソース NAT して、応答が同じ NVA に到着できるようにすることによって、これを回避できる場合があります。  ただし、1 つの NIC を使用し、上の図に示す参照アーキテクチャを使用することを強くお勧めします。


## <a name="next-steps"></a>次の手順

- [内部 Standard Load Balancer で HA ポートを構成する](load-balancer-configure-ha-ports.md)
- [Standard Load Balancer の詳細を確認する](load-balancer-standard-overview.md)
