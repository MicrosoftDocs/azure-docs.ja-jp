---
title: "Azure ExpressRoute のルーティングの要件 | Microsoft Docs"
description: "このページでは、ExpressRoute 回線のルーティングを構成および管理するための詳細な要件について説明します。"
documentationcenter: na
services: expressroute
author: osamazia
manager: ganesr
editor: 
ms.assetid: 5b382e79-fa3f-495a-a764-c5ff86af66a2
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2017
ms.author: osamam
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: e678062429605db34c1e6c835841aaf2508d81a3
ms.lasthandoff: 03/03/2017


---
# <a name="expressroute-routing-requirements"></a>ExpressRoute のルーティングの要件
ExpressRoute を使用して Microsoft クラウド サービスに接続するには、ルーティングをセットアップして管理する必要があります。 一部の接続プロバイダーでは、ルーティングのセットアップと管理が管理されたサービスとして提供されています。 このサービスが提供されているかどうか、接続プロバイダーに問い合わせてください。 提供されていない場合は、次の要件に従う必要があります。 

接続を容易にするために設定する必要があるルーティング セッションの説明については、[回線とルーティング ドメイン](expressroute-circuit-peerings.md)に関する記事をご覧ください。

> [!NOTE]
> Microsoft では、高可用性構成のためのルーター冗長化プロトコル (HSRP、VRRP など) をサポートしていません。 代わりに、ピアリングごとの BGP セッションの冗長ペアに依存して高可用性を実現します。
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>ピアリングに使用する IP アドレス
ネットワークと Microsoft のエンタープライズ エッジ (MSEE) ルーター間のルーティングを構成するには、IP アドレスのいくつかのブロックを予約する必要があります。 このセクションでは、要件の一覧を示すと共に、これらの IP アドレスを取得および使用する方法に関する規則について説明します。

### <a name="ip-addresses-used-for-azure-private-peering"></a>Azure プライベート ピアリングに使用する IP アドレス
ピアリングは、プライベート IP アドレスまたはパブリック IP アドレスを使用して構成できます。 ルートを構成するために使用されるアドレス範囲と Azure で仮想ネットワークを作成するために使用されるアドレス範囲とが重複しないようにする必要があります。 

* ルーティング インターフェイス用に、1 つの /29 サブネットまたは 2 つの /30 サブネットを予約する必要があります。
* ルーティングに使用するサブネットには、プライベート IP アドレスまたはパブリック IP アドレスを指定できます。
* サブネットと、Microsoft クラウドで使用するために顧客によって予約された範囲とが競合しないようにする必要があります。
* /29 サブネットを使用すると、2 つの /30 サブネットに分割されます。 
  * 最初の /30 サブネットはプライマリ リンク用に使用され、2 つ目の /30 サブネットはセカンダリ リンク用に使用されます。
  * それぞれの /30 サブネットに対し、ルーター上で /30 サブネットの最初の IP アドレスを使用する必要があります。 Microsoft は、/30 サブネットの&2; 番目の IP アドレスを使用して BGP セッションをセットアップします。
  * [可用性 SLA](https://azure.microsoft.com/support/legal/sla/) を有効にするには、両方の BGP セッションをセットアップする必要があります。  

#### <a name="example-for-private-peering"></a>プライベート ピアリング用の例
a.b.c.d/29 を使用してピアリングをセットアップすることを選択した場合、このサブネットは&2; つの /30 サブネットに分割されます。 次の例では、a.b.c.d/29 サブネットがどのように使用されるかについて注目します。 

a.b.c.d/29 は、a.b.c.d/30 と a.b.c.d+4/30 に分割され、プロビジョニング API を介して Microsoft に渡されます。 a.b.c.d+1 をプライマリ PE の VRF IP として使用すると、Microsoft は a.b.c.d+2 をプライマリ MSEE の VRF IP として使用します。 a.b.c.d+5 をセカンダリ PE の VRF IP として使用すると、Microsoft は a.b.c.d+6 をセカンダリ MSEE の VRF IP として使用します。

ここで、192.168.100.128/29 を選択してプライベート ピアリングをセットアップするとします。 192.168.100.128/29 には、192.168.100.128 ～ 192.168.100.135 の範囲のアドレスが含まれています。この中で、

* 192.168.100.128/30 は link1 に割り当てられます。プロバイダーは 192.168.100.129 を使用し、Microsoft は 192.168.100.130 を使用します。
* 192.168.100.132/30 は link2 に割り当てられます。プロバイダーは 192.168.100.133 を使用し、Microsoft は 192.168.100.134 を使用します。

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>Azure パブリック ピアリングと Microsoft ピアリングに使用する IP アドレス
ユーザーは、所有しているパブリック IP アドレスを使用して BGP セッションをセットアップする必要があります。 Microsoft は、ルーティング インターネット レジストリおよびインターネット ルーティング レジストリを介して IP アドレスの所有権を確認できる必要があります。 

* ユーザーは、一意の /29 サブネットまたは&2; つの /30 サブネットを使用して、ExpressRoute 回線ごとに (複数存在する場合) それぞれのピアリングの BGP ピアリングをセットアップする必要があります。 
* /29 サブネットを使用すると、2 つの /30 サブネットに分割されます。 
  * 最初の /30 サブネットはプライマリ リンク用に使用され、2 つ目の /30 サブネットはセカンダリ リンク用に使用されます。
  * それぞれの /30 サブネットに対し、ルーター上で /30 サブネットの最初の IP アドレスを使用する必要があります。 Microsoft は、/30 サブネットの&2; 番目の IP アドレスを使用して BGP セッションをセットアップします。
  * [可用性 SLA](https://azure.microsoft.com/support/legal/sla/) を有効にするには、両方の BGP セッションをセットアップする必要があります。

## <a name="public-ip-address-requirement"></a>パブリック IP アドレス要件
### <a name="private-peering"></a>プライベート ピアリング
パブリックまたはプライベート IPv4 アドレスをプライベート ピアリングに使用することもできます。 プライベート ピアリングの場合に他の顧客とのアドレスの重複が発生しないように、トラフィックのエンド ツー エンドの分離が提供されます。 これらのアドレスはインターネットにはアドバタイズされません。 

### <a name="public-peering"></a>パブリック ピアリング
Azure パブリック ピアリング パスを利用すれば、パブリック IP アドレスで Azure にホストされているすべてのサービスに接続できます。 たとえば、 [ExpessRoute FAQ](expressroute-faqs.md) の一覧にあるサービスや Microsoft Azure で ISV によりホストされているサービスです。 パブリック ピアリングでの Microsoft Azure への接続は常にネットワークから Microsoft ネットワークに対して開始されます。 Microsoft ネットワークに送信されるトラフィックには、パブリック IP アドレスを使用する必要があります。

### <a name="microsoft-peering"></a>Microsoft ピアリング
Microsoft ピアリング パスにより、Azure パブリック ピアリング パスでサポートされていない Microsoft クラウド サービスに接続できます。 そのようなサービスには、Exchange Online、SharePoint Online、Skype for Business、CRM Online のような Office 365 サービスがあります。 Microsoft では、Microsoft ピアリングで双方向接続をサポートしています。 Microsoft クラウド サービスに送信されるトラフィックが Microsoft ネットワークに入るには、有効なパブリック IPv4 アドレスを使用している必要があります。

以下のレジストリのいずれかで IP アドレスと AS 番号が自分に登録されていることを確認します。

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](http://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](http://www.radb.net/)
* [ALTDB](http://altdb.net/)

> [!IMPORTANT]
> ExpressRoute 経由で Microsoft にアドバタイズされるパブリック IP アドレスは、インターネットにアドバタイズしないでください。 他の Microsoft サービスへの接続が切断される場合があります。 ただし、Microsoft 内の O365 エンドポイントと通信するネットワーク内のサーバーが使用するパブリック IP アドレスは、ExpressRoute 経由でアドバタイズされることがあります。 
> 
> 

## <a name="dynamic-route-exchange"></a>動的なルート交換
ルーティングの交換は eBGP プロトコル上で実行されます。 MSEE とルーターとの間に EBGP セッションが確立されます。 BGP セッションの認証は必須ではありません。 必要な場合は、MD5 ハッシュを構成することができます。 BGP セッションの構成については、[ルーティングの構成](expressroute-howto-routing-classic.md)に関する記事および[回線のプロビジョニング ワークフローと回線の状態](expressroute-workflows.md)に関する記事をご覧ください。

## <a name="autonomous-system-numbers"></a>自律システム番号
Microsoft は、Azure パブリック、Azure プライベート、および Microsoft ピアリングのために AS 12076 を使用します。 ASN 65515 ～ 65520 は、内部使用のために予約されています。 16 ビットと 32 ビットの両方の AS 番号がサポートされています。

データ転送の対称性に関する要件はありません。 転送パスとリターン パスは、異なるルーター ペアを通過することができます。 同じルートについては、自分に属している複数の回線ペアのどちらかの側からアドバタイズする必要があります。 ルートのメトリックは同一である必要はありません。

## <a name="route-aggregation-and-prefix-limits"></a>ルート集約とプレフィックスの制限
Azure プライベート ピアリングを介してアドバタイズされるプレフィックスは、最大で 4,000 個がサポートされます。 ExpressRoute Premium アドオンが有効になっている場合、このプレフィックス数を 10,000 に増やすことができます。 Azure パブリックおよび Microsoft ピアリングの場合、BGP セッションあたり最大で 200 個のプレフィックスを使用できます。 

プレフィックスの数がこの制限を超えると、BGP セッションは切断されます。 既定のルートは、プライベート ピアリング リンクのみで使用できます。 プロバイダーは、Azure パブリック パスと Microsoft ピアリング パスから既定のルートおよびプライベート IP アドレス (RFC 1918) をフィルターで除外する必要があります。 

## <a name="transit-routing-and-cross-region-routing"></a>トランジット ルーティングおよびリージョン間ルーティング
ExpressRoute をトランジット ルーターとして構成することはできません。 トランジット ルーティング サービスについては、接続プロバイダーに依存する必要があります。

## <a name="advertising-default-routes"></a>既定のルートのアドバタイズ
既定のルートは、Azure プライベート ピアリング セッションでのみ許可されます。 その場合、Microsoft は、関連付けられている仮想ネットワークからのすべてのトラフィックをユーザーのネットワークにルーティングします。 プライベート ピアリングに既定のルートをアドバタイズすると、Azure からのインターネット パスがブロックされます。 Azure でホストされるサービスのトラフィックをインターネットとの間で送受信するには、会社のエッジに依存する必要があります。 

 他の Azure サービスおよびインフラストラクチャ サービスへの接続を有効にするには、次のどちらかの条件が満たされている必要があります。

* Azure パブリック ピアリングが有効になっていて、トラフィックをパブリック エンドポイントにルーティングするように設定されている。
* ユーザー定義のルーティングを使用して、インターネット接続を必要とするすべてのサブネットに対してインターネット接続を許可している。

> [!NOTE]
> 既定のルートをアドバタイズすると、Windows VM や他の VM のライセンス認証が破棄されます。 これを回避する方法については、 [このページ](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) を参照してください。
> 
> 

## <a name="support-for-bgp-communities"></a>BGP コミュニティのサポート
ここでは、ExpressRoute で BGP コミュニティがどのように使用されるかについて概説します。 Microsoft は、パブリックおよび Microsoft ピアリング パスのルートに適切なコミュニティ値をタグ付けしてアドバタイズします。 その理由とコミュニティ値の詳細については以降に示します。 ただし、Microsoft は、Microsoft にアドバタイズされるルートにタグ付けされたすべてのコミュニティ値を無視します。

地理的リージョン内の任意の&1; つのピアリングの場所で ExpressRoute を介して Microsoft に接続する場合、地理的境界内のすべてのリージョンですべての Microsoft クラウド サービスにアクセスできます。 

たとえば、ExpressRoute を介してアムステルダムの Microsoft に接続している場合、北ヨーロッパと西ヨーロッパでホストされているすべての Microsoft クラウド サービスにアクセスできます。 

地理的リージョン、関連付けられている Azure リージョン、および対応する ExpressRoute のピアリングの場所の詳細な一覧については、「 [ExpressRoute パートナーとピアリングの場所](expressroute-locations.md) 」を参照してください。

地理的リージョンごとに複数の ExpressRoute 回線を購入できます。 複数の接続を持つことで、geo 冗長性による高可用性が確保される大きなメリットがあります。 複数の ExpressRoute 回線がある場合、パブリック ピアリングおよび Microsoft ピアリング パスで Microsoft からアドバタイズされたプレフィックスの同じセットを受け取ります。 これは、ネットワークから Microsoft へのパスが複数あることを意味します。 これは、ネットワーク内で十分に最適化されないルーティングの決定が行われる可能性があることを示します。 その結果、さまざまなサービスの接続エクスペリエンスが十分に最適化されない可能性があります。 ユーザーは、このコミュニティ値に依存して、[最適なルーティングをユーザーに](expressroute-optimize-routing.md)提供するための適切なルーティングの決定を行うことができます。

| **Microsoft Azure リージョン** | **BGP コミュニティ値** |
| --- | --- |
| **北米** | |
| 米国東部 |12076:51004 |
| 米国東部 2 |12076:51005 |
| 米国西部 |12076:51006 |
| 米国西部 2 |12076:51026 |
| 米国中西部 |12076:51027 |
| 米国中北部 |12076:51007 |
| 米国中南部 |12076:51008 |
| 米国中央部 |12076:51009 |
| カナダ中部 |12076:51020 |
| カナダ東部 |12076:51021 |
| **南アメリカ** | |
| ブラジル南部 |12076:51014 |
| **ヨーロッパ** | |
| 北ヨーロッパ |12076:51003 |
| 西ヨーロッパ |12076:51002 |
| 英国南部 | 12076:51024 |
| 英国西部 | 12076:51025 |
| **アジア太平洋** | |
| 東アジア |12076:51010 |
| 東南アジア |12076:51011 |
| **日本** | |
| 東日本 |12076:51012 |
| 西日本 |12076:51013 |
| **オーストラリア** | |
| オーストラリア東部 |12076:51015 |
| オーストラリア南東部 |12076:51016 |
| **インド** | |
| インド南部 |12076:51019 |
| インド西部 |12076:51018 |
| インド中部 |12076:51017 |
| **韓国** | |
| 韓国南部 |12076:51028 |
| 韓国中部 |12076:51029 |

Microsoft からアドバタイズされるすべてのルートには、適切なコミュニティ値がタグ付けされます。 

> [!IMPORTANT]
> ExpressRoute Premium アドオンが有効になっている場合にのみ、グローバル プレフィックスに適切なコミュニティ値がタグ付けされてアドバタイズされます。
> 
> 

さらに、Microsoft は、所属先のサービスに基づいてプレフィックスにもタグ付けします。 これは、Microsoft ピアリングにのみ該当します。 次の表に、サービスと BGP コミュニティ値のマッピングを示します。

| **サービス** | **BGP コミュニティ値** |
| --- | --- |
| Exchange Online |12076:5010 |
| SharePoint Online |12076:5020 |
| Skype for Business Online |12076:5030 |
| CRM Online |12076:5040 |
| その他の Office 365 Online サービス |12076:5100 |

> [!NOTE]
> Microsoft は、Microsoft にアドバタイズされるルートに設定されたすべての BGP コミュニティ値を無視します。
> 
> 

### <a name="bgp-community-support-in-national-clouds-preview"></a>National Clouds (プレビュー) の BGP コミュニティのサポート

| **National Clouds Azure リージョン**| **BGP コミュニティ値** |
| --- | --- |
| **米国政府** |  |
| 米国政府アリゾナ | 12076:51106 |
| 米国政府アイオワ州 | 12076:51109 |
| 米国政府バージニア州 | 12076:51105 |
| 米国政府テキサス | 12076:51108 |
| US DoD Central | 12076:51209 |
| US DoD East | 12076:51205 |


| **National Clouds のサービス** | **BGP コミュニティ値** |
| --- | --- |
| **米国政府** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype for Business Online |12076:5130 |
| CRM Online |12076:5140 |
| その他の Office 365 Online サービス |12076:5200 |

## <a name="next-steps"></a>次のステップ
* ExpressRoute 接続を構成します。
  
  * [クラシック デプロイ モデルで ExpressRoute 回線を作成](expressroute-howto-circuit-classic.md)するか、[Azure Resource Manager を使用して ExpressRoute 回線を作成、変更](expressroute-howto-circuit-arm.md)します。
  * [クラシック デプロイ モデルでルーティングを構成](expressroute-howto-routing-classic.md)するか、[Resource Manager デプロイ モデルでルーティングを構成](expressroute-howto-routing-arm.md)します。
  * [クラシック VNET を ExpressRoute 回線にリンク](expressroute-howto-linkvnet-classic.md)させるか、[Resource Manager VNET を ExpressRoute 回線にリンク](expressroute-howto-linkvnet-arm.md)させます。


