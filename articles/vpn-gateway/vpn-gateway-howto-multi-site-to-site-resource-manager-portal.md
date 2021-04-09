---
title: 複数の VPN Gateway サイト間接続を VNet に追加するAzure portal
description: マルチサイトのサイト間接続を、既存の接続がある VPN Gateway に追加する
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890150"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>サイト間接続を VNet に追加します。Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (クラシック)](vpn-gateway-multi-site.md)
>

この記事は、既存の接続がある VPN ゲートウェイにサイト間 (S2S) 接続を追加する場合に役立ちます。 このアーキテクチャは、一般に "マルチサイト" 構成と呼ばれます。 既にサイト間接続、ポイント対サイト接続、またはVNet 間接続のある VNet にサイト間接続を追加できます。 接続を追加する際には、制限があります。 構成を始める前に、この記事の「[前提条件](#before)」セクションで確認してください。

この記事は、RouteBased VPN ゲートウェイが存在する Resource Manager VNet に適用されます。 次の手順は、新しい ExpressRoute/サイト間の共存接続の構成には適用されません。 ただし、既存の共存構成に新しい VPN 接続を追加するだけの場合は、次の手順を使用できます。 共存接続の詳細については、「[クラシック デプロイ モデルにおいて共存する ExpressRoute 接続とサイト間接続を構成する](../expressroute/expressroute-howto-coexist-resource-manager.md)」を参照してください。

## <a name="prerequisites"></a><a name="before"></a>前提条件

次の項目についてご確認ください。

* ExpressRoute と VPN Gateway の共存構成を新しく構成しているのではない。
* 既存の接続のある Resource Manager デプロイ モデルを使用して作成した仮想ネットワークがある。
* VNet の仮想ネットワーク ゲートウェイがルートベースである。 VPN ゲートウェイがポリシーベースの場合は、仮想ネットワーク ゲートウェイを削除して、ルートベースとして新しい VPN ゲートウェイを作成する必要があります。
* この VNet が接続する VNet のアドレスの範囲のいずれも重複していないこと。
* 互換性のある VPN デバイスがあり、デバイスを構成できる人員がいる。 「 [VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。 VPN デバイスの構成に詳しくない場合や、オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。
* VPN デバイスの外部接続用パブリック IP アドレスがある。

## <a name="configure-a-connection"></a><a name="configure"></a>接続の構成

1. ブラウザーから [Azure Portal](https://portal.azure.com) に移動します。必要であれば Azure アカウントでサインインします。
1. **[すべてのリソース]** を選択して、リソースの一覧から **[仮想ネットワーク ゲートウェイ]** を見つけて、それを選択します。
1. **[仮想ネットワーク ゲートウェイ]** ページで、 **[接続]** を選択します。

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="VPN ゲートウェイ接続":::
1. **[接続]** ページで、 **[+ 追加]** を選択します。
1. **[接続の追加]** ページが表示されます。

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="[接続の追加] ページ":::
1. **[接続追加]** ページで、次のフィールドを入力します。

   * **[名前]:** 作成している接続先のサイトに付ける名前です。
   * **[接続の種類]** : **[サイト対サイト (IPsec)]** を選択します。

## <a name="add-a-local-network-gateway"></a><a name="local"></a>ローカル ネットワーク ゲートウェイの追加

1. **[ローカル ネットワーク ゲートウェイ]** フィールドで、**_[ローカル ネットワーク ゲートウェイを選択する]_ *_ を選択します。これで、_* [ローカル ネットワーク ゲートウェイの選択]** ページが開きます。
1. **[+ 新規作成]** を選択して **[ローカル ネットワーク ゲートウェイの作成]** ページを開きます。

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="[ローカル ネットワーク ゲートウェイの作成] ページ":::
1. **[ローカル ネットワーク ゲートウェイの作成]** ページで、次のフィールドを入力します。

   * **[名前]:** ローカル ネットワーク ゲートウェイ リソースに付ける名前です。
   * **エンドポイント:** 接続先のサイト上の VPN デバイスのパブリック IP アドレス、またはエンドポイントの FQDN です。
   * **[アドレス空間]:** 新しいローカル ネットワーク サイトにルーティングするアドレス空間です。
1. **[ローカル ネットワーク ゲートウェイの作成]** ページで **[OK]** を選択して変更を保存します。

## <a name="add-the-shared-key"></a><a name="part3"></a>共有キーの追加

1. ローカル ネットワーク ゲートウェイを作成したら、 **[接続 の追加]** ページに戻ります。
1. 残りのフィールドに入力します。 **[共有キー (PSK)]** で、VPN デバイスから共有キーを取得するか、ここで新しく作成して、同じ共有キーを使用するように VPN デバイスを構成します。 キーが完全に同一であることが重要です。

## <a name="create-the-connection"></a><a name="create"></a>接続を作成する

1. ページ下部の **[OK]** を選択すると、接続が作成されます。 接続の作成はすぐに開始されます。
1. 接続が完了したら、それを表示して確認できます。

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>VPN 接続の表示と確認

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>次のステップ

接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[仮想マシンのラーニング パス](/learn/paths/deploy-a-website-with-azure-virtual-machines/)に関するページを参照してください。
