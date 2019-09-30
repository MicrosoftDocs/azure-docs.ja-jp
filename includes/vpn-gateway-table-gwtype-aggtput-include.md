---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919703"
---
|**SKU**   | **S2S/VNet 間<br>トンネル** | **P2S<br> SSTP 接続** | **P2S<br> IKEv2/OpenVPN 接続** | **合計<br>スループット ベンチマーク** | **BGP** | **ゾーン冗長** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Basic** | 最大 10    | 最大 128  | サポートされていません  | 100 Mbps  | サポートされていません| いいえ |
|**VpnGw1**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | サポートされています | いいえ |
|**VpnGw2**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | サポートされています | いいえ |
|**VpnGw3**| 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | サポートされています | いいえ |
|**VpnGw1AZ**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | サポートされています | はい |
|**VpnGw2AZ**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | サポートされています | はい |
|**VpnGw3AZ**| 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | サポートされています | はい |

(*) 30 個を超える S2S VPN トンネルが必要な場合は、[Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) を使用してください。

* これらの接続の制限は別々になっています。 たとえば、VpnGw1 SKU では 128 の SSTP 接続が利用できると共に 250 の IKEv2 接続を利用できます。

* 料金情報については、[価格](https://azure.microsoft.com/pricing/details/vpn-gateway)に関するページをご覧ください。

* SLA (サービス レベル アグリーメント) 情報は [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) のページで確認できます。

* VpnGw1、VpnGw2、VpnGw3、VpnGw1AZ、VpnGw2AZ、および VpnGw3AZ は、Resource Manager デプロイ モデルを使用する VPN ゲートウェイでのみサポートされます。

* Basic SKU はレガシ SKU とみなされます。 Basic SKU には一定の機能制限があります。 Basic SKU を使用するゲートウェイのサイズを変更し、新しいゲートウェイ SKU のいずれかにすることはできません。その代わり、新しい SKU に変更する必要があります。 Basic SKU を使用する前に、必要としている機能がサポートされていることを確認してください。

* 合計スループット ベンチマークは、1 つのゲートウェイから集計された複数のトンネルの測定値に基づいています。 VPN ゲートウェイの合計スループット ベンチマークは、S2S と P2S を組み合わせたものです。 **多数のポイント対サイト接続がある場合、スループットの制限が原因でサイト間接続に悪影響が及ぶ可能性があります。** 合計スループット ベンチマークは、インターネット トラフィックの状況とアプリケーションの動作に依存するため、保証されたスループットではありません。

* VpnGw Sku の相対的なパフォーマンスをお客様が容易に把握できるように、一般公開されている iPerf ツールと CTSTraffic ツールを使用してパフォーマンスを測定しました。 次の表に、さまざまなアルゴリズムを使用したパフォーマンス テストの結果を示します。 ご覧のとおり、IPsec 暗号化と整合性の両方に GCMAES256 アルゴリズムを使用した場合に、最高のパフォーマンスが得られました。 IPsec 暗号化に AES256 を使用し、整合性に SHA256 を使用した場合は、平均的なパフォーマンスが得られました。 IPsec 暗号化に DES3 を使用し、整合性に SHA256 を使用した場合は、パフォーマンスが最も低くなりました。

|**SKU**   | **使用した<br>アルゴリズム** | **測定された<br>スループット** | **測定された<br> 1 秒あたりのパケット数** |
|---       | ---                 | ---            | ---                    |
|**VpnGw1**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**VpnGw2**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**VpnGw3**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 1.25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60,000|
|**VpnGw1AZ**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**VpnGw2AZ**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**VpnGw3AZ**| GCMAES256<br>AES256 と SHA256<br>DES3 と SHA256| 1.25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60,000|
