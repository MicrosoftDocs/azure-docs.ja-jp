---
title: Azure Virtual Network (プレビュー) の IPv6 の概要
titlesuffix: Azure Virtual Network
description: Azure 仮想ネットワーク内の IPv6 エンドポイントおよびデータ パスを IPv6 の観点から説明します。
services: virtual-network
documentationcenter: na
author: kumudd
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62131091"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Azure Virtual Network の IPv6 の概要 (プレビュー)

Azure Virtual Network (VNET) の IPv6 により、仮想ネットワークやインターネットで IPv6 接続や IPv4 接続を使用するアプリケーションを Azure 内でホストできます。 パブリック IPv4 アドレスが枯渇したことから、モビリティおよびモノのインターネット (IoT) 向けの新しいネットワークは、IPv6 をベースに構築されることが多くなっています。 老舗の ISP やモバイル ネットワークでも IPv6 への転換が進められています。 IPv4 専用のサービスは、既存の市場でも新興市場でも実際に不利になる場合があります。 デュアル スタック IPv4/IPv6 接続により、Azure でホストされるサービスは、このテクノロジのギャップを乗り越えることができます。これにより、既存の IPv4 用およびこの新しい IPv6 用のどちらのデバイスおよびネットワークにも簡単に接続できる、グローバルに使用可能なデュアル スタック サービスが実現されます。

Azure の最初の IPv6 接続により、Azure 内でホストされるアプリケーションでは、デュアル スタック (IPv4/IPv6) インターネット接続を簡単に利用できるようになりました。 接続がインバウンドで開始される場合でも、アウトバウンドで開始される場合でも、IPv6 接続が負荷分散されるため、VM のデプロイが簡単になります。 この機能は依然として使用できます。詳細については、[こちら](../load-balancer/load-balancer-ipv6-overview.md)を参照してください。
Azure 仮想ネットワークの IPv6 は、はるかに充実した機能を備えているため、Azure 内に IPv6 ソリューション アーキテクチャ全体をデプロイできます。

> [!Important]
> 現在、Azure Virtual Network の IPv6 は、パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

次の図は、Azure 内のシンプルなデュアル スタック (IPv4/IPv6) デプロイメントを示しています。

![IPv6 ネットワーク デプロイメント図](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>メリット

Azure Virtual Network IPv6 のメリットを次に示します。

- Azure でホストされるアプリケーションが、成長中のモバイル市場やモノのインターネット市場で利用できるようになります。
- デュアル スタック IPv4/IPv6 VM により、サービスをきわめて柔軟にデプロイできます。 単一のサービス インスタンスから IPv4 対応のインターネット クライアントにも、IPv6 対応のインターネット クライアントにも接続できます。
- 安定した既存の Azure VM およびインターネット間の IPv6 接続を基礎にしています。
- インターネットに対する IPv6 接続は、デプロイメント内で明示的に要求して初めて確立されるため、既定で安全です。

## <a name="capabilities"></a>機能

仮想マシンの IPv6 サポートでは、次のことが可能です。

- Azure のお客様は、アプリケーションやお客様のニーズを満たすように IPv6 仮想ネットワーク アドレス空間を定義できます。また、オンプレミス IP 空間にシームレスに統合するように IPv6 仮想ネットワーク アドレス空間を定義することもできます。
- デュアル スタック (IPv4 および IPv6) 仮想ネットワークとデュアル スタック サブネットにより、アプリケーションは、仮想ネットワークやインターネット内の IPv4 リソースと IPv6 リソースの両方に接続できます。
- ネットワーク セキュリティ グループに対する IPv6 規則により、リソースを保護できます。
- 仮想ネットワーク内の IPv6 トラフィックのルーティングを、ユーザー定義ルートを使用してカスタマイズできます。これは特に、ネットワーク仮想アプライアンスを利用してアプリケーションを拡張しているときに便利です。
- IPv6 ロード バランサー サポートにより、回復性があるスケーラブルなアプリケーションを作成できます。これには、IPv6 パブリック IP の AAAA レコードに対する Azure DNS サポートが含まれます。
- インプレース アップグレードにより、既存の IPv4 専用デプロイメントに IPv6 接続を簡単に追加できます。
- 仮想マシン スケール セットにより、負荷に応じて自動的に拡大縮小するデュアル スタック アプリケーションを作成できます。

## <a name="limitations"></a>制限事項
Azure 仮想ネットワークの IPv6 のプレビュー リリースには、次の制限があります。
- Azure 仮想ネットワーク (プレビュー) の IPv6 は、すべてのグローバル Azure リージョン内で利用できます。ただし、利用できるのは、グローバル Azure 内のみで、政府のクラウド内では利用できません。   
- プレビューのポータル サポートでは、IPv6 構成の大部分を表示できますが、すべては表示できません。ただし、仮想ネットワークの IPv6 では、Azure Powershell やコマンド ライン インターフェイス (CLI) を使用した IPv6 のデプロイメントが完全にサポートされており、そのドキュメント (サンプルを含む) も用意されています。
- プレビューの Network Watcher サポートは、NSG フロー ログとネットワーク パケット キャプチャに制限されています。
- プレビューの負荷分散サポートは、最初は Basic Load Balancer に制限されています。
- プレビューでは、インスタンス レベルのパブリック IP (VM 上の直接のパブリック IP) はサポートされません。  
- プレビューでは、仮想ネットワーク ピアリング (リージョンまたはグローバル) はサポートされません。 

## <a name="pricing"></a>価格

Azure の IPv6 のリソースおよび帯域幅は、IPv4 と同じレートで課金されます。 IPv6 の場合であっても、料金の追加や変更はありません。 [パブリック IP アドレス](https://azure.microsoft.com/pricing/details/ip-addresses/)、[ネットワーク帯域幅](https://azure.microsoft.com/pricing/details/bandwidth/)、または [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) の価格に関する詳細を確認してください。

## <a name="next-steps"></a>次の手順

- [Azure PowerShell を使用して IPv6 デュアル スタック アプリケーションをデプロイする](virtual-network-ipv4-ipv6-dual-stack-powershell.md)方法を学習します。
- [Azure CLI を使用して IPv6 デュアル スタック アプリケーションをデプロイする](virtual-network-ipv4-ipv6-dual-stack-cli.md)方法を学習します。
