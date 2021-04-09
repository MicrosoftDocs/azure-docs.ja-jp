---
title: 'チュートリアル - VPN ゲートウェイを作成して管理する: Azure portal'
description: このチュートリアルでは、ポータルを使用して Azure VPN Gateway を作成、デプロイ、管理する方法について説明します。
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 820482a268af038737557c517ccda086cd65d943
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880561"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>チュートリアル:Azure portal を使用して VPN ゲートウェイを作成、管理する

Azure VPN ゲートウェイは、お客様のオンプレミスと Azure 間のクロスプレミス接続を提供します。 このチュートリアルでは、VPN ゲートウェイの作成と管理など、Azure VPN ゲートウェイのデプロイの基本事項について説明します。 [Azure CLI](create-routebased-vpn-gateway-cli.md) または [Azure PowerShell](create-routebased-vpn-gateway-powershell.md) を使用して、ゲートウェイを作成することもできます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * VPN ゲートウェイの作成
> * ゲートウェイのパブリック IP アドレスを表示する
> * VPN ゲートウェイのサイズ変更 (SKU のサイズ変更)
> * VPN ゲートウェイのリセット

次の図は、このチュートリアルの一環として作成する仮想ネットワークと VPN ゲートウェイを示しています。

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="VNet と VPN ゲートウェイの図":::

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションが含まれる Azure アカウント。 ない場合は、[無料で作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)してください。

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>仮想ネットワークの作成

次の値を使用して VNet を作成します。

* **[リソース グループ]:** TestRG1
* **[名前]:** VNet1
* **[リージョン]:** (米国) 米国東部
* **IPv4 アドレス空間:** 10.1.0.0/16
* **サブネット名:** FrontEnd
* **サブネット アドレス空間:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>VPN ゲートウェイの作成

この手順では、VNet の仮想ネットワーク ゲートウェイを作成します。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

次の値を使用して仮想ネットワーク ゲートウェイを作成します。

* **[名前]:** VNet1GW
* **[リージョン]:** 米国東部
* **ゲートウェイの種類:** VPN
* **VPN の種類:** ルート ベース
* **SKU:** VpnGw1
* **世代:** Generation1
* **仮想ネットワーク:** VNet1
* **ゲートウェイ サブネットのアドレス範囲:** 10.1.255.0/27
* **[パブリック IP アドレス]** : 新規作成
* **パブリック IP アドレス名:** VNet1GWpip
* **アクティブ/アクティブ モードの有効化:** 無効
* **[Configure BGP]\(BGP の構成\):** 無効

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-public-ip-address"></a><a name="view"></a>パブリック IP アドレスの表示

ゲートウェイのパブリック IP アドレスは、ご利用のゲートウェイの **[概要]** ページで確認できます。

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="[概要] ページ":::

パブリック IP アドレス オブジェクトに関する詳しい情報を表示するには、 **[パブリック IP アドレス]** の横にある名前 (または IP アドレス) リンクをクリックします。

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>ゲートウェイの SKU のサイズ変更

ゲートウェイの SKU の変更とサイズ変更には、一定のルールがあります。 このセクションでは、SKU のサイズを変更します。 詳細については、[ゲートウェイの設定 (SKU のサイズ変更または変更)](vpn-gateway-about-vpn-gateway-settings.md#resizechange) に関するセクションを参照してください。

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>ゲートウェイをリセットする

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後このアプリケーションを使用しない場合、または次のチュートリアルに進む場合は、次の手順に従ってリソースを削除してください。

1. ポータルの上部にある **検索** ボックスに、お使いのリソース グループの名前を入力し、検索結果からそれを選択します。

1. **[リソース グループの削除]** を選択します。

1. **[リソース グループ名を入力してください]** に、お使いのリソース グループを入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

VPN ゲートウェイが完成したら、接続を構成することができます。 以下の記事には、最も一般的ないくつかの構成を作成する方法が紹介されています。

> [!div class="nextstepaction"]
> [サイト間 VPN 接続](./tutorial-site-to-site-portal.md)

> [!div class="nextstepaction"]
> [ポイント対サイト VPN 接続](vpn-gateway-howto-point-to-site-resource-manager-portal.md)