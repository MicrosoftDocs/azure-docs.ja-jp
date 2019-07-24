---
title: 'ルーティングを最適化する - ExpressRoute 回線: Azure | Microsoft Docs'
description: このページでは、Microsoft とユーザー (企業) のネットワークとを接続する ExpressRoute 回線がユーザー側に複数存在する場合のルーティングを最適化する方法について詳しく説明します。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 65c23b05cfcb623f8e2870df813f5516b3039d5c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140933"
---
# <a name="optimize-expressroute-routing"></a>ExpressRoute ルーティングの最適化
ExpressRoute 回線が複数あるとき、Microsoft への接続経路は複数存在します。 その結果、期待したルーティングが行われない、つまりトラフィックが貴社のネットワークから Microsoft に到達するまでの経路と、Microsoft から貴社のネットワークに到達するまでの経路が、想定よりも長くなってしまう可能性があります。 ネットワーク パスが長くなるほど、遅延は大きくなります。 遅延は、アプリケーションのパフォーマンスとユーザー エクスペリエンスに直接影響します。 この記事では、該当する問題について例示すると共に、標準のルーティング技術を使ってルーティングを最適化する方法を説明します。

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>顧客から Microsoft への準最適なルーティング
では、具体的な例を用いてルーティングの問題を詳しく見ていきましょう。 米国のロサンゼルスとニューヨークにそれぞれ 1 つオフィスがあるとします。 2 つのオフィスは、ワイド エリア ネットワーク (WAN) に接続されています。WAN は、自社のバックボーン ネットワークでも、サービス プロバイダーの IP VPN でもかまいません。 また ExpressRoute 回線が 2 つ存在します。1 つは米国西部に、もう 1 つは米国東部にあり、それらも WAN に接続されています。 オフィスから Microsoft のネットワークには、明らかに 2 とおりの接続経路があります。 このとき米国西部と米国東部の両オフィスで Azure (Azure App Service など) をデプロイしているとします。 ユーザーの接続先として意図した Azure リージョンは当然、ロサンゼルスなら米国西部、ニューヨークなら米国東部です。それぞれのオフィスのユーザーが最適なパフォーマンスを享受できるよう最寄りの Azure サービスがアクセス先となるようにサービス管理者によってアドバタイズされています。 ところが、意図した結果が得られるのは米国東部のオフィスだけで、米国西部のオフィスでは思いどおりの結果が得られません。 問題の原因は、 Azure 米国東部 (23.100.0.0/16) のプレフィックスと Azure 米国西部 (13.100.0.0/16) のプレフィックスの両方が、それぞれの ExpressRoute 回線でアドバタイズされていることにあります。 どのプレフィックスがどのリージョンに属しているかがわからなければ、両者を区別して扱うことはできません。 どちらのプレフィックスも米国西部より米国東部の方が近いと WAN ネットワークで判断される可能性があり、その場合、両オフィスのユーザーが米国東部の ExpressRoute 回線にルーティングされます。 最終的にロサンゼルス オフィスでは、満足なパフォーマンスを享受できないユーザーが続出する結果となります。

![ExpressRoute ケース 1 の問題 - 顧客から Microsoft への準最適なルーティング](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>解決策: BGP コミュニティの使用
両オフィスのユーザーに使用されるルーティングを最適化するには、Azure の米国西部リージョンに属しているプレフィックスと米国東部リージョンに属しているプレフィックスとを区別できなければなりません。 この情報をコード化するためには、[BGP コミュニティ値](expressroute-routing.md)を使用します。 そこで各 Azure リージョンに一意の BGP コミュニティ値を割り当てました (例: 米国東部は "12076:51004"、米国西部は "12076:51006")。 これでどのプレフィックスがどの Azure リージョンに属しているかという情報が得られるので、優先する ExpressRoute 回線を構成することができます。 ルーティング情報の交換に BGP を使用するのであれば、BGP の Local Preference を使用してルーティングを制御することが可能です。 この例では、13.100.0.0/16 に割り当てる Local Preference 値を米国西部では米国東部よりも大きくすることが考えられます。同様に、23.100.0.0/16 に割り当てる Local Preference 値は、米国西部より米国東部の方が大きくなるようにします。 このように構成することで、Microsoft への経路が 2 つあっても、ロサンゼルスのユーザーは米国西部の ExpressRoute 回線を使用して米国西部リージョンに接続し、ニューヨークのユーザーは米国東部の ExpressRoute を使用して米国東部リージョンに接続することができます。 両側でルーティングが最適化されます。 

![ExpressRoute ケース 1 の解決策 - BGP コミュニティの使用](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> Local Preference を使用した同じ手法は、貴社側から Azure Virtual Network へのルーティングにも適用することができます。 Azure から貴社のネットワークにアドバタイズされたプレフィックスに対し、Microsoft は BGP コミュニティ値をタグ付けしません。 しかしどの Virtual Network デプロイが、どの支社に近いかは貴社が把握しているので、一方の ExpressRoute 回線をもう一方よりも優先するようにルーターを構成することができます。
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Microsoft から顧客への準最適なルーティング
もう 1 つ例を紹介します。Microsoft から貴社ネットワークへの接続経路が長くなってしまうケースです。 このケースでは、オンプレミスの Exchange サーバーと Exchange Online を[ハイブリッド環境](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx)で使用しています。 オフィスはいずれも WAN に接続されています。 マイクロソフトには、両オフィスのオンプレミス サーバーのプレフィックスを 2 つの ExpressRoute 回線を介してアドバタイズします。 Exchange Online はメールボックスを移行する場合などに、オンプレミス サーバーへの接続を開始します。 ここで問題が発生します。ロサンゼルス オフィスへの接続が、わざわざ米国東部の ExpressRoute 回線にルーティングされてから、大陸を横断して米国西部のオフィスに戻ってくるという事態が発生しています。 問題の原因は 1 つ目の例と同様です。 手掛かりがなければ、Microsoft のネットワークが、米国東部に近い利用者のプレフィックスと、米国西部に近い利用者のプレフィックスを判別できないのです。 ロサンゼルスのオフィスへの経路が正しく選択されないという事態が起こります。

![ExpressRoute ケース 2 - Microsoft から顧客への準最適なルーティング](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>解決策: AS PATH プリペンドの使用
この問題には解決策が 2 つあります。 1 つ目は単純です。米国西部の ExpressRoute 回線にはロサンゼルス オフィスのオンプレミス プレフィックス (177.2.0.0/31) を、米国東部の ExpressRoute 回線にはニューヨーク オフィスのオンプレミス プレフィックス (177.2.0.2/31) をアドバタイズします。 結果的に、Microsoft からそれぞれのオフィスに接続するための経路は 1 つしか存在しないことになります。 あいまいさが排除され、ルーティングが最適化されます。 この設計を採用した場合、フェールオーバーの方針を考慮する必要があります。 ExpressRoute を介した Microsoft への経路に障害が発生しても、Exchange Online が貴社のオンプレミス サーバーに引き続き接続できるように対策を講じなければなりません。 

もう 1 つの解決策は、引き続き両方の ExpressRoute 回線で 2 つのプレフィックスをアドバタイズしたうえで、どのプレフィックスがどちらのオフィスに近いか、という手掛かりを Microsoft に知らせる方法です。 BGP の AS Path プリペンドがサポートされているため、プレフィックスの AS Path を構成することでルーティングを制御することができます。 この例では、172.2.0.0/31 の AS PATH を、米国東部では意図的に長くすることが考えられます。そうすることで、このプレフィックスに向かうトラフィックでは、米国西部の ExpressRoute 回線が優先されます (このプレフィックスに対する経路は米国西部の方が短いと Microsoft のネットワークが判断します)。 米国西部でも同様に、172.2.0.2/31 の AS PATH を意図的に長くし、米国東部の ExpressRoute 回線が優先されるようにします。 これで両方のオフィスのルーティングが最適化されます。 このように設計すれば、いずれかの ExpressRoute 回線で障害が発生しても、Exchange Online は、もう 1 つの ExpressRoute 回線および WAN を介して引き続き貴社オフィスに到達することができます。 

> [!IMPORTANT]
> Microsoft ピア設定では、受信したプレフィックスの AS PATH からプライベート AS 番号が削除されます。 Microsoft ピア設定のルーティングを制御するには、AS PATH にパブリック AS 番号を付加する必要があります。
> 
> 

![ExpressRoute ケース 2 の解決策 - AS PATH プリペンドの使用](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> この記事の例は Microsoft ピアリングとパブリック ピアリングのものですが、プライベート ピアリングでも同じ機能がサポートされます。 また、AS PATH プリペンドは単一の ExpressRoute 回線内で機能し、プライマリ パスとセカンダリ パスの選択に影響します。
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>仮想ネットワーク間の準最適なルーティング
ExpressRoute では、2 つの Virtual Network ("VNet") を ExpressRoute 回線で結ぶことで、その両者間に通信を確立することができます。 それらの VNet を複数の ExpressRoute 回線にリンクさせると、その VNet 間のルーティングが最適に行われないケースが生じます。 具体的な例を考えてみましょう。 ExpressRoute 回線が 2 つ存在し、1 つは米国西部に、もう 1 つは米国東部にあるとします。 VNet は、それぞれのリージョンに 2 つずつ存在します。 一方の VNet には Web サーバーが、もう一方の VNet にはアプリケーション サーバーがデプロイされています。 それぞれのリージョンにある 2 つの VNet は、冗長性を確保するために、ローカルの ExpressRoute 回線とリモートの ExpressRoute 回線の両方に接続されています。 次の図を見ると、それぞれの VNet からもう一方の VNet には 2 つの経路が存在することがわかります。 VNet からは、ローカルの ExpressRoute 回線とリモートの ExpressRoute 回線を区別できません。 ECMP (Equal-Cost-Multi-Path ) ルーティングによって VNet 間のトラフィックを負荷分散する際に、一部のトラフィック フローで長い方の経路が選択され、リモートの ExpressRoute 回線を経由してルーティングされる結果となります。

![ExpressRoute ケース 3 - 仮想ネットワーク間の準最適なルーティング](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>解決策: ローカル接続の重みを大きくする
これを解決するのは簡単です。 VNet と回線の位置はわかっているので、それぞれの VNet で優先すべき経路を指定すればよいのです。 具体的にこの例で言えば、ローカル接続に割り当てる重みをリモート接続よりも大きくします (構成例については、[こちら](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection)を参照)。 VNet は、複数の接続上でもう一方の VNet のプレフィックスを受信すると、重みが最も大きい接続を優先的に選んで、そのプレフィックス宛てのトラフィックを送信します。

![ExpressRoute ケース 3 の解決策 - ローカル接続の重みを大きくする](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> 複数の ExpressRoute 回線が存在する場合、AS PATH プリペンド (2 番目のシナリオで説明した手法) を適用する代わりに接続の重みを構成することで、VNet からオンプレミス ネットワークへのルーティングを制御することもできます。 トラフィックの送信方法が決定される際、AS Path の長さの前に、それぞれのプレフィックスについて必ず接続の重みが調べられます。
>
>
