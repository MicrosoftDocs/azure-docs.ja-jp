---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6bc8b6cc18f4a4bc41cabf7f8fefe78a5aaf7827
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94427849"
---
1. [Azure portal](https://portal.azure.com) で、 **[Search resources, services, and docs]\(ソース、サービス、ドキュメントの検索\) (G+/)** に、「**仮想ネットワーク ゲートウェイ**」と入力します。 検索結果で **仮想ネットワーク ゲートウェイ** を見つけて、それを選択します。

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/search.png" alt-text="検索フィールド" lightbox="./media/vpn-gateway-add-gw-rm-portal-include/search-expand.png":::

1. **[仮想ネットワーク ゲートウェイ]** ページで、 **[+ 追加]** を選択します。 **[仮想ネットワーク ゲートウェイの作成]** ページが開きます。

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/add.png" alt-text="[仮想ネットワーク ゲートウェイ] ページ":::
1. **[基本]** タブで、仮想ネットワーク ゲートウェイの値を入力します。

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway.png" alt-text="ゲートウェイ フィールド":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway-vnet.png" alt-text="追加のゲートウェイ フィールド":::

   * **サブスクリプション**:使用するサブスクリプションをドロップダウンから選択します。
   * **リソース グループ**:この設定は、このページで仮想ネットワークを選択すると自動入力されます。

   **インスタンスの詳細**

   * **Name**:ゲートウェイに名前を付けます。 ゲートウェイの名前付けは、ゲートウェイ サブネットの名前付けと同じではありません。 作成するゲートウェイ オブジェクトの名前です。
   * **[リージョン]** :このリソースを作成するリージョンを選択します。 ゲートウェイのリージョンは、仮想ネットワークと同じである必要があります。
   * **[ゲートウェイの種類]** : **[VPN]** を選択します。 VPN Gateway では、仮想ネットワーク ゲートウェイの種類として **VPN** を使用します。
   * **VPN の種類**:構成に指定されている VPN の種類を選択します。 ほとんどの構成で [VPN の種類] は [ルート ベース] にする必要があります。
   * **SKU**:ゲートウェイの SKU をドロップダウンから選択します。 ドロップダウン リストに表示される SKU は、選択した VPN の種類によって異なります。 ゲートウェイの SKU の詳細については、「[ゲートウェイの SKU](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。
   * **世代**: VPN ゲートウェイの世代については、「[ゲートウェイの SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)」を参照してください。
   * **仮想ネットワーク**:ドロップダウンから、このゲートウェイの追加先の仮想ネットワークを選択します。
   * **[ゲートウェイ サブネットのアドレス範囲]** : このフィールドは、VNet にゲートウェイ サブネットがない場合にのみ表示されます。 可能であれば、範囲を /27 以上 (/26、/25 など) に指定してください。 /28 より小さい範囲を作成することはお勧めしません。 既にゲートウェイ サブネットがある場合は、仮想ネットワークから GatewaySubnet の詳細を表示できます。 範囲を表示するには、 **[サブネット]** をクリックします。 範囲を変更する場合は、GatewaySubnet を削除して再作成できます。

   **パブリック IP アドレス**

   この設定は、VPN ゲートウェイに関連付けられるパブリック IP アドレス オブジェクトを指定します。 パブリック IP アドレスは、VPN ゲートウェイの作成時に、このオブジェクトに対して動的に割り当てられます。 パブリック IP アドレスが変わるのは、ゲートウェイが削除され、再度作成されたときのみです。 VPN ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードでは、IP アドレスは変わりません。

     * **[パブリック IP アドレス]** : **[新規作成]** を選択しておいてください。
     * **パブリック IP アドレス名**:このテキスト ボックスに、パブリック IP アドレス インスタンスの名前を入力します。
     * **割り当て**: VPN ゲートウェイでは動的のみがサポートされます。
     * **[アクティブ/アクティブ モードの有効化]** : **[アクティブ/アクティブ モードの有効化]** を選択するのは、アクティブ/アクティブ ゲートウェイ構成を作成する場合だけです。 それ以外の場合は、この設定を **[Disabled]\(無効\)** のままにします。
     * **[Configure BGP]\(BGP の構成\)** の設定は、実際の構成で特に必要でない限り、 **[無効]** のままにしておいてください。 この設定が必要である場合、既定の ASN は 65515 です。ただし、これは変わる場合があります。
1. **[確認と作成]** を選択して検証を実行します。
1. 検証に合格したら、 **[作成]** を選択して VPN ゲートウェイをデプロイします。

ゲートウェイを完全に作成してデプロイするには最大で 45 分かかることがあります。 デプロイの状態は、ゲートウェイの [概要] ページで確認できます。 ゲートウェイの作成後は、ポータルの仮想ネットワークを調べることで、ゲートウェイに割り当てられている IP アドレスを確認できます。 ゲートウェイは、接続されたデバイスとして表示されます。
