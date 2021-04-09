---
title: Azure DNS を他の Azure サービスで使用する
description: このラーニング パスでは、Azure DNS を使用して他の Azure サービスの名前を解決する方法を説明します
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76937253"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Azure DNS を他の Azure サービスと使用する方法

Azure DNS は、ホストされる DNS 管理および名前解決サービスです。 これを使用して、Azure にデプロイしている他のアプリケーションやサービスのためにパブリック DNS 名を作成できます。 カスタム ドメイン内の Azure サービスのために名前を作成することは簡単です。 そのサービスの正しい種類のレコードを追加するだけです。

* IP アドレスが動的に割り当てられる場合は、Azure でサービスに対して作成された DNS 名にマップされる DNS CNAME レコードを作成できます。 DNS の標準により、ゾーンの頂点でCNAME レコードを使用することはできません。 代わりに、エイリアス レコードを使用できます。 詳細については、「[チュートリアル: Azure パブリック IP アドレスを参照するエイリアス レコードを構成する](tutorial-alias-pip.md)」を参照してください。
* 静的に割り当てられる IP アドレスについては、任意の名前を使用して DNS A レコードを作成することができ、それにはゾーンの頂点の *ネイキッド ドメイン* 名が含まれます。

次の表は、さまざまな Azure サービスで使用できる、サポートされているレコードの種類の要点を示しています。 表が示すように、Azure DNS は、インターネットに接続するネットワーク リソースの DNS レコードのみをサポートします。 Azure DNS は、内部アドレスやプライベート アドレスの名前解決には使用できません。

| Azure サービス | ネットワーク インターフェイス | 説明 |
| --- | --- | --- |
| Azure Application Gateway |[フロントエンド パブリック IP](dns-custom-domain.md#public-ip-address) |DNS A または CNAME レコードを作成できます。 |
| Azure Load Balancer |[フロントエンド パブリック IP](dns-custom-domain.md#public-ip-address) |DNS A または CNAME レコードを作成できます。 Load Balancer には、動的に割り当てられる IPv6 パブリック IP アドレスを設定できます。 IPv6 アドレス用の CNAME レコードを作成してください。 |
| Azure の Traffic Manager |パブリック名 |Traffic Manager プロファイルに割り当てられている trafficmanager.net 名にマップされるエイリアス レコードのみを作成できます。 詳細については、「[Tutorial: Configure an alias record to support apex domain names with Traffic Manager (チュートリアル: Traffic Manager で頂点のドメイン名をサポートするエイリアス レコードを構成する)](tutorial-alias-tm.md)」を参照してください。 |
| Azure クラウド サービス |[パブリック IP](dns-custom-domain.md#public-ip-address) |静的に割り当てられる IP アドレスには、DNS A レコードを作成できます。 動的に割り当てられる IP アドレスには、 *cloudapp.net* 名にマップする CNAME レコードを作成する必要があります。|
| Azure App Service | [外部 IP](dns-custom-domain.md#app-service-web-apps) |外部 IP アドレスには、DNS A レコードを作成できます。 それ以外の場合は、azurewebsites.net 名にマップする CNAME レコードを作成する必要があります。 詳細については、[Azure アプリへのカスタム ドメイン名のマッピング](../app-service/app-service-web-tutorial-custom-domain.md)に関するページを参照してください。 |
| Azure Resource Manager VM |[パブリック IP](dns-custom-domain.md#public-ip-address) |Resource Manager VM はパブリック IP アドレスを持つことができます。 パブリック IP アドレスを持つ VM は、ロード バランサーの背後にも配置できます。 パブリック アドレスには、DNS A、CNAME、またはエイリアス レコードを作成できます。 このカスタム名を使用して、ロード バランサーの VIP をバイパスできます。 |
| クラシック VM |[パブリック IP](dns-custom-domain.md#public-ip-address) |PowerShell または CLI は使用して作成するクラシック VM は、動的アドレスまたは静的 (予約済み) 仮想アドレスを指定して構成できます。 それぞれ、DNS CNAME または A レコードを作成できます。 |
