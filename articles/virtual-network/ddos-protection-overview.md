---
title: Azure DDoS Protection Standard の概要 | Microsoft Docs
description: Azure DDoS Protection サービスについて説明します。
services: virtual-network
documentationcenter: na
author: kumudd
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: kumud
ms.openlocfilehash: 41e9d88df49d153089e6dc7a12c5873ccc167279
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209452"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection Standard の概要

分散型サービス拒否 (DDoS) 攻撃は、アプリケーションをクラウドに移行している顧客が直面する可用性とセキュリティに関する最大の関心事の一部です。 DDoS 攻撃は、アプリケーションのリソースを使い果たして、そのアプリケーションを正当なユーザーから使用不能にしようとします。 DDoS 攻撃は、インターネット経由で一般に到達可能なすべてのエンドポイントで実行できます。

Azure DDoS Protection をアプリケーション設計のベスト プラクティスと組み合わせることにより、DDoS 攻撃に対する防御が提供されます。 Azure DDoS Protection では、次のサービス レベルが提供されます。

- **Basic**:Azure プラットフォームの一部として、自動的に有効になります。 常時接続のトラフィック監視および一般的なネットワーク レベル攻撃のリアルタイムの軽減策によって、Microsoft のオンライン サービスによって使用されるのと同じ防御が提供されます。 Azure のグローバル ネットワークのスケール全体を使用すると、各地域にまたがる攻撃トラフィックを分散および軽減できます。 IPv4 と IPv6 の Azure [パブリック IP アドレス](virtual-network-public-ip-address.md)に対して保護が提供されます。
- **Standard**: Basic サービス レベルに加えて、特に Azure Virtual Network リソースに対してチューニングされた追加の軽減機能を提供します。 DDoS Protection Standard を有効にすることは簡単であり、アプリケーションの変更は必要ありません。 保護ポリシーは、専用のトラフィック監視および機械学習アルゴリズムによってチューニングされます。 ポリシーは、Azure Load Balancer、Azure Application Gateway、Azure Service Fabric のインスタンスなど、仮想ネットワーク内に展開されたリソースに関連付けられたパブリック IP アドレスに適用されますが、この保護は App Service 環境には適用されません。 攻撃中および履歴の表示のために、Azure Monitor ビューからリアルタイムのテレメトリを使用できます。 診断設定を使用して、多くの機能を持つ攻撃軽減分析を利用できます。 アプリケーション レイヤー保護は、[Azure Application Gateway Web アプリケーション ファイアウォール](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を通して、または Azure Marketplace からサードパーティ製のファイアウォールをインストールすることで、追加できます。 IPv4 と IPv6 の Azure [パブリック IP アドレス](virtual-network-public-ip-address.md)に対して保護が提供されます。

![Azure DDoS Protection の Basic とStandard](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS Protection Standard によって軽減される DDoS 攻撃の種類

DDoS Protection Standard は、次の種類の攻撃を軽減できます。

- **帯域幅消費型攻撃**: 攻撃の目標は、膨大な量の一見正当なトラフィックでネットワーク層をあふれさせることです。 これには、UDP フラッド、増幅フラッド、およびその他の偽装されたパケットのフラッドが含まれます。 DDoS Protection Standard は、Azure のグローバル ネットワーク スケールを自動的に使用し、この潜在的に数ギガバイトに及ぶ攻撃を吸収して除去し、これらの攻撃を軽減します。
- **プロトコル攻撃**: これらの攻撃は、レイヤー 3 とレイヤー 4 のプロトコル スタック内の弱点を悪用して、ターゲットをアクセス不可にします。 これには、SYN フラッド攻撃、リフレクション攻撃、およびその他のプロトコル攻撃が含まれます。 DDoS Protection Standard は、クライアント側と対話して悪意のあるトラフィックと正当なトラフィックを区別し、悪意のあるトラフィックをブロックしてこれらの攻撃を軽減します。 
- **リソース (アプリケーション) レイヤー攻撃**: これらの攻撃は、ホスト間のデータ転送を妨害するために Web アプリケーション パケットをターゲットにします。 これらの攻撃には、HTTP プロトコル違反、SQL インジェクション、クロスサイト スクリプティング、およびその他のレイヤー 7 攻撃が含まれます。 Azure [Application Gateway Web アプリケーション ファイアウォール](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を DDoS Protection Standard と共に使用することにより、これらの攻撃に対する防御が提供されます。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall) ではサードパーティの Web アプリケーション ファイアウォール製品も入手できます。

DDoS Protection Standard は、仮想マシン、ロード バランサー、アプリケーション ゲートウェイに関連付けられたパブリック IP アドレスを含む、仮想ネットワーク内のリソースを保護します。 Application Gateway Web アプリケーション ファイアウォールと組み合わせれば、DDoS Protection Standard は、レイヤー 3 からレイヤー 7 までの完全な軽減機能を提供できます。

## <a name="ddos-protection-standard-features"></a>DDoS Protection Standard の機能

![DDoS 機能](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection Standard の機能には、次のものが含まれます。

- **ネイティブのプラットフォーム統合:** Azure にネイティブに統合します。 Azure Portal による構成が含まれます。 DDoS Protection Standard は、ユーザーのリソースおよびリソース構成を理解しています。
- **ターンキー保護:** DDoS Protection Standard が有効になるとすぐに、簡略化された構成によって、仮想ネットワーク上のすべてのリソースが直ちに保護されます。 ユーザーが介入したり、ユーザーが定義したりする必要はありません。 DDoS Protection Standard では、攻撃が検出されるとすぐに自動で軽減されます。
- **常時接続のトラフィック監視:** DDoS 攻撃の兆候を検出するために、アプリケーションのトラフィック パターンが 24 時間 365 日監視されます。 軽減策は、保護ポリシーを超えた場合に実行されます。
- **アダプティブ チューニング:** インテリジェント トラフィック プロファイリングにより、一定期間にわたってアプリケーションのトラフィックが学習され、お使いのサービスに最も適したプロファイルが選択および更新されます。 このプロファイルは、時間の経過とともにトラフィックが変化すると調整されます。
- **多層保護:** Web アプリケーション ファイアウォールと併用すると、全スタックにわたって DDoS 保護が提供されます。
- **広範囲にわたる軽減スケール:** 60 種類を超える攻撃を軽減することができ、地球規模の容量を利用して、過去最大の DDoS 攻撃からも保護されます。
- **攻撃の分析:** 攻撃中の 5 分ごとの詳細なレポートと、攻撃終了後の完全な概要を取得します。 攻撃中もほぼリアルタイムで監視されている軽減フローのログをオフラインのセキュリティ情報イベント管理 (SIEM) システムにストリーム配信します。
- **攻撃メトリック:** 各攻撃から要約されたメトリックに Azure Monitor 経由でアクセスできます。
- **攻撃アラート:** 組み込みの攻撃メトリックを使用して、攻撃の開始時と停止時、およびその攻撃の期間にわたってアラートを構成できます。 アラートは、Microsoft Azure Monitor ログ、Splunk、Azure Storage、電子メール、Azure portal などの運用ソフトウェアに統合されます。
- **コストの保証:** データ転送とアプリケーションは、ドキュメント化された DDoS 攻撃のサービス クレジットをスケールアウトします。

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection Standard の軽減策

DDoS Protection Standard は実際のトラフィック使用率を監視し、それを DDoS ポリシーで定義されたしきい値と常に比較します。 そのトラフィックしきい値を超えた場合は、DDoS 軽減策が自動的に開始されます。 トラフィックがしきい値未満に戻ると、その軽減策は削除されます。

![対応策](./media/ddos-protection-overview/mitigation.png)

軽減策の実行中、保護されたリソースに送信されたトラフィックは DDoS Protection サービスによってリダイレクトされ、次のようないくつかのチェックが実行されます。

- パケットがインターネット仕様に準拠しており、不正な形式でないことを確認します。
- クライアントと対話して、それが偽装されたパケットである可能性があるかどうかを判定します (たとえば、SYN Auth や SYN Cookie、またはソースへのパケットを削除してそれを再送信します)。
- 他に適用できる方法がない場合は、パケットをレート制限します。

DDoS Protection は、攻撃トラフィックをブロックし、残りのトラフィックをその目的の宛先に転送します。 攻撃の検出から数分以内に、Azure Monitor メトリックを使用してユーザーに通知します。 DDoS Protection Standard テレメトリへのログ記録を構成することによって、そのログを将来の分析のための使用可能なオプションに書き込むことができます。 DDoS Protection Standard のための Azure Monitor 内のメトリック データは、30 日間保持されます。

Microsoft は [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) と提携して、シミュレーションのために DDoS Protection を有効にしたパブリック IP アドレスに対してトラフィックを生成できるインターフェイスを構築しました。 BreakPoint Cloud シミュレーションを使用して、以下の操作を行うことができます。

- Microsoft Azure DDoS Protection Standard が Azure リソースを DDoS 攻撃から保護する方法を検証します
- DDoS 攻撃を受けているときにインシデント レスポンス プロセスを最適化します
- DDoS コンプライアンスを文書化します
- ネットワーク セキュリティ チームのトレーニングを行います

## <a name="next-steps"></a>次の手順

- [Azure DDoS Protection Standard を構成する](manage-ddos-protection.md)
