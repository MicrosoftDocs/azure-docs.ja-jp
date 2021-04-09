---
title: Azure 仮想ネットワーク TAP の概要 | Microsoft Docs
description: 仮想ネットワーク TAP について説明します。 仮想ネットワーク TAP には、パケット コレクターにストリーミングできる仮想マシン ネットワーク トラフィックのディープ コピー機能があります。
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 6160dd09edc57f2f52306d4dad0dde413fff0616
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617184"
---
# <a name="virtual-network-tap"></a>仮想ネットワーク TAP
> [!IMPORTANT]
> 仮想ネットワーク TAP プレビューは、現在、すべての Azure リージョンで保留にされています。 サブスクリプション ID を記入して <azurevnettap@microsoft.com> にメールを送信していただけば、プレビューに関する今後の更新についてお知らせします。 その間は、エージェント ベースの ソリューションまたは NVA ソリューションを使用できます。これらは、[Azure Marketplace オファリング](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances%3Ball&search=Network%20Traffic&filters=partners)で入手できる[パケット ブローカー パートナー ソリューション](#virtual-network-tap-partner-solutions)を通して TAP/ネットワーク可視性機能を提供します。

Azure 仮想ネットワーク TAP (ターミナル アクセス ポイント) を使用すると、仮想マシン ネットワークのトラフィックをネットワーク パケット コレクターまたは分析ツールに連続してストリーミングできます。 コレクターまたは分析ツールは、[ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/) パートナーから提供されています。 仮想ネットワーク TAP を使用できることが検証されたパートナー ソリューションの一覧については、[パートナー ソリューション](#virtual-network-tap-partner-solutions)に関するページを参照してください。
次の図は、仮想ネットワーク TAP のしくみを示しています。 仮想ネットワークにデプロイされている仮想マシンに接続された[ネットワーク インターフェイス](virtual-network-network-interface.md)に TAP 構成を追加できます。 接続先は、監視対象のネットワーク インターフェイスまたは [ピアリングされた仮想](virtual-network-peering-overview.md)ネットワークと同じ仮想ネットワーク内の仮想ネットワーク IP アドレスです。 仮想ネットワーク TAP 用のコレクター ソリューションは、高可用性のために Azure 内部ロード バランサーの背後にデプロイできます。
![仮想ネットワーク TAP のしくみ](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>前提条件

仮想ネットワーク TAP を作成する前に、プレビューへの登録を確認するメールを受信済みで、[Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) デプロイ モデルとパートナー ソリューションを使用して、同じ Azure リージョン内の TAP トラフィックを集計するための仮想マシンを 1 つ以上作成済みの必要があります。 仮想ネットワークにパートナー ソリューションがない場合は、[パートナー ソリューション](#virtual-network-tap-partner-solutions)のページを参照してデプロイしてください。 同じ仮想ネットワーク TAP リソースを使用して、同じサブスクリプションまたは異なるサブスクリプションの複数のネットワーク インターフェイスからのトラフィックを集計できます。 監視対象のネットワーク インターフェイスが異なるサブスクリプションに属している場合、両方のサブスクリプションが同じ Azure Active Directory テナントに関連付けられている必要があります。 また、監視対象のネットワーク インターフェイスと、TAP トラフィックを集計するための宛先エンドポイントは、同じリージョン内のピアリングされた仮想ネットワーク内に存在することができます。 このデプロイ モデルを使用している場合は、仮想ネットワーク TAP を構成する前に[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)が有効になっていることを確認してください。

## <a name="permissions"></a>アクセス許可

ネットワーク インターフェイスで TAP 構成を適用するために使用するアカウントには、[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表に記載されている必要なアクションを実行できる[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)が割り当てられている必要があります。

| アクション | 名前 |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | 仮想ネットワーク TAP リソースの作成、更新、読み取り、および削除に必要 |
| Microsoft.Network/networkInterfaces/read | TAP が構成されるネットワーク インターフェイス リソースを読み取るために必要 |
| Microsoft.Network/tapConfigurations/* | ネットワーク インターフェイス上の TAP 構成の作成、更新、読み取り、削除に必要 |


## <a name="virtual-network-tap-partner-solutions"></a>仮想ネットワーク TAP パートナー ソリューション

### <a name="network-packet-brokers"></a>ネットワーク パケット ブローカー

- [GigaVUE Cloud Suite for Azure](https://www.gigamon.com/solutions/cloud/public-cloud/gigavue-cloud-suite-azure.html)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>セキュリティ分析、ネットワーク/アプリケーションのパフォーマンス管理

- [Awake Security](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/marketplace-azure)
- [Noname Security](https://nonamesecurity.com/)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® Platform](https://www.rsa.com/content/dam/en/solution-brief/rsa-netwitness-platform-overview-for-federal-agencies.pdf)
- [Vectra Cognito](https://vectra.ai/microsoftazure)



## <a name="next-steps"></a>次のステップ

- [仮想ネットワーク TAP の作成](tutorial-tap-virtual-network-cli.md)方法について説明します。
