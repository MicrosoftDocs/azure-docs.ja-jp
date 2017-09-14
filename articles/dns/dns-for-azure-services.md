---
title: "他の Azure サービスと共に Azure DNS を使用する | Microsoft Docs"
description: "Azure DNS を使用して 他の Azure サービスの名前を解決する方法を理解します"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 5b95d5455c08d09a088c740e9df9605d3d7f719b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Azure DNS を他の Azure サービスと使用する方法

Azure DNS は、ホストされる DNS 管理および名前解決サービスです。 このサービスによって、Azure にデプロイしている他のアプリケーションやサービスのパブリック DNS 名を作成できます。 カスタム ドメイン内の Azure サービスの名前の作成は、サービスに適した種類のレコードを追加するだけの単純な作業です。

* IP アドレスが動的に割り当てられる場合は、Azure でサービスに対して作成された DNS 名に対応する DNS CNAME レコードを作成する必要があります。 DNS の標準により、ゾーンの頂点でCNAME レコードを使用することはできません。
* IP アドレスが静的に割り当てられる場合は、任意の名前を使用して DNS A レコードを作成でき、ゾーンの頂点で *ネイキッド ドメイン* を作成することもできます。

次の表は、さまざまな Azure サービスで使用できる、サポートされているレコードの種類の要点を示しています。 この表からわかるように、Azure DNS は、インターネットに接続するネットワーク リソースの DNS レコードのみをサポートします。 Azure DNS は、内部アドレスやプライベート アドレスの名前解決のために使用することはできません。

| Azure サービス | ネットワーク インターフェイス | Description |
| --- | --- | --- |
| Application Gateway |[フロントエンド パブリック IP](dns-custom-domain.md#public-ip-address) |DNS A または CNAME レコードを作成できます。 |
| Load Balancer |[フロントエンド パブリック IP](dns-custom-domain.md#public-ip-address)  |DNS A または CNAME レコードを作成できます。 Load Balancer には、動的に割り当てられる IPv6 パブリック IP アドレスを設定できます。 したがって、IPv6 アドレス用の CNAME レコードを作成する必要があります。 |
| Traffic Manager |パブリック名 |Traffic Manager プロファイルに割り当てられている .trafficmanager.net 名にマップする CNAME のみを作成できます。 詳細については、「 [Traffic Manager のしくみ](../traffic-manager/traffic-manager-overview.md#traffic-manager-example)」をご覧ください。 |
| クラウド サービス |[パブリック IP](dns-custom-domain.md#public-ip-address) |静的に割り当てられる IP アドレスには、DNS A レコードを作成できます。 動的に割り当てられる IP アドレスには、 *cloudapp.net* 名にマップする CNAME レコードを作成する必要があります。 クラシック ポータルで作成された VM はクラウド サービスとしてデプロイされるため、このルールが適用されます。 詳細については、「 [Azure クラウド サービスのカスタム ドメイン名の構成](../cloud-services/cloud-services-custom-domain-name-portal.md)」を参照してください。 |
| App Service | [外部 IP](dns-custom-domain.md#app-service-web-apps) |外部 IP アドレスには、DNS A レコードを作成できます。 それ以外の場合は、azurewebsites.net 名にマップする CNAME レコードを作成する必要があります。 詳細については、「 [Azure アプリへのカスタム ドメイン名のマッピング](../app-service-web/web-sites-custom-domain-name.md) |
| Resource Manager VM |[パブリック IP](dns-custom-domain.md#public-ip-address) |Resource Manager VM はパブリック IP アドレスを持つことができます。 パブリック IP アドレスを持つ VM は、ロード バランサーの背後に配置されることもあります。 パブリック アドレスの DNS A または CNAME レコードを作成できます。 このカスタム名を使用して、ロード バランサーの VIP をバイパスできます。 |
| クラシック VM |[パブリック IP](dns-custom-domain.md#public-ip-address) |PowerShell または CLI は使用して作成したクラシック VM には、動的または静的 (予約済み) 仮想アドレスを構成できます。 それぞれに DNS CNAME または A レコードを作成できます。 |

