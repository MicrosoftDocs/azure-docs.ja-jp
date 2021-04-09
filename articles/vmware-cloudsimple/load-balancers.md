---
title: Azure VMware Solution by CloudSimple-CloudSimple プライベート クラウド用の負荷分散ソリューションを選択する
description: プライベート クラウドにアプリケーションをデプロイするときの負荷分散のオプションについて説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06d2305c209011e4fb1b8ee05a016d2e15a49833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898030"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>CloudSimple プライベート クラウドの負荷分散ソリューションを選択する

CloudSimple プライベート クラウドにアプリケーションをデプロイするときに、いずれかの負荷分散オプションを選択できます。

CloudSimple プライベート クラウドで仮想またはソフトウェアベースのロード バランサーを選択できます。または、Azure サブスクリプションで実行されている Azure L7 ロード バランサーを使用して、CloudSimple プライベート クラウドで実行されている Web 層の VM のフロントエンド処理を実行させることもできます。 ここでは、いくつかのオプションについて説明します。

## <a name="virtual-load-balancers"></a>仮想ロード バランサー

vCenter インターフェイスを使用して、仮想ロード バランサー アプライアンスを VMware 環境にデプロイし、アプリケーション トラフィックのフロントエンド処理を実行するように構成できます。

一般的なベンダーは次のとおりです。 http://nginx.org/en/docs/http/load_balancing.html F5- BigIP - Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 ロード バランサー

プライベート クラウドで実行されているアプリケーションの L7 ロード バランサーとして Azure Application Gateway を使用する場合、ロード バランサー ソフトウェアを管理する必要はありません。 ロード バランサー ソフトウェアは Azure によって管理されます。 プライベート クラウド内のすべての Web 層の VM では、プライベート IP アドレスが使用され、名前を解決するための追加の NAT 規則やパブリック IP アドレスは必要ありません。 Web 層の VM は、待機時間が短く、高帯域幅のプライベート接続を介して Azure Application Gateway と通信します。

このソリューションを構成する方法の詳細については、Azure Application Gateway を L7 ロードバランサーとして使用する方法に関するソリューション ガイドを参照してください。

## <a name="azure-internal-load-balancer"></a>Azure 内部ロード バランサー

Web フロントエンド層が Azure サブスクリプションの Azure vNet 内で実行され、アプリケーションの DB 層が CloudSimple プライベート クラウドの VMware VM で実行されているハイブリッド デプロイでアプリケーションを実行することを選択する場合は、トラフィックの管理のために、DB 層 VM の前で Azure 内部ロード バランサー (L4 ロード バランサー) を使用できます。

詳細については、Azure の[内部ロード バランサー](../load-balancer/components.md#frontend-ip-configurations)に関するドキュメントを参照してください。

## <a name="global-server-load-balancer"></a>グローバル サーバーのロード バランサー

DNS ベースのロード バランサーをお探しの場合は、Azure Marketplace で入手可能なサードパーティのソリューションを使用するか、ネイティブの Azure ソリューションを使用することができます。

Azure Traffic Manager は、世界中の Azure リージョン間およびオンプレミスのサービスにトラフィックを最適に分散することを可能にしながら、高可用性と応答性を実現する DNS ベースのトラフィック ロード バランサーです。 詳細は、Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) に関するドキュメントを参照してください。
