---
title: Azure ネットワーク サービスを使用したリモートでの作業
description: このページでは、リモートで作業できるように Azure ネットワーク サービスを使用する方法と、リモートで作業するユーザー数が増加して発生するトラフィックの問題を軽減する方法について説明します。
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 835be5b867826d75732c9482743ad6a4ca2900e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98231844"
---
# <a name="working-remotely-using-azure-networking-services"></a>Azure ネットワーク サービスを使用したリモートでの作業

>[!NOTE]
> この記事では、Azure ネットワーク サービス、Microsoft ネットワーク、Azure パートナー エコシステムを利用してリモートで作業し、COVID-19 の危機が原因で直面している可能性のあるネットワークの問題を軽減する方法について説明します。

この記事では、ピーク使用時に組織がユーザー向けにリモート アクセスを設定したり、容量を追加して既存のソリューションを補完したりするために使用できるオプションについて説明します。 ネットワーク アーキテクトは、次のような課題に直面しています。

- ネットワーク使用率の増加に対処する。
- 企業や顧客のより多くの従業員に信頼性の高い安全な接続を提供する。
- 世界中の離れた場所への接続を提供する。

すべてのネットワーク (たとえば、プライベート WAN や企業のコア ネットワークなど) で、リモート ワーカーによるピーク時の負荷による輻輳が発生するわけではありません。 ボトルネックは、一般に、ホーム ブロードバンド ネットワークと、企業のオンプレミス ネットワークの VPN ゲートウェイでのみ報告されています。

ネットワーク プランナーは、さまざまな種類のトラフィックに異なるネットワーク処理の優先順位が必要であることを念頭に置き、スマートな負荷のリダイレクト/分散をいくつか行うことで、ボトルネックの緩和とネットワーク輻輳の軽減を支援します。 たとえば、医師と患者のやりとりをリアルタイムで遠隔診断する際のトラフィックは、重要度が高く、遅延/ジッターの影響を受けます。 一方、ストレージ間の同じトラフィックのレプリケーションには、遅延の影響がありません。 前者のトラフィックは、サービス品質の高い最適なネットワーク パスを使用してルーティングする必要がありますが、後者のトラフィックは、次善のルートを使用してルーティングすることが許容されます。



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>ベスト プラクティスの共有 - Azure ネットワークは、弾力性と高可用性を実現するように設計されている

Azure は、リソースの使用率の急激な変化に耐えられるように設計されており、ピーク使用時に大幅に役立ちます。 また、Microsoft では、世界最大規模のネットワークの 1 つを管理し、運用しています。 Microsoft のネットワークは、1 つのネットワーク要素の障害からリージョン全体の障害まで、さまざまな種類の障害に耐えられる高可用性を実現するように設計されています。

Microsoft ネットワークは、要件を満たし、さまざまな種類のネットワーク トラフィックに対し最適なパフォーマンスを提供するように設計されています。たとえば、Skype や Teams、CDN、リアルタイムのビッグ データ分析、Azure Storage、Bing、Xbox のための、遅延の影響を受けやすいマルチメディア トラフィックを含みます。 さまざまな種類のトラフィックに最適なパフォーマンスを提供するために、Microsoft ネットワークでは、トラフィックの発信元にできるだけ近い場所にあるリソースを宛先として (または経由地として) すべてのトラフィックを引き付けます。

>[!NOTE] 
>以下で説明する Azure ネットワーク機能を使用すると、Microsoft グローバル ネットワークのトラフィック引力動作を利用して、より優れたカスタマー ネットワーク エクスペリエンスを実現できます。 Microsoft ネットワークのトラフィック引力動作は、ピーク使用時の輻輳が発生する可能性のあるファースト/ラスト マイル ネットワークからできるだけ早くトラフィックのオフロードを行います。
>

## <a name="enable-employees-to-work-remotely"></a>従業員がリモートで作業できるようにする

Azure VPN Gateway では、ポイント対サイト (P2S) とサイト間 (S2S) の両方の VPN 接続がサポートされています。 Azure VPN Gateway を使用すると、Azure にデプロイされたリソースとオンプレミスのリソースの両方に安全にアクセスできるように従業員の接続を拡張できます。 詳細については、[ユーザーがリモートで作業できるようにする方法](../vpn-gateway/work-remotely-support.md)に関するページを参照してください。 

Secure Sockets Tunneling Protocol (SSTP) を使用している場合、コンカレント接続の数は 128 に制限されます。 より多くの接続を得るには、OpenVPN または IKEv2 に移行することを推奨します。 詳細については、「[SSTP から OpenVPN プロトコルまたは IKEv2 に移行する](../vpn-gateway/ikev2-openvpn-from-sstp.md
)」を参照してください。

Azure にデプロイされたリソースにアクセスするために、リモート開発者は、VPN 接続で安全なシェル アクセス (RDP または SSH) を取得する代わりに Azure Bastion ソリューションを使用できます。アクセスされる VM にパブリック IP を必要としません。 詳細については、[Azure Bastion を使用してリモートで作業する方法](../bastion/work-remotely-support.md)に関するページを参照してください。

大規模な VPN 接続を集約する場合は、さまざまなオンプレミスのグローバルな場所内、さまざまなリージョン ハブ内、スポークの仮想ネットワーク内にあるリソース間で Any-to-Any 接続をサポートするため、そして複数のホーム ブロードバンド ネットワークの使用率を最適化するため、Azure Virtual WAN を使用できます。 詳細については、[自宅からの作業のニーズに応えることに苦慮している場合にAzure Virtual WAN を役立てる方法](../virtual-wan/work-remotely-support.md)に関するページを参照してください。

リモート ワーカーをサポートするもう 1 つの方法は、Azure 仮想ネットワークにホストされている仮想デスクトップ インフラストラクチャ (VDI) をデプロイして、Azure Firewall で保護することです。 たとえば、Windows Virtual Desktop (WVD) は、Azure 上で実行されるデスクトップとアプリの仮想化サービスです。 Windows Virtual Desktop を使用すると、追加のゲートウェイ サーバーを実行しなくても、Azure サブスクリプションでスケーラブルで柔軟な環境を設定できます。 お客様は、仮想ネットワーク内の WVD 仮想マシンに対してのみ責任を持ちます。 詳細については、「[Azure Firewall のリモート作業のサポート](../firewall/remote-work-support.md)」を参照してください。 

Azure には、豊富なエコ システム パートナーもあります。 パートナーである Azure 上のネットワーク仮想アプライアンスは、VPN 接続の拡張にも役立ちます。 詳細については、「[リモート作業でのネットワーク仮想アプライアンス (NVA) に関する考慮事項](../vpn-gateway/nva-work-remotely-support.md)」を参照してください。

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>グローバルに分散したリソースにアクセスできるように従業員の接続を拡張する

次の Azure サービスは、従業員がグローバルに分散したリソースにアクセスできるようにするために役立ちます。 リソースは、Azure リージョン、オンプレミスのネットワーク、または他のパブリックまたはプライベート クラウドのいずれかに存在する可能性があります。 

- **Azure Virtual Network ピアリング**:複数の Azure リージョンにリソースをデプロイする場合、または複数の仮想ネットワークを使用してリモートで作業している従業員の接続を集約する場合は、仮想ネットワーク ピアリングを使用して複数の Azure 仮想ネットワーク間の接続を確立できます。 詳細については、「[仮想ネットワーク ピアリング][VNet-peer]」をご覧ください。

- **Azure VPN ベースのソリューション**:リモート従業員が P2S または S2S VPN 経由で Azure に接続されている場合は、オンプレミスのネットワークと Azure VPN Gateway の間に S2S VPN を構成することで、オンプレミスのネットワークへのアクセスを有効にすることができます。 詳細については、[サイト間接続の作成][S2S]に関する記事をご覧ください。

- **ExpressRoute**:ExpressRoute プライベート ピアリングを使用すると、Azure デプロイとオンプレミスのインフラストラクチャまたはコロケーション施設内のインフラストラクチャとの間でプライベート接続を有効にすることができます。 Microsoft ピアリングを介した ExpressRoute は、オンプレミス ネットワークから Microsoft のパブリック エンドポイントへのアクセスも許可します。 ExpressRoute 接続では、公共のインターネットを利用できません。 セキュリティで保護された接続、信頼性、高いスループット、インターネット経由の一般的な接続よりも短く安定した待機時間を提供します。 詳細については、「[ExpressRoute の概要][ExR]」を参照してください。 既に [ExpressRoute パートナー エコシステム][ExR-eco]の一部である既存のネットワーク プロバイダーを活用することで、Microsoft への大規模な帯域幅の接続を実現するための時間を短縮することができます。  [ExpressRoute Direct][ExR-D] を使用して、オンプレミスのネットワークを Microsoft のバックボーンに直接接続できます。 ExpressRoute Direct では、デュアル 10 Gbps または 100 Gbps の 2 つの異なる回線レート オプションが提供されます。 

- **Azure Virtual WAN**:Azure Virtual WAN を使用すると、VPN 接続と ExpressRoute 回線の間でシームレスな相互運用性を実現できます。 既に説明したように、Azure Virtual WAN では、さまざまなリージョン ハブとスポークの仮想ネットワーク内で、さまざまなオンプレミスのグローバルな場所にあるリソース間の Any-to-Any 接続もサポートしています。

## <a name="scale-customer-connectivity-to-frontend-resources"></a>顧客の接続をフロントエンド リソースへ拡大する

多くのユーザーがオンラインになっている間、企業の Web サイトの多くで顧客のトラフィックが増加しています。 Azure Application Gateway は、この増加したフロントエンド ワークロードを管理するのに役立ちます。 詳しくは、「[Application Gateway の高トラフィックのサポート](../application-gateway/high-traffic-support.md)」をご覧ください。

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft によるマルチクラウド トラフィックのサポート

他のパブリック クラウドへのデプロイでは、Microsoft はグローバル接続を提供できます。 Azure Virtual WAN、VPN、または ExpressRoute がこの点で役立ちます。 Azure から他のクラウドへ接続を拡張するために、2 つのクラウド間に S2S VPN を構成できます。 ExpressRoute を使用して、Azure から他のパブリック クラウドへの接続を確立することもできます。 Oracle クラウドは、ExpressRoute パートナー エコシステムの一部です。 [Azure と Oracle Cloud Infrastructure 間の直接相互接続をセットアップする][Az-OCI]ことができます。 ExpressRoute パートナー エコシステムの一部であるほとんどのサービス プロバイダーは、他のパブリック クラウドへのプライベート接続も提供します。 これらのサービス プロバイダーを利用することで、Azure と他のクラウド内のデプロイの間に ExpressRoute を介したプライベート接続を確立できます。

## <a name="next-steps"></a>次のステップ

次の記事では、さまざまな Azure ネットワーク機能を使用して、リモートで作業するユーザーの規模を拡大する方法について説明します。

| **記事** | **説明** |
| --- | --- |
| [ユーザーがリモートで作業できるようにする方法](../vpn-gateway/work-remotely-support.md) | ユーザーのリモート アクセスを設定するため、または組織の容量を追加して既存のソリューションを補完するために使用できるオプションを確認します。|
| [自宅からの作業のニーズに応えることに苦慮している場合にAzure Virtual WAN を役立てる方法](../virtual-wan/work-remotely-support.md) | Azure Virtual WAN を使用して、組織のリモート接続のニーズに対処します。|
| [Application Gateway の高トラフィックのサポート](../application-gateway/high-traffic-support.md) | Application Gateway と Web アプリケーション ファイアウォール (WAF) を使用して、Web アプリケーションへのトラフィックをスケーラブルかつ安全な方法で管理できます。 |
| [リモート作業でのネットワーク仮想アプライアンス (NVA) に関する考慮事項](../vpn-gateway/nva-work-remotely-support.md)|Azure で NVA を活用してリモート アクセス ソリューションを提供するためのガイダンスを確認します。 |
| [SSTP から OpenVPN プロトコルまたは IKEv2 に移行する](../vpn-gateway/ikev2-openvpn-from-sstp.md) | OpenVPN プロトコルまたは IKEv2 に移行して、コンカレント接続の上限が 128 という SSTP の制限を取り除きます。|
| [Azure Bastion を使用したリモート作業](../bastion/work-remotely-support.md) | パブリック IP アドレスを使用しないで Azure portal で直接、Azure 仮想ネットワーク内の仮想マシンに安全かつシームレスに RDP/SSH 接続できます。 |
| [リモート ユーザーをサポートするためのハイブリッド接続を Azure ExpressRoute を使用して作成する](../expressroute/work-remotely-support.md) | ハイブリッド接続に ExpressRoute を使用して、組織内のユーザーがリモートで作業できるようにします。|
| [Azure Firewall のリモート作業のサポート](../firewall/remote-work-support.md)|Azure Firewall を使用して Azure 仮想ネットワーク リソースを保護します。 |

<!--Link References-->
[VNet-peer]: ../virtual-network/virtual-network-peering-overview.md
[S2S]: ../vpn-gateway/tutorial-site-to-site-portal.md
[ExR]: ../expressroute/expressroute-introduction.md
[ExR-eco]: ../expressroute/expressroute-locations.md
[ExR-D]: ../expressroute/expressroute-erdirect-about.md
[Az-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md