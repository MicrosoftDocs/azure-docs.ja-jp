---
title: Private Zones のシナリオ - Azure DNS
description: この記事では、Azure DNS Private Zones を使用するための一般的なシナリオについて説明します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76939336"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS Private Zones のシナリオ

Azure DNS Private Zones は、仮想ネットワーク内での名前解決と仮想ネットワーク間での名前解決を提供します。 この記事では、この機能を使用して実現できる一般的なシナリオについて説明します。

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>シナリオ: 1 つの仮想ネットワークをスコープとする名前解決
このシナリオでは、仮想マシン (VM) を含む多数の Azure リソースがある Azure 内の仮想ネットワークがあります。 仮想ネットワーク内から特定のドメイン名 (DNS ゾーン) を使用してリソースを解決します。そのためには、名前解決をプライベートで実行し、インターネットからアクセスできないようにする必要があります。 さらに、VNET 内の VM が Azure によって DNS ゾーンに自動的に登録される必要があります。 

このシナリオを図示すると次のようになります。 "A" という名前の仮想ネットワークに、2 つの VM (VNETA-VM1 と VNETA-VM2) が含まれています。 それぞれが、関連付けられたプライベート IP を持っています。 Contoso.com という名前のプライベート ゾーンを作成し、この仮想ネットワークを登録仮想ネットワークとしてリンクすると、Azure DNS によって、図のように 2 つの A のレコードがゾーン内に自動的に作成されます。 これで、VNETA-VM2.contoso.com を解決するための VNETA-VM1 からの DNS クエリは、VNETA-VM2 のプライベート IP を含む DNS 応答を受信します。 さらに、VNETA VM2 から発行された VNETA VM1 (10.0.0.1) のプライベート IP に対する逆引き DNS クエリ (PTR) は、想定どおりに VNETA-VM1 の名前を含む DNS 応答を受信します。 

![単一仮想ネットワークの解決](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>シナリオ: 仮想ネットワーク間での名前解決

このシナリオは、プライベート ゾーンを複数の仮想ネットワークに関連付ける必要がある、もっと一般的なケースです。 このシナリオは、複数のスポーク仮想ネットワークが接続される中央ハブ仮想ネットワークが 1 つある、ハブ アンド スポーク モデルなどのアーキテクチャに適しています。 プライベート ゾーンに中央ハブ仮想ネットワークを登録仮想ネットワークとしてリンクでき、スポーク仮想ネットワークを解決仮想ネットワークとしてリンクできます。 

次の図は、このシナリオの 2 つの仮想ネットワーク (A と B) のみがある単純なバージョンを示しています。A が登録仮想ネットワークとして指定され、B が解決仮想ネットワークとして指定されます。 その意図は、両方の仮想ネットワークで共通のゾーン contoso.com を共有することです。 ゾーンを作成し、解決仮想ネットワークと登録仮想ネットワークをゾーンにリンクすると、Azure によって仮想ネットワーク A から VM (VNETA-VM1 と VNETA-VM2) の DNS レコードが自動的に登録されます。解決仮想ネットワーク B 内の VM の DNS レコードをゾーンに手動で追加することもできます。この設定では、正引きと逆引きの DNS クエリで次の動作が観察されます。
* 解決仮想ネットワーク B 内の VNETB VM1 から VNETA-VM1.contoso.com に対する DNS クエリは、VNETA-VM1 のプライベート IP を含む DNS 応答を受信します。
* 解決仮想ネットワーク B 内の VNETB-VM2 からの 10.1.0.1 に対する逆引き DNS (PTR) クエリは、FQDN VNETB-VM1.contoso.com を含む DNS 応答を受信します。  
* 解決仮想ネットワーク B 内の VNETB-VM3 からの 10.0.0.1 に対する逆引き DNS (PTR) クエリは、NXDOMAIN を受信します。 その理由は、逆引き DNS クエリのスコープが、同じ仮想ネットワークに限定されているためです。 


![複数の仮想ネットワークの解決](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>シナリオ: 水平分割機能

このシナリオでは、同じ DNS ゾーンのクライアントの存在場所 (Azure 内部またはインターネット上) に応じて、異なる DNS 解決動作を実現するユース ケースがあります。 たとえば、アプリケーションに機能や動作が異なるプライベート バージョンとパブリック バージョンがあるが、両方のバージョンで、同じドメイン名を使用するとします。 このシナリオは、Azure DNS で同じ名前のパブリック DNS ゾーンとプライベート ゾーンを作成することで実現できます。

次の図は、このシナリオを示しています。 プライベート IPとパブリック IP の両方が割り当てられた 2 つの VM (VNETA-VM1 と VNETA-VM2) が含まれる仮想ネットワーク A があります。 Contoso.com という名前のパブリック DNS ゾーンを作成し、これらの VM のパブリック IP を DNS レコードとしてゾーンに登録します。 A を登録仮想ネットワークとして指定する同名の contoso.com というプライベート DNS ゾーンも作成します。 Azure によって、VM がそれぞれのプライベート IP を指す A のレコードとしてプライベート ゾーンに自動的に登録されます。

これで、インターネット クライアントが VNETA-VM1.contoso.com を検索する DNS クエリを発行すると、Azure は、パブリック ゾーンからパブリック IP レコードを返します。 同じ DNS クエリが、同じ仮想ネットワーク A 内の別の VM (たとえば VNETA-VM2) から発行された場合、Azure は、プライベート ゾーンからプライベート IP レコードを返します。 

![分割 Brian 解決](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>次のステップ
プライベート DNS ゾーンの詳細については、「[Using Azure DNS for private domains (プライベート ドメインでの Azure DNS の使用)](private-dns-overview.md)」をご覧ください。

Azure DNS で[プライベート DNS ゾーンを作成する](./private-dns-getstarted-powershell.md)方法を学びます。

「[DNS ゾーンとレコードの概要](dns-zones-records.md)」でDNS ゾーンとレコードについて学びます。

Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。

