---
title: Azure DDoS Protection の基本的なベスト プラクティス
description: DDOS Protection を使用した、セキュリティ上のベスト プラクティスについて説明します。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 66c1ab1cb5ed478aa34825fb6903e4d06f834097
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94989475"
---
# <a name="fundamental-best-practices"></a>基本的なベスト プラクティス

次のセクションでは、Azure で DDoS に対する回復力のあるサービスを構築するための規範的なガイダンスを提供します。

## <a name="design-for-security"></a>セキュリティ向けの設計

設計と実装から、デプロイと運用まで、アプリケーションのライフ サイクル全体を通して、セキュリティを優先する必要があります。 アプリケーションには、比較的少量の要求によって膨大なリソースを使用し、結果としてサービスを停止させることができるような、バグが含まれる可能性があります。 

Microsoft Azure で実行されるサービスを保護するには、お客様は、自分のアプリケーションのアーキテクチャをよく理解し、[ソフトウェア品質の 5 つの重要な要素](/azure/architecture/guide/pillars)に注目する必要があります。
お客様は、標準的なトラフィックの量、アプリケーションと他のアプリケーションの間の接続モデル、パブリック インターネットに公開されるサービス エンドポイントについて知っておく必要があります。

アプリケーション自体を対象とするサービス拒否攻撃に対処するのに十分な回復力をアプリケーションに持たせることが、最も重要です。 Azure プラットフォームには、[セキュリティ開発ライフサイクル (Security Development Lifecycle: SDL)](https://www.microsoft.com/sdl/default.aspx) からセキュリティとプライバシーが組み込まれるようになっています。 SDL は、すべての開発段階でセキュリティに対処し、Azure がいっそう安全になるように継続的に更新されることを保証します。

## <a name="design-for-scalability"></a>スケーラビリティのための設計

スケーラビリティとは、負荷の増大に対するシステムの対応能力のことです。 [水平方向にスケーリングする](/azure/architecture/guide/design-principles/scale-out)ようにアプリケーションを設計し、増加した負荷の需要を満たす (特に、DDoS 攻撃の場合)。 アプリケーションがサービスの 1 つのインスタンスに依存する場合は、単一障害点が発生します。 複数のインスタンスをプロビジョニングすると、システムの回復力と拡張性が高まります。

[Azure App Service](../app-service/overview.md) の場合は、複数のインスタンスを提供する [App Service プラン](../app-service/overview-hosting-plans.md)を選択してください。 Azure Cloud Services の場合は、[複数インスタンス](../cloud-services/cloud-services-choose-me.md)を使用するように各ロールを構成してください。 [Azure Virtual Machines](../virtual-machines/index.yml) の場合は、仮想マシン (VM) アーキテクチャに 1 つ以上の VM が含まれていることと、[可用性セット](../virtual-machines/windows/tutorial-availability-sets.md)に各 VM が含まれていることを確認してください。 自動スケーリング機能には[仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)を使うことをお勧めします。

## <a name="defense-in-depth"></a>多層防御

多層防御の基になっている考え方は、多様な防御戦略によってリスクを管理することです。 アプリケーションのセキュリティ防御を多層化すると、攻撃が成功する可能性が減少します。 お客様には、Azure プラットフォームの組み込み機能を使って、アプリケーションのセキュリティ保護設計を実装することをお勧めします。

たとえば、攻撃のリスクは、アプリケーションの規模 (*攻撃対象領域*) と共に大きくなります。 公開されている IP アドレス空間を承認リストを使用して閉鎖し、ロード バランサー ([Azure Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md) と [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)) を用いて不要にリスニングしているポートを閉じることで、攻撃対象領域を減らすことができます。 [ネットワーク セキュリティ グループ (NSG)](../virtual-network/network-security-groups-overview.md) も、攻撃対象領域を軽減する 1 つの手段です。
[サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags)および[アプリケーション セキュリティ グループ](../virtual-network/network-security-groups-overview.md#application-security-groups)を使用して、セキュリティ規則作成の複雑さを軽減し、アプリケーションの構造の自然な延長としてネットワーク セキュリティを構成することができます。

お客様は、可能な限り、[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)に Azure サービスをデプロイする必要があります。 このプラクティスを使用すると、サービス リソースはプライベート IP アドレスを通して通信できます。 仮想ネットワークからの Azure サービス トラフィックは、パブリック IP アドレスを発信元 IP アドレスとして既定で使用します。 [サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を使用すると、サービス トラフィックは、仮想ネットワークから Azure サービスにアクセスするときに、仮想ネットワークのプライベート アドレスを発信元 IP アドレスとして使用するように切り替わります。

Azure 内のリソースと供に、お客様のオンプレミスのリソースが攻撃されることがよくあります。 オンプレミスの環境を Azure に接続している場合は、オンプレミスのリソースのパブリック インターネットへの露出を最小限にすることをお勧めします。 よく知られたパブリック エンティティを Azure にデプロイすることで、Azure のスケールと高度な DDoS 保護機能を使用できます。 これらのパブリックにアクセスできるエンティティは、DDoS 攻撃の対象になることがよくあるため、Azure 内に配置すると、オンプレミスのリソースへの影響が減少します。

## <a name="next-steps"></a>次のステップ

- [DDoS 保護プランの作成](manage-ddos-protection.md)方法について説明します。