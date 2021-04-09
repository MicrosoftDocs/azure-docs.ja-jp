---
title: Azure Virtual Network - 概念とベスト プラクティス
description: Azure Virtual Network の概念とベスト プラクティスについて説明します。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 0a9945a58aa6ec49ad58f3a0a0d03ea75e30f6d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223620"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Azure Virtual Network の概念とベスト プラクティス

この記事では、Azure Virtual Network (VNet) の主要な概念とベスト プラクティスについて説明します。

## <a name="vnet-concepts"></a>VNet の概念

- **[アドレス空間]:** VNet を作成する場合は、パブリック アドレスとプライベート (RFC 1918) アドレスを使用して、カスタム プライベート IP アドレス空間を指定する必要があります。 Azure は、ユーザーが割り当てたアドレス空間のプライベート IP アドレスを仮想ネットワーク内のリソースに割り当てます。 たとえば、アドレス空間 10.0.0.0/16 を指定した VNet で VM をデプロイする場合、VM には 10.0.0.4 などのプライベート IP が割り当てられます。
- **サブネット:** サブネットにより、仮想ネットワークを 1 つ以上のサブネットワークにセグメント分割して、仮想ネットワークのアドレス空間の一部を各サブネットに割り当てることができます。 その後、特定のサブネット内に Azure リソースをデプロイできます。 従来のネットワークのように、サブネットでは、組織の内部ネットワークに適したセグメントに VNet アドレス空間をセグメント分割することができます。 これにより、アドレスの割り当ての効率も向上します。 ネットワーク セキュリティ グループを使用して、サブネット内のリソースを保護できます。 詳細については、「[ネットワーク セキュリティ グループ](./network-security-groups-overview.md)」を参照してください。
- **リージョン**:VNet は 1 つのリージョン/場所内に制限されますが、仮想ネットワーク ピアリングを使用して、異なるリージョンの複数の仮想ネットワークを相互に接続できます。
- **サブスクリプション:** VNet は、サブスクリプション内に制限されます。 各 Azure [サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)と Azure [リージョン](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)内に複数の仮想ネットワークを実装できます。

## <a name="best-practices"></a>ベスト プラクティス

Azure でネットワークを構築するときに、次の汎用的な設計原則に留意することが重要です。

- アドレス空間が重複しないように徹底します。 VNet アドレス空間 (CIDR ブロック) が組織の他のネットワークの範囲と重複しないようにします。
- サブネットは、VNet のアドレス空間全体を有効範囲に含めてはなりません。 事前に計画し、将来に備えてアドレス空間の一部を予約します。
- 小規模な VNet を複数持つよりも、大規模な VNet を少数持つことをお勧めします。 これによって、管理の負担を抑えられます。
- VNet のサブネットにネットワーク セキュリティ グループ (NSG) を割り当てて VNet を保護します。

## <a name="next-steps"></a>次のステップ

 仮想ネットワークの使用を開始するには、仮想ネットワークを作成し、いくつかの VM をそこにデプロイし、VM 間で通信します。 その方法については、[仮想ネットワークの作成](quick-create-portal.md)に関するクイック スタートを参照してください。