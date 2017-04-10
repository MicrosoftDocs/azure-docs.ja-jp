---
title: "Premium Azure Redis Cache 向けの Virtual Network のサポートの構成 | Microsoft Docs"
description: "Premium レベル Azure Redis Cache インスタンスの Virtual Network のサポートを作成および管理する方法"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 50d8db29ccce1244387f1fe0e3e42e610575e483
ms.openlocfilehash: 057affeccd955245ac07fc5e174447562e81b959
ms.lasthandoff: 02/09/2017


---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Premium Azure Redis Cache の Virtual Network のサポートを構成する方法
Azure Redis Cache には、クラスタリング、永続性、仮想ネットワークのサポートといった Premium レベルの機能など、キャッシュのサイズと機能を柔軟に選択できるさまざまなキャッシュ サービスがあります。 VNet とは、クラウド内のプライベート ネットワークです。 VNet を使用して Azure Redis Cache インスタンスを構成する場合、パブリックにアドレスを指定することはできないため、VNet 内の仮想マシンとアプリケーションからしかアクセスできません。 この記事では、Premium Azure Redis Cache インスタンスの仮想ネットワークのサポートを構成する方法について説明します。

> [!NOTE]
> Azure Redis Cache では、クラシック VNet と ARM VNet の両方がサポートされています。
> 
> 

Premium キャッシュのその他の機能の詳細については、「 [Azure Redis Cache Premium レベルの概要](cache-premium-tier-intro.md)」を参照してください。

## <a name="why-vnet"></a>VNet を選ぶ理由
[Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) のデプロイにより、Azure Redis Cache のセキュリティと分離が強化されるとともに、サブネット、アクセス制御ポリシー、Azure Redis Cache へのアクセスをさらに制限する他の機能も提供されます。

## <a name="virtual-network-support"></a>Virtual Network のサポート
仮想ネットワーク (VNet) のサポートは、キャッシュの作成中に **[新規 Redis Cache]** ブレードで構成します。 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Premium 価格レベルを選択すると、キャッシュと同じサブスクリプションと場所にある VNet を選択することで、Azure Redis Cache VNet 統合を構成できます。 新しい VNet を使用するには、まず「[Azure ポータルを使用した仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)」または「[Azure ポータルを使用した仮想ネットワーク (従来型) の作成](../virtual-network/virtual-networks-create-vnet-classic-portal.md)」の手順に従って VNet を作成した後、**[新規 Redis Cache]** ブレードに戻り、Premium キャッシュを作成して構成します。

新しいキャッシュ用に VNet を構成するには、**[新規 Redis Cache]** ブレードの **[仮想ネットワーク]** をクリックし、ドロップダウン リストから目的の VNet を選択します。

![Virtual Network][redis-cache-vnet]

**[サブネット]** ボックスの一覧で目的のサブネットを選択し、必要な**静的 IP アドレス**を指定します。 クラシック VNet を使用している場合、**[静的 IP アドレス]** フィールドは省略可能です。何も指定しないと、選択したサブネットから&1; つのアドレスが選択されます。

> [!IMPORTANT]
> Azure Redis Cache を ARM VNet にデプロイする場合、キャッシュは、Azure Redis Cache インスタンス以外のリソースを含まない専用サブネット内に存在する必要があります。 ARM VNet で他のリソースが含まれるサブネットに Azure Redis Cache をデプロイしようとすると、そのデプロイは失敗します。
> 
> 

![Virtual Network][redis-cache-vnet-ip]

> [!IMPORTANT]
> サブネットの最初の&4; つのアドレスは予約済みのため、使用できません。 詳細については、「 [これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> 

キャッシュが作成されたら、**[リソース]** メニューの **[仮想ネットワーク]** をクリックすることで、VNet の構成を表示できます。

![Virtual Network][redis-cache-vnet-info]

VNet の使用時に Azure Redis Cache インスタンスに接続するには、次の例に示すように、接続文字列でキャッシュのホスト名を指定します。

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Azure Redis Cache VNet についてよく寄せられる質問 (FAQ)
次の一覧は、Azure Redis Cache のスケーリングに関するよく寄せられる質問への回答です。

* [Azure Redis Cache と VNet の誤った構成に関してよく見られる問題を教えてください](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Standard キャッシュまたは Basic キャッシュで VNet を使用できますか](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Redis Cache の作成が失敗するサブネットと成功するサブネットがあるのはなぜですか](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [VNET でキャッシュをホストしている場合、キャッシュ機能はすべて動作しますか](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Azure Redis Cache と VNet の誤った構成に関してよく見られる問題を教えてください
Azure Redis Cache が VNet でホストされている場合は、次の表にあるポートが使用されます。 これらのポートがブロックされている場合は、キャッシュが正しく機能しない可能性があります。 VNet で Azure Redis Cache を使用する場合、構成の誤りに関する最も一般的な問題は、これらのポートのうち&1; つ以上がブロックされていることです。

| ポート | 方向 | トランスポート プロトコル | 目的 | リモート IP |
| --- | --- | --- | --- | --- |
| 80、443 |送信 |TCP |Azure Storage/PKI (インターネット) に対する Redis の依存関係 |* |
| 53 |送信 |TCP/UDP |DNS (インターネット/VNet) に対する Redis の依存関係 |* |
| 6379, 6380 |受信 |TCP |Redis へのクライアント通信、Azure 負荷分散 |VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 8443 |受信/送信 |TCP |Redis の実装の詳細 |VIRTUAL_NETWORK |
| 8500 |受信 |TCP/UDP |Azure 負荷分散 |AZURE_LOADBALANCER |
| 10221-10231 |受信/送信 |TCP |Redis の実装の詳細 (リモート エンドポイントを VIRTUAL_NETWORK に制限できます) |VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 13000-13999 |受信 |TCP |Redis クラスターへのクライアント通信、Azure 負荷分散 |VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 15000-15999 |受信 |TCP |Redis クラスターへのクライアント通信、Azure 負荷分散 |VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 16001 |受信 |TCP/UDP |Azure 負荷分散 |AZURE_LOADBALANCER |
| 20226 |受信 + 送信 |TCP |Redis クラスターの実装の詳細 |VIRTUAL_NETWORK |

Azure Redis Cache のネットワーク接続要件には、仮想ネットワークで最初に満たされていないものがある可能性があります。 仮想ネットワーク内で使用したときに正常に動作させるためには、Azure Redis Cache に次の項目すべてが必要になります。

* 世界各国の Azure Storage エンドポイントに対する発信ネットワーク接続 これには、Azure Redis Cache インスタンスと同じリージョンにあるエンドポイントと、**他の** Azure リージョンにあるストレージ エンドポイントが含まれます。 Azure Storage エンドポイントは、次の DNS ドメインで解決されます: *table.core.windows.net*、*blob.core.windows.net*、*queue.core.windows.net*、*file.core.windows.net*。 
* *ocsp.msocsp.com*、*mscrl.microsoft.com*、*crl.microsoft.com* に対する発信ネットワーク接続。 この接続は、SSL 機能をサポートするために必要です。
* 仮想ネットワークの DNS 構成は、前述したすべてのエンドポイントとドメインを解決できるようにする必要があります。 これらの DNS 要件を満たすには、仮想ネットワークの有効な DNS インフラストラクチャを構成し、保守します。
* 次の DNS ドメインで解決される次の Azure Monitoring エンドポイントに対する発信ネットワーク接続: shoebox2-black.shoebox2.metrics.nsatc.net、north-prod2.prod2.metrics.nsatc.net、azglobal-black.azglobal.metrics.nsatc.net、shoebox2-red.shoebox2.metrics.nsatc.net、east-prod2.prod2.metrics.nsatc.net、azglobal-red.azglobal.metrics.nsatc.net。

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Standard キャッシュまたは Basic キャッシュで VNet を使用できますか
VNet は Premium キャッシュでのみ使用できます。

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Redis Cache の作成が失敗するサブネットと成功するサブネットがあるのはなぜですか
Azure Redis Cache を ARM VNet にデプロイする場合、キャッシュは、他の種類のリソースが含まれない専用サブネット内に存在する必要があります。 他のリソースが含まれる ARM VNet サブネットに Azure Redis Cache をデプロイしようとすると、そのデプロイは失敗します。 新しい Redis Cache を作成する前に、サブネット内の既存のリソースを削除する必要があります。

使用可能な IP アドレスが十分にあれば、複数の種類のリソースをクラシック VNet にデプロイできます。

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>VNET でキャッシュをホストしている場合、キャッシュ機能はすべて動作しますか
キャッシュが VNET の一部である場合は、VNET のクライアントだけがキャッシュにアクセスできます。 そのため、次のキャッシュ管理機能は現時点では動作しません。

* Redis コンソール - Redis コンソールでは VNET に含まれていない VM でホストされている redis-cli.exe クライアントを使用するため、キャッシュには接続できません。

## <a name="use-expressroute-with-azure-redis-cache"></a>Azure Redis Cache と ExpressRoute の使用
顧客は、 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 回線を自分の仮想ネットワーク インフラストラクチャに接続することで、オンプレミスのネットワークを Azure に拡張できます。 

既定では、新しく作成された ExpressRoute 回線は、発信インターネット接続を許可する既定のルートをアドバタイズします。 この構成を使用すると、クライアント アプリケーションは Azure Redis Cache を含む他の Azure エンドポイントに接続できます。

ただし、顧客の一般的な構成では、発信インターネット トラフィックを強制的にオンプレミスにフローさせる独自の既定のルート (0.0.0.0/0) を定義しています。 このトラフィック フローでは、Azure Redis Cache を使用した接続は必ず切断されます。これは、発信トラフィックがオンプレミスでブロックされるか、さまざまな Azure エンドポイントで有効ではなくなった、認識できないアドレス セットに NAT 処理されることが原因です。

解決策は、Azure Redis Cache を含むサブネット上で&1; つ (以上) のユーザー定義ルート (UDR) を定義することです。 UDR は、既定のルートに優先するサブネット固有のルートを定義します。

可能であれば、次の構成を使用することをお勧めします。

* ExpressRoute 構成は 0.0.0.0/0 をアドバタイズし、既定でオンプレミスのすべての発信トラフィックを強制的にトンネリングします。
* Azure Redis Cache を含むサブネットに適用される UDR では、次ホップの種類がインターネットである 0.0.0.0/0 を定義します (例については、この記事の後半を参照してください)。

これらの手順を組み合わせた結果として、サブネット レベル UDR は ExpressRoute 強制トンネリングよりも優先されるので、Azure Redis Cache からの発信インターネット アクセスを確保できます。

ExpressRoute を使用したオンプレミス アプリケーションから Azure Redis Cache インスタンスへの接続は、パフォーマンス上の理由から一般的な使用シナリオではありません (最適なパフォーマンスを得るには、Azure Redis Cache クライアントを Azure Redis Cache と同じリージョンに配置する必要があります)。ただし、このシナリオでは、発信ネットワークの経路は、社内プロキシを経由したり、オンプレミスに強制的にトンネリングしたりすることができません。 この処理を実行した場合、Azure Redis Cache からの発信ネットワーク トラフィックの実質的な NAT アドレスが変更されます。 Azure Redis Cache インスタンスの発信ネットワーク トラフィックの NAT アドレスを変更すると、上記のエンドポイントの多くに対する接続エラーが発生します。 その結果、Azure Redis Cache の作成が失敗します。

>[!IMPORTANT] 
>ExpressRoute 構成でアドバタイズされたルートよりも優先するには、UDR に定義されているルートを詳細にする **必要があります** 。 以下の例では、0.0.0.0/0 の広域なアドレス範囲を使用しているので、より詳細なアドレス範囲を使用するルート アドバタイズで誤って上書きされる可能性があります。

>[!WARNING]  
>Azure Redis Cache は、**パブリック ピアリング パスからプライベート ピアリング パスにルートを正しくクロスアドバタイズしていない** ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、大規模な Microsoft Azure の IP アドレス範囲について Microsoft からルート アドバタイズを受信します。 これらのアドレス範囲がプライベート ピアリング パスで誤ってクロスアドバタイズされている場合、Azure Redis Cache インスタンスのサブネットからのすべての発信ネットワーク パケットは、誤って顧客のオンプレミス ネットワーク インフラストラクチャに強制的にトンネリングされます。 このネットワーク フローでは、Azure Redis Cache が機能しません。 この問題を解決するには、パブリック ピアリング パスからプライベート ピアリング パスへのルートのクロスアドバタイズを停止します。


ユーザー定義ルートの背景情報については、この [概要](../virtual-network/virtual-networks-udr-overview.md)を参照してください。

ExpressRoute の詳細については、「 [ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>次のステップ
Premium キャッシュ機能をさらに使用する方法を学習します。

* [Azure Redis Cache Premium レベルの概要](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png


