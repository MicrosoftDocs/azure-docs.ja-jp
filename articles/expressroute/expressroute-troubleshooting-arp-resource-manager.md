---
title: 'ARP テーブルの取得 - トラブルシューティング - ExpressRoute: Azure| Microsoft Docs'
description: このページでは、ExpressRoute 回線の ARP テーブルを取得する手順について説明します。
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 76e242adb07f4e6176bbdc6c03c75950e3732c2b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622078"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Resource Manager デプロイ モデルでの ARP テーブルの取得
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - クラシック](expressroute-troubleshooting-arp-classic.md)
> 
> 

この記事では、ExpressRoute 回線で使用されている ARP テーブルを取得する手順について説明します。

> [!IMPORTANT]
> 単純な問題の診断と解決を支援することが、このドキュメントの目的です。 Microsoft サポートに代わるものではありません。 以降のガイダンスで問題を解決できない場合は、 [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) にサポート チケットを申請してください。
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>アドレス解決プロトコル (ARP) と ARP テーブル
アドレス解決プロトコル (ARP) は、 [RFC 826](https://tools.ietf.org/html/rfc826)で定義されたレイヤー 2 のプロトコルです。 Ethernet アドレス (MAC アドレス) と IP アドレスとを対応付けるために、ARP が使用されます。

ARP テーブルから、特定のピアリングに関する ipv4 アドレスと MAC アドレスのマッピングを得ることができます。 ExpressRoute 回線のピアリングで使用される ARP テーブルは、プライマリ インターフェイスとセカンダリ インターフェイスのそれぞれに関して次の情報を提供します。

1. オンプレミス ルーター インターフェイスの IP アドレスから MAC アドレスへのマッピング
2. ExpressRoute ルーター インターフェイスの IP アドレスから MAC アドレスへのマッピング
3. マッピングがキャッシュされてからの経過時間

レイヤー 2 の構成を検証したり、レイヤー 2 の基本的な接続の問題をトラブルシューティングしたりする際に、ARP テーブルを役立てることができます。 

ARP テーブルの例: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


次のセクションでは、ExpressRoute のエッジ ルーターによって参照される ARP テーブルの表示方法について説明します。 

## <a name="prerequisites-for-learning-arp-tables"></a>ARP テーブルを取得するための前提条件
以降の作業を行うには、あらかじめ次の要件が満たされている必要があります。

* 少なくとも 1 つのピアリングが構成された有効な ExpressRoute 回線。 この回線は、接続プロバイダーによって確実に構成されている必要があります。 この回線に対し、貴社または貴社の接続プロバイダーが少なくとも 1 つのピアリング (Azure プライベート、Azure パブリック、Microsoft) を構成済みであることが必要となります。
* ピアリング (Azure プライベート、Azure パブリック、Microsoft) の構成に使用する IP アドレス範囲。 貴社側インターフェイスと ExpressRoute 側インターフェイスに対する IP アドレスのマッピングについては、 [ExpressRoute のルーティングの要件](expressroute-routing.md) に関するページに記載された IP アドレス割り当ての例をご覧ください。 ピアリングの構成については、 [ExpressRoute のピアリングの構成](expressroute-howto-routing-arm.md)に関するページを参照してください。
* 各 IP アドレスで使用するインターフェイスの MAC アドレスに関する情報 (ネットワーク チーム/接続プロバイダーから支給してもらう)。
* Azure 用の最新の PowerShell モジュール (バージョン 1.50 以降)。

> [!NOTE]
> レイヤー 3 がサービス プロバイダーから提供され、以下のポータル/出力において ARP テーブルが空の場合は、ポータルの更新ボタンを使用して回線の構成を更新します。 この操作で、回線に適切なルーティング構成が適用されます。 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>ExpressRoute 回線の ARP テーブルの取得
このセクションでは、各ピアリングの ARP テーブルを PowerShell で参照する手順について説明します。 対象となるピアリングについては、あらかじめ貴社または貴社の接続プロバイダーによって構成済みであることを前提としています。 それぞれの回線には 2 つのパス (プライマリとセカンダリ) があります。 それぞれのパスについて別々に ARP テーブルを確認することができます。

### <a name="arp-tables-for-azure-private-peering"></a>Azure プライベート ピアリングの ARP テーブル
Azure プライベート ピアリングの ARP テーブルは、次のコマンドレットで取得します。

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

以下に示したのは、該当する 1 本のパスについての出力例です。

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Azure パブリック ピアリングの ARP テーブル
Azure パブリック ピアリングの ARP テーブルは、次のコマンドレットで取得します。

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


以下に示したのは、該当する 1 本のパスについての出力例です。

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft ピアリングの ARP テーブル
Microsoft ピアリングの ARP テーブルは、次のコマンドレットで取得します。

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


以下に示したのは、該当する 1 本のパスについての出力例です。

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>この情報の用途
ピアリングの ARP テーブルは、レイヤー 2 の構成と接続性の検証に使用できます。 このセクションでは、各種の状況下における ARP テーブルの見え方について簡単に説明します。

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>回線が運用状態 (正常な状態) にあるときの ARP テーブル
* ARP テーブルには、オンプレミス側とマイクロソフト側とについて、有効な IP アドレスと MAC アドレスから成るエントリがそれぞれ存在します。 
* オンプレミス側 IP アドレスの最終オクテットは常に奇数です。
* マイクロソフト側 IP アドレスの最終オクテットは常に偶数です。
* 3 つのピアリング (プライマリ/セカンダリ) のいずれについても、マイクロソフト側では同じ MAC アドレスが表示されます。 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>オンプレミス側または接続プロバイダー側に問題がある場合の ARP テーブル
オンプレミスまたは接続プロバイダーに問題がある場合は、ARP テーブルにエントリが 1 つだけ表示されるか、オンプレミス MAC アドレスが Incomplete と表示されます。 この場合マイクロソフト側で使用されている MAC アドレスと IP アドレスとのマッピングが表示されます。 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

or
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> このような問題の解決については、ご利用の接続プロバイダーにサポート要求を申請してください。 ARP テーブルでインターフェイスの IP アドレスが MAC アドレスにマップされていない場合は、次の情報を確認してください。
> 
> 1. MSEE-PR と MSEE の間のリンクに割り当てられた /30 サブネットの最初の IP アドレスが MSEE-PR のインターフェイスで使用されている場合は、 Azure では、常に MSEE の 2 番目の IP アドレスが使用されます。
> 2. MSEE-PR と MSEE のペアで顧客 (S-Tag) とサービス (S-Tag) VLAN タグの両方が一致することを確認します。
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>マイクロソフト側に問題がある場合の ARP テーブル
* マイクロソフト側に問題がある場合は、ピアリングの ARP テーブルが表示されません。 
* [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)にサポート チケットを申請します。 レイヤー 2 の接続に問題がある旨をはっきりと伝えてください。 

## <a name="next-steps"></a>次の手順
* ExpressRoute 回線のレイヤー 3 の構成を検証する
  * ルートのサマリーを取得して BGP セッションの状態を調べる 
  * ExpressRoute でアドバタイズされるプレフィックスをルート テーブルを取得して調べる
* バイトの入出力を参照してデータ転送を検証する
* 問題が解消しない場合は [Microsoft サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) にサポート チケットを申請する

