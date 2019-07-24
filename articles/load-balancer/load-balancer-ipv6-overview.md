---
title: Azure Load Balancer の IPv6 の概要
titlesuffix: Azure Load Balancer
description: Azure Load Balancer と負荷分散された VM に対する IPv6 サポートについて説明します。
services: load-balancer
documentationcenter: na
author: KumudD
keywords: ipv6, azure load balancer, デュアル スタック, パブリック IP, ネイティブ ipv6, モバイル, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: kumud
ms.openlocfilehash: 894a56c2e51e8fa8a2d72253563d218416ace4cb
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161935"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Azure Load Balancer の IPv6 の概要


>[!NOTE] 
>Azure Load Balancer では、Basic と Standard の 2 種類がサポートされています。 この記事では、Basic Load Balancer について説明します。 Standard Load Balancer について詳しくは、[Standard Load Balancer の概要](load-balancer-standard-overview.md)に関するページをご覧ください。

インターネットに接続するロード バランサーは、IPv6 アドレスでデプロイできます。 これにより、IPv4 接続に加えて次の機能を使用できます。

* ロード バランサーを介したパブリック インターネット クライアントと Azure Virtual Machines (VMs) の間のネイティブなエンド ツー エンドの IPv6 接続。
* VM と IPv6 対応のパブリック インターネット クライアントの間のネイティブなエンド ツー エンドの IPv6 送信接続。

次の図は、Azure Load Balancer の IPv6 の機能を示します。

![IPv6 を使用した Azure Load Balancer](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

一度デプロイすると、IPv4 または IPv6 対応のインターネット クライアントがインターネットに接続された Azure Load Balancer の IPv4 または IPv6 のパブリック アドレスと通信できるようになります。 ロード バランサーは、ネットワーク アドレス変換 (NAT) を使用して IPv6 のパケットを VM のプライベート IPv6 アドレスにルーティングします。 IPv6 インターネット クライアントは、VM の IPv6 アドレスと直接通信できません。

## <a name="features"></a>機能

Azure Resource Manager を介してデプロイされた VM のネイティブ IPv6 サポートは、次の機能を提供します。

1. インターネット上の IPv6 クライアントに対する負荷分散された IPv6 サービス
2. VM 上の IPv6 および IPv4 のネイティブなエンドポイント (「デュアル スタック」)
3. 受信および送信で開始されるネイティブ IPv6 接続
4. TCP、UDP、HTTP(S) などのサポートされているプロトコルによる幅広いサービス アーキテクチャの有効化

## <a name="benefits"></a>メリット

この機能により、主に次のようなメリットを享受できます。

* 新しいアプリケーションが IPv6 のみのクライアントにアクセスできることを必要とする政府の規制に対応する
* モバイルやモノのインターネット (IOT) の開発者がデュアル スタック (IPv4 + IPv6) の Azure Virtual Machines を使用して増え続けるモバイルおよび IOT のマーケットに対応する

## <a name="details-and-limitations"></a>詳細と制限

詳細

* Azure DNS サービスにはロード バランサー用の IPv4 A と IPV6 AAAA の両方の名前記録が含まれており、2 つの記録を使用して応答します。 どちらのアドレス (IPv4 または IPv6) と通信するかは、クライアントが選択します。
* VM がインターネットに接続されたパブリック IPv6 デバイスとの接続を開始すると、VM の発信元 IPv6 アドレスはロード バランサーのパブリック IPv6 アドレスにネットワーク アドレス変換 (NAT) されます。
* Linux オペレーティング システムを実行している VM は、DHCP 経由で IPv6 IP アドレスを受信するように構成する必要があります。 Azure ギャラリー内の Linux イメージの多くは、既に IPv6 をサポートするように構成されています。 詳細については、「 [Linux VM の DHCPv6 の設定](load-balancer-ipv6-for-linux.md)
* お使いのロード バランサーで正常性プローブを使用するように選択した場合は、IPv4 プローブを作成し、IPv4 と IPv6 の両方のエンドポイントで使用します。 お使いの VM 上のサービスに障害が発生した場合、IPv4 と IPv6 の両方のエンドポイントはローテーションから外されます。

制限事項

* Azure Portal に IPv6 の負荷分散規則は追加できません。 規則はテンプレート、CLI、PowerShell を使用してのみ作成できます。
* 既存の VM を IPv6 のアドレスを使用するようにアップグレードしないでください。 新しい VM をデプロイする必要があります。
* 1 つの IPv6 アドレスは、VM ごとに 1 つのネットワーク インターフェイスに割り当てることができます。
* パブリック IPv6 アドレスを VM に割り当てることはできません。 ロード バランサーにのみ割り当てることができます。
* パブリック IPv6 アドレスの DNS 逆引き参照は構成できません。
* IPv6 アドレスを持つ VM は、Azure Cloud Service のメンバーにすることはできません。 Azure Virtual Network (VNet) に接続して、IPv4 アドレスで互いに通信できます。
* プライベート IPv6 アドレスはリソース グループ内の個々 の VM 上にデプロイできますが、スケール セットを介してリソース グループにデプロイすることはできません。
* Azure VMs は IPv6 を介して他の VM、他の Azure サービス、またはオンプレミスのデバイスに接続できません。 Azure Load Balancer とのみ IPv6 を介して通信できます。 ただし、これらの他のリソースとは IPv4 を使用して通信できます。
* IPv4 のネットワーク セキュリティ グループ (NSG) 保護は、デュアル スタック (IPv4 + IPv6) デプロイメントでサポートされています。 NSG は IPv6 のエンドポイントには適用されません。
* VM 上の IPv6 エンドポイントはインターネットに直接公開されません。 ロード バランサーの内側にあります。 ロード バランサー規則で指定されているポートのみ、IPv6 を介してアクセスできます。
* IPv6 の IdleTimeout パラメーターの変更は、**現在サポートされていません**。 既定では 4 分です。
* IPv6 の loadDistributionMethod パラメーターの変更は、**現在サポートされてません**。
* 予約済み IPv6 IP (IPAllocationMethod = static) は、**現在サポートされていません**。
* NAT64 (IPv6 の IPv4 への変換) はサポートされていません。

## <a name="next-steps"></a>次の手順

IPv6 でロード バランサーをデプロイする方法について説明します。

* [リージョンごとの IPv6 の使用の可否](https://go.microsoft.com/fwlink/?linkid=828357)
* [テンプレートを使用して IPv6 でロード バランサーをデプロイする](load-balancer-ipv6-internet-template.md)
* [Azure PowerShell を使用して IPv6 でロード バランサーをデプロイする](load-balancer-ipv6-internet-ps.md)
* [Azure CLI を使用して IPv6 でロード バランサーをデプロイする](load-balancer-ipv6-internet-cli.md)
