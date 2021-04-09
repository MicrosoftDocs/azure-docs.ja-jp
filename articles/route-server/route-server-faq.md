---
title: Azure Route Server に関してよく寄せられる質問
description: Azure Route Server についてよく寄せられる質問とその回答を紹介します。
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 6eed0ed3e936b0e9a534c82a3105c2ed37cab3d5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485421"
---
# <a name="azure-route-server-preview-faq"></a>Azure Route Server (プレビュー) に関する FAQ

> [!IMPORTANT]
> Azure Route Server (プレビュー) は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="what-is-azure-route-server"></a>Azure Route Server とは何ですか。

Azure Route Server は、ネットワーク仮想アプライアンス (NVA) と仮想ネットワークの間のルーティングを簡単に管理できる、フル マネージド サービスです。

### <a name="is-azure-route-server-just-a-vm"></a>Azure Route Server は単なる VM ですか。

いいえ。 Azure Route Server は、高可用性を使用して設計されたサービスです。 [Availability Zones](../availability-zones/az-overview.md) をサポートする Azure リージョンにデプロイされている場合は、ゾーンレベルの冗長性があります。

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Azure Route Server ではどのようなルーティング プロトコルがサポートされていますか。

Azure Route Server では、Border Gateway Protocol (BGP) のみがサポートされています。 仮想ネットワーク内の専用サブネットに Azure Route Server をデプロイする必要があるため、NVA ではマルチホップの外部 BGP をサポートする必要があります。 選択する [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) は、NVA で BGP を構成するときに Azure Route Server によって使用されるものと異なる必要があります。

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Azure Route Server では NVA と VM の間でデータ トラフィックがルーティングされますか。

いいえ。 Azure Route Server では、NVA との間でのみ BGP ルートが交換されます。 データ トラフィックは、NVA と、選択した VM の間で直接送受信されます。

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Azure Route Server が複数の NVA から同じルートを受信した場合、仮想ネットワーク内の VM に対してルートのすべてのコピー (ただし、それぞれ次ホップが異なる) がプログラムされますか。

はい (ルートの AS パスの長さが同じ場合にのみ)。 VM によってこのルートの宛先にトラフィックが送信されると、VM ホストによって等コスト マルチパス (ECMP) ルーティングが行われます。 ただし、1 つの NVA によって、他の NVA よりも AS パスの長さが短いルートが送信された場合。 Azure Route Server では、仮想ネットワーク内の VM に対して、この NVA に設定された次ホップを持つルートのみがプログラムされます。

### <a name="does-azure-route-server-support-vnet-peering"></a>Azure Route Server では VNet ピアリングがサポートされていますか。

はい。 Azure Route Server をホストしている VNet を別の VNet にピアリングし、その VNet で [Use Remote Gateway]\(リモートゲートウェイの使用\) を有効にする場合。 Azure Route Server では、その VNet のアドレス空間が確認され、ピアリングされたすべての NVA にそれらが送信されます。

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>どの自律システム番号 (ASN) を使用できますか。

ネットワーク仮想アプライアンスで、独自のパブリック ASN またはプライベート ASN を使用できます。 Azure または IANA によって予約されている範囲を使用することはできません。
次の ASN は、Azure または IANA によって予約されています。

* Azure によって予約済みの ASN:
    * パブリック ASN: 8074、8075、12076
    * プライベート ASN: 65515、65517、65518、65519、65520
* [IANA によって予約済み](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)の ASN:
    * 23456、64496-64511、65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>32 ビット (4 バイト) の ASN を使用できますか。

いいえ。Azure Route Server でサポートされる ASN は、16 ビット (2 バイト) のみです。

## <a name="route-server-limits"></a><a name = "limitations"></a>Route Server の制限

Azure Route Server には、(デプロイごとに) 次の制限があります。

| リソース | 制限 |
|----------|-------|
| サポートされている BGP ピアの数 | 8 |
| 各 BGP ピアで Azure Route Server にアドバタイズできるルートの数 | 200 |
| Azure Route Server で ExpressRoute または VPN ゲートウェイにアドバタイズできるルートの数 | 200 |

NVA によって制限より多くのルートが公開された場合、BGP セッションは削除されます。 ゲートウェイと Azure Route Server にこれが発生した場合は、オンプレミス ネットワークから Azure への接続が失われます。 詳しくは、「[Azure 仮想マシンのルーティングに関する問題を診断する](../virtual-network/diagnose-network-routing-problem.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

[Azure Route Server を構成する](quickstart-configure-route-server-powershell.md)方法を確認します。
