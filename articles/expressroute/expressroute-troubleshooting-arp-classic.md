---
title: 'Azure ExpressRoute: ARP テーブル - トラブルシューティング: クラシック'
description: このページでは、ExpressRoute 回線の ARP テーブルを取得する手順について説明します (クラシック デプロイ モデル)。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/30/2017
ms.author: duau
ms.openlocfilehash: 7c83b6d25936e184c70d4bf0a51636a3986bf4b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89394841"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>クラシック デプロイ モデルでの ARP テーブルの取得
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - クラシック](expressroute-troubleshooting-arp-classic.md)
> 
> 

この記事では、Azure ExpressRoute 回線のアドレス解決プロトコル (ARP) テーブルを取得するための手順を説明します。

> [!IMPORTANT]
> 単純な問題の診断と解決を支援することが、このドキュメントの目的です。 Microsoft サポートに代わるものではありません。 次のガイダンスを使用して問題を解決できない場合は、 [Microsoft Azure のヘルプとサポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)にサポート要求を申請してください。
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>アドレス解決プロトコル (ARP) と ARP テーブル
ARP は、 [RFC 826](https://tools.ietf.org/html/rfc826)で定義されているレイヤー 2 のプロトコルです。 Ethernet アドレス (MAC アドレス) と IP アドレスとを対応付けるために、ARP が使用されます。

ARP テーブルから、特定のピアリングに関する IPv4 アドレスと MAC アドレスのマッピングを得ることができます。 ExpressRoute 回線のピアリングで使用される ARP テーブルは、プライマリ インターフェイスとセカンダリ インターフェイスのそれぞれに関して次の情報を提供します。

1. オンプレミス ルーター インターフェイスの IP アドレスから MAC アドレスへのマッピング
2. ExpressRoute ルーター インターフェイスの IP アドレスから MAC アドレスへのマッピング
3. マッピングがキャッシュされてからの経過時間

レイヤー 2 の構成を検証したり、レイヤー 2 の基本的な接続の問題をトラブルシューティングしたりする際に、ARP テーブルを役立てることができます。

次に、ARP テーブルの例を示します。

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


次のセクションでは、ExpressRoute のエッジ ルーターによって参照される ARP テーブルの表示方法について説明します。

## <a name="prerequisites-for-using-arp-tables"></a>ARP テーブルを使用するための前提条件
続行する前に、あらかじめ次の要件が満たされている必要があります。

* 少なくとも 1 つのピアリングが構成された有効な ExpressRoute 回線。 この回線は、接続プロバイダーによって確実に構成されている必要があります。 この回線に対し、貴社または貴社の接続プロバイダーが少なくとも 1 つのピアリング (Azure プライベート、Azure パブリック、Microsoft) を構成済みであることが必要となります。
* ピアリング (Azure プライベート、Azure パブリック、Microsoft) の構成に使用する IP アドレス範囲。 貴社側インターフェイスと ExpressRoute 側インターフェイスに対する IP アドレスのマッピングについては、「[ExpressRoute のルーティングの要件](expressroute-routing.md)」に記載された IP アドレス割り当ての例をご覧ください。 ピアリングの構成については、 [ExpressRoute のピアリングの構成](expressroute-howto-routing-classic.md)に関するページをご覧ください。
* 各 IP アドレスで使用するインターフェイスの MAC アドレスに関する情報 (ネットワーク チーム/接続プロバイダーから支給してもらう)。
* Azure 用の最新の Windows PowerShell モジュール (バージョン 1.50 以降)。

## <a name="arp-tables-for-your-expressroute-circuit"></a>ExpressRoute 回線の ARP テーブル
このセクションでは、PowerShell を使用してピアリングの種類ごとに ARP テーブルを表示する方法について説明します。 続行する前に、貴社または貴社の接続プロバイダーがピアリングを構成する必要があります。 それぞれの回線には 2 つのパス (プライマリとセカンダリ) があります。 それぞれのパスについて別々に ARP テーブルを確認することができます。

### <a name="arp-tables-for-azure-private-peering"></a>Azure プライベート ピアリングの ARP テーブル
Azure プライベート ピアリングの ARP テーブルは、次のコマンドレットで取得します。

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure private peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

# ARP table for Azure private peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary
```

該当する 1 本のパスについてのサンプル出力を次に示します。

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Azure パブリック ピアリングの ARP テーブル
Azure パブリック ピアリングの ARP テーブルは、次のコマンドレットで取得します。

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure public peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

# ARP table for Azure public peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary
```

該当する 1 本のパスについてのサンプル出力を次に示します。

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


該当する 1 本のパスについてのサンプル出力を次に示します。

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft ピアリングの ARP テーブル
Microsoft ピアリングの ARP テーブルは、次のコマンドレットで取得します。

```azurepowershell
# ARP table for Microsoft peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

# ARP table for Microsoft peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary
```


以下に示したのは、該当する 1 本のパスについての出力例です。

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>この情報の用途
ピアリングの ARP テーブルは、レイヤー 2 の構成と接続性の検証に使用できます。 このセクションでは、各種の状況下における ARP テーブルの見え方について簡単に説明します。

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>回線が運用 (正常な) 状態にあるときの ARP テーブル
* ARP テーブルには、オンプレミス側とマイクロソフト側とについて、有効な IP アドレスと MAC アドレスから成るエントリがそれぞれ存在します。
* オンプレミス側 IP アドレスの最後のオクテットは常に奇数です。
* マイクロソフト側 IP アドレスの最後のオクテットは常に偶数です。
* 3 つのピアリング (プライマリ/セカンダリ) のいずれについても、マイクロソフト側では同じ MAC アドレスが表示されます。

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>オンプレミス側または接続プロバイダー側に問題がある場合の ARP テーブル
 ARP テーブルに表示されるのは 1 件のエントリのみです。 この場合マイクロソフト側で使用されている MAC アドレスと IP アドレスとのマッピングが表示されます。

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
   0 Microsoft         65.0.0.2  aaaa.bbbb.cccc
```

> [!NOTE]
> このような問題が発生した場合は、問題の解決について、ご利用の接続プロバイダーにサポート要求を申請してください。
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>マイクロソフト側に問題がある場合の ARP テーブル
* マイクロソフト側に問題がある場合は、ピアリングの ARP テーブルが表示されません。
* [Microsoft Azure のヘルプとサポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)にサポート要求を申請してください。 レイヤー 2 の接続に問題がある旨をはっきりと伝えてください。

## <a name="next-steps"></a>次のステップ
* ExpressRoute 回線のレイヤー 3 の構成を検証します。
  * ルートのサマリーを取得して BGP セッションの状態を調べます。
  * ExpressRoute でアドバタイズされるプレフィックスをルート テーブルを取得して調べます。
* バイトの入出力を参照してデータ転送を検証します。
* 問題が解消しない場合は [Microsoft Azure のヘルプとサポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) にサポート要求を申請します。

