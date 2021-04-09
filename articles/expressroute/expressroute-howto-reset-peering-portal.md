---
title: 'Azure ExpressRoute: Azure portal を使用して回線のピアリングをリセットする'
description: Azure portal を使用して Azure ExpressRoute 回線のピアリングを無効および有効にする方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680273"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Azure portal を使用して ExpressRoute 回線のピアリングをリセットする

この記事では、Azure portal を使用して Azure ExpressRoute 回線のピアリングを無効および有効にする方法について説明します。 ピアリングを無効にすると、ExpressRoute 回線のプライマリとセカンダリの接続の両方のボーダー ゲートウェイ プロトコル (BGP) セッションがシャットダウンされます。 ピアリングを有効にすると、ExpressRoute 回線のプライマリとセカンダリの接続の両方の BGP セッションが復元されます。

> [!Note]
> ExpressRoute 回線上のピアリングを初めて構成すると、そのピアリングは既定で有効になります。

ExpressRoute ピアリングのリセットは、次のシナリオで役立つことがあります。

* ディザスター リカバリーの設計と実装をテストしている。 たとえば、2 つの ExpressRoute 回線があるとします。 片方の回線のピアリングを無効にして、ネットワーク トラフィックで他方の回線を強制的に使用するように設定できます。

* Azure プライベート ピアリングまたは Microsoft ピアリングで、双方向フォワーディング検出 (BFD) を有効にしたい。 ExpressRoute 回線が、2018 年 8 月 1 日より前に Azure プライベート ピアリングで作成された場合、または 2020 年 1 月 10 日より前に Microsoft ピアリングで作成された場合、BFD は、既定では有効になりませんでした。 BFD を有効にするには、ピアリングをリセットします。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

ブラウザーから [Azure portal](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

## <a name="reset-a-peering"></a>ピアリングをリセットする

ExpressRoute 回線上の Microsoft ピアリングと Azure プライベート ピアリングは、単独でリセットできます。

1. 変更する回線を選択します。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute 回線の一覧からの回線の選択を示すスクリーンショット。":::

1. リセットするピアリング構成を選択します。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="ExpressRoute 回線の概要でのピアリングの選択を示すスクリーンショット。":::

1. **[ピアリングを有効にする]** チェック ボックスをオフにし、 **[保存]** を選択してピアリング構成を無効にします。

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="[ピアリングを有効にする] チェック ボックスがオフになっていることを示すスクリーンショット。":::

1. **[ピアリングを有効にする]** チェック ボックスをオンにし、 **[保存]** を選択してピアリング構成を有効にします。

## <a name="next-steps"></a>次のステップ

ExpressRoute に関する問題のトラブルシューティングを行うには、次の記事を参照してください。

* [ExpressRoute 接続の確認](expressroute-troubleshooting-expressroute-overview.md)
* [ネットワーク パフォーマンスのトラブルシューティング](expressroute-troubleshooting-network-performance.md)
