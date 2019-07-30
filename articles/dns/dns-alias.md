---
title: Azure DNS エイリアス レコードの概要
description: Microsoft Azure DNS のエイリアス レコードのサポートの概要について説明します。
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/7/2019
ms.author: victorh
ms.openlocfilehash: 5dfc00b1193117c22ba1c763bb0e75d9c4712222
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275738"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS エイリアス レコードの概要

Azure DNS エイリアス レコードは、DNS レコード セットを修飾するものです。 それらは、DNS ゾーン内で他の Azure リソースを参照できます。 たとえば、A レコードではなく Azure のパブリック IP アドレスを参照するエイリアス レコード セットを作成できます。 エイリアス レコード セットは、Azure のパブリック IP アドレスのサービス インスタンスを動的にポイントします。 その結果、エイリアス レコード セットは、DNS の解決時にシームレスに更新されます。

Azure DNS ゾーンでは、エイリアス レコード セットとして、次の種類のレコードがサポートされます。 

- A
- AAAA
- CNAME

> [!NOTE]
> A または AAAA レコードの種類に対してエイリアス レコードを使用して [Azure Traffic Manager プロファイル](../traffic-manager/quickstart-create-traffic-manager-profile.md)をポイントする場合は、Traffic Manager プロファイルにあるのが[外部エンドポイント](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)だけであることを確認する必要があります。 Traffic Manager の外部エンドポイントには、IPv4 または IPv6 アドレスを指定する必要があります。 エンドポイントで完全修飾ドメイン名 (FQDN) を使用することはできません。 できる限り、静的 IP アドレスを使用します。

## <a name="capabilities"></a>機能

- **DNS の A または AAAA レコード セットからパブリック IP リソースにポイントする**。 A または AAAA レコード セットを作成し、パブリック IP リソースをポイントするエイリアス レコード セットにすることができます。 パブリック IP アドレスが変更されるか削除されると、DNS レコード セットが自動的に変更されます。 正しくない IP アドレスをポイントする未解決の DNS レコードは回避されます。

- **DNS の A、AAAA または CNAME レコード セットから Traffic Manager プロファイルをポイントする**。 A/AAAA または CNAME レコード セットを作成し、エイリアス レコードを使用して Traffic Manager プロファイルをポイントすることができます。 従来の CNAME レコードはゾーンの頂点に対してサポートされていないため、ゾーンの頂点でトラフィックをルーティングする必要がある場合に特に便利です。 たとえば、Traffic Manager プロファイルが myprofile.trafficmanager.net で、ビジネスの DNS ゾーンが contoso.com であるものとします。 contoso.com (ゾーンの頂点) に対して A/AAAA の種類のエイリアス レコード セットを作成し、それで myprofile.trafficmanager.net をポイントすることができます。
- **Azure Content Delivery Network (CDN) エンドポイントをポイントする**。 これは、Azure Storage と Azure CDN を使って静的な Web サイトを作成する場合に便利です。
- **同じゾーン内の別の DNS レコード セットをポイントする**。 エイリアス レコードでは、同じ種類の別のレコード セットを参照できます。 たとえば、DNS の CNAME レコード セットは、別の CNAME レコード セットのエイリアスになることができます。 この配置は、一部のレコード セットをエイリアスにしたり、一部をエイリアスにしたくない場合に便利です。

## <a name="scenarios"></a>シナリオ

エイリアス レコードが一般的に使用されるいくつかのシナリオを次に示します。

### <a name="prevent-dangling-dns-records"></a>未解決の DNS レコードを防ぐ

従来の DNS レコードでよくある問題は、未解決のレコードです。 たとえば、IP アドレスの変更を反映するように更新されていない DNS レコードです。 その問題は、A、AAAA、または CNAME のレコードの種類で特に発生します。

従来の DNS ゾーン レコードでは、ターゲットの IP または CNAME が存在しなくなった場合、それに関連付けられている DNS レコードを手動で更新する必要があります。 一部の組織では、手動での更新が、プロセスの問題のため、またはロールと関連付けられているアクセス許可レベルの分離のため、間に合わないことがありました。 たとえば、ロールには、アプリケーションに属する CNAME または IP アドレスを削除する権限があるとします。 ただし、それらのターゲットをポイントする DNS レコードを更新するための十分な権限はありません。 DNS レコードの更新の遅延は、ユーザーにとっては停止になる可能性があります。

エイリアス レコードでは、DNS レコードと Azure リソースのライフサイクルを緊密に結合することで、未解決の参照が防止されます。 たとえば、パブリック IP アドレスまたは Traffic Manager プロファイルをポイントするエイリアス レコードとして修飾されている DNS レコードについて考えます。 これらの基になるリソースを削除すると、DNS エイリアス レコードが空のレコード セットになります。 削除されたリソースは参照されなくなります。

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>アプリケーションの IP アドレスが変化したら、DNS レコード セットを自動的に更新する

このシナリオは、前のシナリオに似ています。 アプリケーションが移動されたか、基になる仮想マシンが再起動されたとします。 基になるパブリック IP リソースの IP アドレスが変更されると、エイリアス レコードは自動的に更新されます。 これにより、古いパブリック IP アドレスが割り当てられている別のアプリケーションにユーザーが転送される潜在的なセキュリティ リスクが回避されます。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Zone Apex で負荷分散されたアプリケーションをホストする

DNS プロトコルでは、ゾーンの頂点での CNAME レコードの割り当てができません。 たとえば、ドメインが contoso.com の場合、somelable.contoso.com に対する CNAME レコードは作成できますが、contoso.com 自体に対する CNAME を作成することはできません。
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) の背後でアプリケーションの負荷分散を行っているアプリケーションの所有者にとっては、この制限によって問題が生じます。 Traffic Manager プロファイルを使用するには CNAME レコードを作成する必要があるため、ゾーンの頂点から Traffic Manager プロファイルをポイントすることはできません。

この問題は、エイリアス レコードを使用して解決されます。 CNAME レコードとは異なり、エイリアス レコードはゾーンの頂点で作成され、アプリケーションの所有者はそれを使用して、外部エンドポイントを含む Traffic Manager プロファイルを、ゾーンの頂点のレコードでポイントできます。 アプリケーションの所有者は、DNS ゾーン内の他のドメインで使用されているのと同じ Traffic Manager プロファイルをポイントします。

たとえば、contoso.com と www\.contoso.com で、同じ Traffic Manager プロファイルをポイントできます。 Azure Traffic Manager プロファイルでのエイリアス レコードの使用に関する詳細については、次の手順のセクションを参照してください。

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>ゾーンの頂点から Azure CDN エンドポイントをポイントする

Traffic Manager プロファイルの場合と同様に、エイリアス レコードを使用して DNS ゾーンの頂点から Azure CDN エンドポイントをポイントすることもできます。 これは、Azure Storage と Azure CDN を使って静的な Web サイトを作成する場合に便利です。 DNS 名の前に "www" を付けなくても、Web サイトにアクセスできるようになります。

たとえば、静的な Web サイトの名前が www.contoso.com の場合、DNS 名の前に www を付ける必要はなく、ユーザーは contoso.com を使ってサイトにアクセスできます。

前述のように、CNAME レコードはゾーンの頂点ではサポートされていません。 そのため、CNAME レコードを使用して contoso.com から CDN エンドポイントをポイントすることはできません。 代わりに、エイリアス レコードを使用して、ゾーンの頂点から直接 CDN エンドポイントをポイントすることができます。

> [!NOTE]
> Akamai から Azure CDN の CDN エンドポイントにゾーンの頂点をポイントすることは、現在サポートされていません。

## <a name="next-steps"></a>次の手順

エイリアス レコードの詳細については、次の記事を参照してください。

- [チュートリアル:Azure パブリック IP アドレスを参照するエイリアス レコードを構成する](tutorial-alias-pip.md)
- [チュートリアル:Traffic Manager で頂点のドメイン名をサポートするエイリアス レコードを構成する](tutorial-alias-tm.md)
- [DNS に関する FAQ](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
