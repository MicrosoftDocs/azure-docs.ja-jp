---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3507aacc68de25f7368cbe3cda917077564c56eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356246"
---
1. **[リソース グループ]** に移動し、**ピアリング** リソースの作成時に選択したリソース グループを選択します。 リソース グループが多すぎる場合は、**フィルター** ボックスを使用します。

    > [!div class="mx-imgBorder"]
    > ![リソース グループ](../media/setup-direct-get-resourcegroup.png)

1. 作成した **ピアリング** リソースを選択します。

    > [!div class="mx-imgBorder"]
    > ![左側のペインで、[概要] ページが選択されています。 PeeringResourceGroup に関する情報が表示されています。 ピアリングの一覧で、AshburnPeering が強調表示されています。](../media/setup-direct-get-open.png)

1. **[概要]** ページには、次に示すように概要情報が表示されます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソースの [概要] ウィンドウ](../media/setup-direct-get-overview.png)

1. 左側で、 **[ASN information]\(ASN 情報\)** を選択して、PeerAsn を作成したときに送信された情報を表示します。

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソースの [ASN information]\(ASN 情報\)](../media/setup-direct-get-asninfo.png)

1. 左側で、 **[接続]** を選択します。 画面上部で、都市内のさまざまな施設にわたる、ASN と Microsoft の間のピアリング接続の概要を確認します。 また、次のようにウィンドウの中央にある **[接続]** を選択することで、 **[概要]** ページから接続の概要にアクセスすることもできます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング リソースの [接続]](../media/setup-direct-get-connectionssummary.png)

    * **[接続状態]** は、ピアリング接続の設定の状態に対応しています。 このフィールドに表示される状態は、「[Direct ピアリングのチュートリアル](../walkthrough-direct-all.md)」に示されている状態図に従います。
    * **IPv4 セッション状態** と **IPv6 セッション状態** は、それぞれ IPv4 と IPv6 BGP のセッション状態に対応しています。 
    * 画面上部の行を選択すると、下部の **[接続]** セクションに各接続の詳細が表示されます。 矢印を選択して、 **[構成]** 、 **[IPv4 アドレス]** 、 **[IPv6 アドレス]** を展開します。
