---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96010770"
---
|**VPN<br>ゲートウェイ<br>世代** |**SKU**   | **S2S/VNet 間<br>トンネル** | **P2S<br> SSTP 接続** | **P2S<br> IKEv2/OpenVPN 接続** | **合計<br>スループット ベンチマーク** | **BGP** | **ゾーン冗長** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation1**|**Basic**   | 最大 10    | 最大 128  | サポートされていません  | 100 Mbps  | サポートされていません| いいえ |
|**Generation1**|**VpnGw1**  | 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | サポートされています | いいえ |
|**Generation1**|**VpnGw2**  | 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | サポートされています | いいえ |
|**Generation1**|**VpnGw3**  | 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | サポートされています | いいえ |
|**Generation1**|**VpnGw1AZ**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | サポートされています | はい |
|**Generation1**|**VpnGw2AZ**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | サポートされています | はい |
|**Generation1**|**VpnGw3AZ**| 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | サポートされています | はい |
|        |            |            |           |                |           |           |     |
|**Generation2**|**VpnGw2**  | 最大 30*   | 最大 128  | 最大 500       | 1.25 Gbps | サポートされています | いいえ |
|**Generation2**|**VpnGw3**  | 最大 30*   | 最大 128  | 最大 1000      | 2.5 Gbps  | サポート | いいえ |
|**Generation2**|**VpnGw4**  | 最大 30*   | 最大 128  | 最大 5000      | 5 Gbps    | サポート | いいえ |
|**Generation2**|**VpnGw5**  | 最大 30*   | 最大 128  | 最大 10000      | 10 Gbps   | サポート | いいえ |
|**Generation2**|**VpnGw2AZ**| 最大 30*   | 最大 128  | 最大 500       | 1.25 Gbps | サポートされています | はい |
|**Generation2**|**VpnGw3AZ**| 最大 30*   | 最大 128  | 最大 1000      | 2.5 Gbps  | サポートされています | はい |
|**Generation2**|**VpnGw4AZ**| 最大 30*   | 最大 128  | 最大 5000      | 5 Gbps    | サポートされています | はい |
|**Generation2**|**VpnGw5AZ**| 最大 30*   | 最大 128  | 最大 10000      | 10 Gbps   | サポートされています | はい |

(*) 30 個を超える S2S VPN トンネルが必要な場合は、[Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) を使用してください。

* VpnGw SKU のサイズ変更は、Basic SKU のサイズ変更を除き、同じ世代内で許可されます。 Basic SKU はレガシ SKU であり、機能に制限があります。 Basic から別の VpnGw SKU に移行するには、Basic SKU VPN ゲートウェイを削除し、必要な世代と SKU サイズの組み合わせを使用して新しいゲートウェイを作成する必要があります。

* これらの接続の制限は別々になっています。 たとえば、VpnGw1 SKU では 128 の SSTP 接続が利用できると共に 250 の IKEv2 接続を利用できます。

* 料金情報については、[価格](https://azure.microsoft.com/pricing/details/vpn-gateway)に関するページをご覧ください。

* SLA (サービス レベル アグリーメント) 情報は [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) のページで確認できます。

* 1 つのトンネルで最大 1 Gbps のスループットを実現できます。 上の表に示した合計スループット ベンチマークは、1 つのゲートウェイから集計された複数のトンネルの測定値に基づいています。 VPN ゲートウェイの合計スループット ベンチマークは、S2S と P2S を組み合わせたものです。 **多数のポイント対サイト接続がある場合、スループットの制限が原因でサイト間接続に悪影響が及ぶ可能性があります。** 合計スループット ベンチマークは、インターネット トラフィックの状況とアプリケーションの動作に依存するため、保証されたスループットではありません。

お客様が異なるアルゴリズムを使用して SKU の相対的なパフォーマンスを容易に把握できるように、一般公開されている iPerf ツールと CTSTraffic ツールを使用してパフォーマンスを測定しました。 次の表は、第 1 世代、VpnGw SKU のパフォーマンステストの結果を示しています。 ご覧のとおり、IPsec 暗号化と整合性の両方に GCMAES256 アルゴリズムを使用した場合に、最高のパフォーマンスが得られました。 IPsec 暗号化に AES256 を使用し、整合性に SHA256 を使用した場合は、平均的なパフォーマンスが得られました。 IPsec 暗号化に DES3 を使用し、整合性に SHA256 を使用した場合は、パフォーマンスが最も低くなりました。

|**世代**|**SKU**   | **使用した<br>アルゴリズム** | **測定された<br>スループット** | **測定された<br> 1 秒あたりのパケット数** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation1**|**VpnGw1**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**Generation1**|**VpnGw2**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**Generation1**|**VpnGw3**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 1.25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60,000|
|**Generation1**|**VpnGw1AZ**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**Generation1**|**VpnGw2AZ**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**Generation1**|**VpnGw3AZ**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 1.25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60,000|
