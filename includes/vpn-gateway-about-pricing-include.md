---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/08/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8f8d366961049deb3eda193718ccb553aac930e3
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666373"
---
料金は 2 つの要素に対して課金されます。1 つは仮想ネットワーク ゲートウェイに対する時間単位のコンピューティング コスト、もう 1 つは仮想ネットワーク ゲートウェイからのエグレス (送信) データ転送のコストです。 料金情報については、[価格](https://azure.microsoft.com/pricing/details/vpn-gateway)に関するページをご覧ください。 レガシ ゲートウェイ SKU の価格については、[ExpressRoute の価格に関するページ](https://azure.microsoft.com/pricing/details/expressroute)を参照し、「**Virtual Network ゲートウェイ**」のセクションまでスクロールしてください。

**仮想ネットワーク ゲートウェイのコンピューティング コスト**<br>仮想ネットワーク ゲートウェイごとに時間単位のコンピューティング コストが課金されます。 料金は、仮想ネットワーク ゲートウェイを作成する際に指定するゲートウェイ SKU に基づいて決定されます。 コストは、ゲートウェイ自体にかかる料金と、ゲートウェイを通過するデータ転送にかかる料金を足した額になります。 アクティブ/アクティブ セットアップのコストは、アクティブ/パッシブの場合と同じです。

**データ転送コスト**<br>データ転送のコストは、ソース仮想ネットワーク ゲートウェイからのエグレス トラフィックに基づいて計算されます。

* オンプレミスの VPN デバイスにトラフィックを送信する場合は、インターネット エグレス データ転送率を使用して課金されます。
* 異なるリージョンに属する仮想ネットワーク間でトラフィックを送信する場合は、リージョンごとの価格に基づいて課金されます。
* 同じリージョンに属する仮想ネットワーク間でトラフィックを送信する場合は、データ コストはかかりません。 同じリージョン内にある VNet 間のトラフィックは無料です。
