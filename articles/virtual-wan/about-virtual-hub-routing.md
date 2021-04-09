---
title: 仮想ハブのルーティングについて
titleSuffix: Azure Virtual WAN
description: この記事では、仮想ハブのルーティングについて説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: c1a2a54bf2d4c5de3e6cfca66256f60592fc1f3e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737513"
---
# <a name="about-virtual-hub-routing"></a>仮想ハブのルーティングについて

仮想ハブのルーティング機能は、Border Gateway Protocol (BGP) を使用してゲートウェイ間のすべてのルーティングを管理するルーターによって提供されます。 仮想ハブには、サイト間 VPN ゲートウェイ、ExpressRoute ゲートウェイ、ポイント対サイト ゲートウェイ、Azure Firewall などの複数のゲートウェイを含めることができます。 また、このルーターは、仮想ハブに接続する仮想ネットワーク間の転送接続を提供し、最大 50 Gbps の合計スループットをサポートできます。 これらのルーティング機能は、Standard Virtual WAN のお客様に適用されます。

ルーティングを構成する方法については、「[仮想ハブのルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。

## <a name="routing-concepts"></a><a name="concepts"></a>ルーティングの概念

次のセクションでは、仮想ハブのルーティングでの主要な概念について説明します。

### <a name="hub-route-table"></a><a name="hub-route"></a>ハブ ルート テーブル

仮想ハブ ルート テーブルには、1 つ以上のルートを含めることができます。 ルートには、その名前、ラベル、宛先の種類、宛先プレフィックスの一覧、およびルーティングされるパケットの次ホップ情報が含まれます。 通常、**接続** は、ルート テーブルに関連付けられたり伝達したりするルーティング構成を持ちます。

### <a name="connections"></a><a name="connection"></a>接続

接続は、ルーティング構成を持つ Resource Manager のリソースです。 4 種類の接続は次のとおりです。

* **VPN 接続**:VPN サイトを仮想ハブ VPN ゲートウェイに接続します。
* **ExpressRoute 接続**: ExpressRoute 回線を仮想ハブの ExpressRoute ゲートウェイに接続します。
* **P2S 構成接続**: ユーザー VPN (ポイント対サイト) 構成を仮想ハブのユーザー VPN (ポイント対サイト) ゲートウェイに接続します。
* **ハブ仮想ネットワーク接続**: 仮想ネットワークを仮想ハブに接続します。

仮想ネットワーク接続のルーティング構成は、セットアップ中に設定できます。 既定では、すべての接続は既定のルート テーブルに関連付けられ、伝達します。

### <a name="association"></a><a name="association"></a>関連付け

各接続は、1 つのルート テーブルに関連付けられています。 ルート テーブルに接続を関連付けることにより、ルート テーブルにルートとして示されている宛先にトラフィックを送信できます。 接続のルーティング構成には、関連付けられているルート テーブルが表示されます。  複数の接続を同じルート テーブルに関連付けることができます。 すべての VPN、ExpressRoute、およびユーザー VPN 接続は、同じ (既定の) ルート テーブルに関連付けられます。

既定では、すべての接続は、仮想ハブ内の **既定のルート テーブル** に関連付けられます。 各仮想ハブには独自の既定のルート テーブルがあり、これを編集して静的ルートを追加することができます。 静的に追加されたルートは、同じプレフィックスに対する動的に学習されたルートより優先されます。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="関連付け":::

### <a name="propagation"></a><a name="propagation"></a>伝達

接続は、ルート テーブルにルートを動的に伝達します。 VPN 接続、ExpressRoute 接続、または P2S 構成接続では、ルートは、BGP を使用して仮想ハブからオンプレミス ルーターに伝達されます。 ルートは、1 つまたは複数のルート テーブルに伝達できます。

各仮想ハブでは、**None ルート テーブル** も使用できます。 None ルート テーブルに伝達するということは、接続から伝達する必要があるルートがないことを意味します。 VPN、ExpressRoute、およびユーザー VPN 接続は、ルートを同じルート テーブルのセットに伝達します。

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="伝達":::

### <a name="labels"></a><a name="labels"></a>ラベル

ラベルは、ルート テーブルを論理的にグループ化するためのメカニズムを提供します。 接続から複数のルート テーブルにルートを伝達するときに特に役立ちます。 たとえば、**既定のルート テーブル** には、'Default' という組み込みのラベルがあります。 ユーザーが接続ルートを 'Default' ラベルに伝達すると、Virtual WAN 内のすべてのハブのすべての既定のルート テーブルに自動的に反映されます。

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>仮想ネットワーク接続での静的ルートの構成

静的ルートを構成すると、仮想ハブに接続されたスポーク VNet にプロビジョニングされているネットワーク仮想アプライアンス (NVA) のものである可能性がある次ホップ IP 経由でトラフィックを誘導するメカニズムが提供されます。 静的ルートは、ルート名、宛先プレフィックスの一覧、および次ホップ IP で構成されます。

## <a name="route-tables-for-pre-existing-routes"></a><a name="route"></a>既存のルートのルート テーブル

ルート テーブルに、関連付けと伝達の機能が備わりました。 既存のルート テーブルは、これらの機能を持たないルート テーブルです。 ハブ ルーティングに既存のルートがあり、新しい機能を使用する場合は、以下を考慮してください。

* **仮想ハブに既存のルートがある Standard Virtual WAN のお客様**:

   Azure portal のハブの [ルーティング] セクションにルートが既にある場合、まずそれらを削除してから、(Azure portal のハブの [ルート テーブル] セクションにある) 新しいルート テーブルを作成する必要があります。

* **仮想ハブに既存のルートがある Basic Virtual WAN のお客様**:

   Azure portal のハブの [ルーティング] セクションにルートが既にある場合、まずそれらを削除してから、お使いの Basic Virtual WAN を Standard Virtual WAN に **アップグレード** します。 「[Virtual WAN を Basic から Standard にアップグレードする](upgrade-virtual-wan.md)」を参照してください。

## <a name="hub-reset"></a><a name="reset"></a>ハブのリセット

仮想ハブの **リセット** は、Azure portal でのみ使用できます。 リセットを使うと、ルート テーブル、ハブ ルーター、または仮想ハブ リソース自体などの失敗したリソースを、適切なプロビジョニング状態に戻すことができます。 Microsoft にサポートを問い合わせる前に、ハブをリセットすることを検討してください。 この操作では、仮想ハブ内のいずれのゲートウェイもリセットされません。

## <a name="additional-considerations"></a><a name="considerations"></a>その他の注意点

Virtual WAN ルーティングを構成するときは、次の点を考慮してください。

* すべてのブランチ接続 (ポイント対サイト、サイト間、および ExpressRoute) が既定のルート テーブルに関連付けられている必要があります。 これにより、すべてのブランチが同じプレフィックスを学習します。
* すべてのブランチ接続が、同じルート テーブルのセットにルートを伝達する必要があります。 たとえば、ブランチが既定のルート テーブルに伝達するように決定した場合、この構成はすべてのブランチで一貫している必要があります。 その結果、既定のルート テーブルに関連付けられているすべての接続が、すべてのブランチに到達できるようになります。
* Azure Firewall を経由したブランチ間接続は現在サポートされていません。
* 複数のリージョンで Azure Firewall を使用する場合は、すべてのスポーク仮想ネットワークが同じルート テーブルに関連付けられている必要があります。 たとえば、VNet のサブセットが Azure Firewall を通過し、他の VNet が同じ仮想ハブ内の Azure Firewall をバイパスすることはできません。
* VNet 接続ごとに次ホップ IP を 1 つ構成できます。

## <a name="next-steps"></a>次のステップ

* ルーティングを構成する方法については、「[仮想ハブのルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。
* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
