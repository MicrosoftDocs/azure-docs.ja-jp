---
title: Azure VPN Gateway について
description: VPN Gateway とは何か、また VPN Gateway を使用して、サイト間、VNet 対 VNet、およびポイント対サイトの IPsec IKE VPN 仮想ネットワークに接続する方法について説明します。
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom: contperf-fy21q1
ms.openlocfilehash: c19e1156f897873defaba4cebaabb904d034ead3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97032747"
---
# <a name="what-is-vpn-gateway"></a>VPN ゲートウェイとは

VPN ゲートウェイは、特定の種類の仮想ネットワーク ゲートウェイで、パブリック インターネットを介して Azure 仮想ネットワークとオンプレミスの場所の間で暗号化されたトラフィックを送信するために使用されます。 VPN ゲートウェイを使用すると、Microsoft ネットワークを経由して Azure 仮想ネットワーク間で暗号化されたトラフィックを送信することもできます。 各仮想ネットワークには VPN ゲートウェイを 1 つだけ作成できます。 ただし、同一の VPN ゲートウェイに対して複数の接続を作成することができます。 同一の VPN ゲートウェイへの複数の接続を作成する場合、利用できるゲートウェイ帯域幅はすべての VPN トンネルによって共有されます。

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>仮想ネットワーク ゲートウェイとは

仮想ネットワーク ゲートウェイは、"*ゲートウェイ サブネット*" と呼ばれる、作成する特定のサブネットにデプロイされる 2 台以上の VM で構成されます。 仮想ネットワーク ゲートウェイ VM には、ルーティング テーブルが含まれ、特定のゲートウェイ サービスが実行されます。 これらの VM は、仮想ネットワーク ゲートウェイを作成するときに作成されます。 仮想ネットワーク ゲートウェイの一部である VM を直接構成することはできません。

仮想ネットワーク ゲートウェイを構成するときに、ゲートウェイの種類を指定する設定を構成します。 ゲートウェイの種類により、仮想ネットワーク ゲートウェイの使用方法と、ゲートウェイによって実行されるアクションが決まります。 ゲートウェイの種類 "Vpn" は、作成される仮想ネットワーク ゲートウェイの種類が "VPN ゲートウェイ" であることを示します。 これにより、異なるゲートウェイの種類を使用する ExpressRoute ゲートウェイと区別されます。 仮想ネットワークには、VPN ゲートウェイと ExpressRoute ゲートウェイの 2 つの仮想ネットワーク ゲートウェイを含めることができます。 詳細については、「[ゲートウェイの種類](vpn-gateway-about-vpn-gateway-settings.md#gwtype)」を参照してください。

仮想ネットワーク ゲートウェイの作成は、完了するまでに最大で 45 分かかる場合があります。 仮想ネットワーク ゲートウェイを作成すると、ゲートウェイ VM はゲートウェイ サブネットにデプロイされ、指定した設定で構成されます。 VPN ゲートウェイを作成した後、その VPN ゲートウェイと別の VPN ゲートウェイ間に IPsec/IKE VPN トンネル接続を作成するか (VNet 間)、VPN ゲートウェイとオンプレミスの VPN デバイス間にクロスプレミス IPsec/IKE VPN トンネル接続を作成できます (サイト間)。 また、ポイント対サイト VPN 接続 (OpenVPN、IKEv2、または SSTP 経由の VPN) を作成することもできます。これにより、会議や自宅などの遠隔地から仮想ネットワークに接続できます。

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>VPN ゲートウェイの構成

VPN ゲートウェイ接続は、特定の設定で構成された複数のリソースに依存します。 ほとんどのリソースは個別に構成できますが、一部のリソースは特定の順序で構成する必要があります。

### <a name="design"></a><a name="diagrams"></a>デザイン

VPN ゲートウェイ接続ではさまざまな構成が利用できることを理解しておくことが重要です。 また、どの構成が自分のニーズに最適かを判断する必要があります。 たとえば、ポイント対サイト、サイト間、および共存する ExpressRoute/サイト間接続にはすべて、それぞれ異なる指示と構成要件があります。 設計および接続トポロジ ダイアグラムの詳細については、[設計](design.md)に関するページを参照してください。

### <a name="planning-table"></a><a name="planningtable"></a>計画表

次の表は、ソリューションに最適な接続オプションを決定するのに役立ちます。

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>設定

リソースごとに選択した設定は、適切な接続を作成するうえで非常に重要です。 VPN Gateway の個々のリソースと設定については、「 [VPN Gateway の設定について](vpn-gateway-about-vpn-gateway-settings.md)」を参照してください。 この記事には、ゲートウェイの種類、ゲートウェイの SKU、VPN の種類、接続の種類、ゲートウェイ サブネット、ローカル ネットワーク ゲートウェイ、検討が必要なその他のさまざまなリソース設定を把握するのに役立つ情報が記載されています。

### <a name="deployment-tools"></a><a name="tools"></a>デプロイ ツール

Azure Portal などの構成ツールをどれか 1 つ使用して、リソースの作成と構成を開始できます。 その後、追加のリソースを構成したり、適用できる場合に既存のリソースを変更したりするために、PowerShell などの別のツールに切り替えることができます。 現時点では、すべてのリソースとリソースの設定を Azure Portal で構成することはできません。 各接続トポロジの記事の手順では、特定の構成ツールが必要な場合が指定されています。

## <a name="gateway-skus"></a><a name="gwsku"></a>ゲートウェイの SKU

仮想ネットワーク ゲートウェイを作成するときには、使用するゲートウェイの SKU を指定します。 ワークロード、スループット、機能、および SLA の種類に基づいて、要件を満たす SKU を選択します。

* サポートされる機能、実稼働環境と開発テスト環境、構成手順など、ゲートウェイ SKU について詳しくは、[VPN Gateway の設定のゲートウェイ SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku) に関する記事をご覧ください。
* レガシ SKU については、[レガシ SKU の使用](vpn-gateway-about-skus-legacy.md)に関する記事をご覧ください。

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>各ゲートウェイ SKU のトンネル数、接続数、およびスループット

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>可用性ゾーン

VPN ゲートウェイを Azure Availability Zones にデプロイすることができます。 これにより、仮想ネットワーク ゲートウェイに回復性、スケーラビリティ、高可用性が提供されます。 Azure Availability Zones にゲートウェイをデプロイすると、オンプレミス ネットワークの Azure への接続をゾーン レベルの障害から保護しながら、ゲートウェイを 1 つのリージョン内に物理的かつ論理的に分離できます。 「[Azure Availability Zones でのゾーン冗長仮想ネットワーク ゲートウェイについて](about-zone-redundant-vnet-gateways.md)」を参照してください。

## <a name="pricing"></a><a name="pricing"></a>価格

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

VPN ゲートウェイの SKU の詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。

## <a name="faq"></a><a name="faq"></a>FAQ

VPN Gateway に関してよく寄せられる質問については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」を参照してください。

## <a name="whats-new"></a><a name="new"></a>新機能

RSS フィードを購読し、[Azure 更新情報](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway)ページで、最新の VPN Gateway 機能の更新を確認します。

## <a name="next-steps"></a>次のステップ

- 詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」を参照してください。
- [サブスクリプションとサービスの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)に関するページを参照してください。
- Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。
