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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: ff5c8c4d3f6a0c87afae67404a5a39d4fe3757d9
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571094"
---
# <a name="virtual-network-tap"></a>仮想ネットワーク TAP

Azure 仮想ネットワーク TAP (ターミナル アクセス ポイント) を使用すると、仮想マシン ネットワークのトラフィックをネットワーク パケット コレクターまたは分析ツールに連続してストリーミングできます。 コレクターまたは分析ツールは、[ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/) パートナーから提供されています。 仮想ネットワーク TAP を使用できることが検証されたパートナー ソリューションの一覧については、[パートナー ソリューション](#virtual-network-tap-partner-solutions)に関するページを参照してください。

> [!IMPORTANT]
> 仮想ネットワーク TAP は、現在、すべての Azure リージョンでプレビューの段階です。 仮想ネットワーク TAP を使用するには、サブスクリプション ID を使用して  <azurevnettap@microsoft.com> にメールを送信してプレビューに登録する必要があります。 サブスクリプションが登録されると、メールが届きます。 確認メールを受信するまで、この機能を使用することはできません。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「 [Microsoft Azure プレビューの追加利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。 

## <a name="virtual-network-tap-partner-solutions"></a>仮想ネットワーク TAP パートナー ソリューション

### <a name="network-packet-brokers"></a>ネットワーク パケット ブローカー

- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>セキュリティ分析、ネットワーク/アプリケーションのパフォーマンス管理

- [Awake Security](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/company/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [RSA NetWitness® Platform](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

次の図は、仮想ネットワーク TAP のしくみを示しています。 仮想ネットワークにデプロイされている仮想マシンに接続された[ネットワーク インターフェイス](virtual-network-network-interface.md)に TAP 構成を追加できます。 接続先は、監視対象のネットワーク インターフェイスまたは [ピアリングされた仮想](virtual-network-peering-overview.md)ネットワークと同じ仮想ネットワーク内の仮想ネットワーク IP アドレスです。 仮想ネットワーク TAP 用のコレクター ソリューションは、高可用性のために [Azure 内部ロード バランサー](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts)の背後にデプロイできます。 個々のソリューションのデプロイ オプションを評価するには、[パートナー ソリューション](#virtual-network-tap-partner-solutions)のページを参照してください。

![仮想ネットワーク TAP のしくみ](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>前提条件

仮想ネットワーク TAP を作成する前に、プレビューへの登録を確認するメールを受信済みであり、同じ Azure リージョンの TAP トラフィックを集計するために、[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) デプロイ モデルとパートナー ソリューションを使用して 1 つまたは複数の仮想マシンを作成している必要があります。 仮想ネットワークにパートナー ソリューションがない場合は、[パートナー ソリューション](#virtual-network-tap-partner-solutions)のページを参照してデプロイしてください。 同じ仮想ネットワーク TAP リソースを使用して、同じサブスクリプションまたは異なるサブスクリプションの複数のネットワーク インターフェイスからのトラフィックを集計できます。 監視対象のネットワーク インターフェイスが異なるサブスクリプションに属している場合、両方のサブスクリプションが同じ Azure Active Directory テナントに関連付けられている必要があります。 また、監視対象のネットワーク インターフェイスと、TAP トラフィックを集計するための宛先エンドポイントは、同じリージョン内のピアリングされた仮想ネットワーク内に存在することができます。 このデプロイ モデルを使用している場合は、仮想ネットワーク TAP を構成する前に[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)が有効になっていることを確認してください。

## <a name="permissions"></a>アクセス許可

ネットワーク インターフェイスで TAP 構成を適用するために使用するアカウントには、[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表に記載されている必要なアクションを実行できる[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)が割り当てられている必要があります。

| Action | Name |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | 仮想ネットワーク TAP リソースの作成、更新、読み取り、および削除に必要 |
| Microsoft.Network/networkInterfaces/read | TAP が構成されるネットワーク インターフェイス リソースを読み取るために必要 |
| Microsoft.Network/tapConfigurations/* | ネットワーク インターフェイス上の TAP 構成の作成、更新、読み取り、削除に必要 |

## <a name="next-steps"></a>次の手順

- [仮想ネットワーク TAP の作成](tutorial-tap-virtual-network-cli.md)方法について説明します。
