---
title: インクルード ファイル
description: インクルード ファイル
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0467359cd9d6a067e519a62532f00459bc5f68cb
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891003"
---
| Resource | 既定の制限 |
| --- | --- |
| データ スループット |30 Gbps<sup>1</sup> |
|ルール|10,000 個。すべての規則の種類が結合されます。|
|AzureFirewallSubnet の最小サイズ |/26|
|ネットワークおよびアプリケーション ルールのポート範囲|0-64,000。 現在、この制限を緩和するように取り組んでいます。|
|ルート テーブル|AzureFirewallSubnet には既定で、NextHopType の値が **Internet** に設定された 0.0.0.0/0 ルートがあります。<br><br>ExpressRoute または VPN Gateway 経由でのオンプレミスへの強制トンネリングを有効にしている場合は、NextHopType の値が Internet に設定された 0.0.0.0/0 ユーザー定義ルート (UDR) を明示的に構成し、それを AzureFirewallSubnet に関連付ける必要があります。 これにより、オンプレミス ネットワークに戻される可能性のある既定のゲートウェイ BGP アドバタイズがオーバーライドされます。 組織で、既定のゲートウェイ トラフィックをオンプレミス ネットワーク経由で戻すための Azure Firewall の強制トンネリングが必要である場合は、サポートにお問い合わせください。 Microsoft にて貴社のサブスクリプションをホワイトリストに登録し、必要なファイアウォールのインターネット接続を確保いたします。|

<sup>1</sup>これらの制限値を引き上げる必要がある場合は、Azure サポートにお問い合せください。
