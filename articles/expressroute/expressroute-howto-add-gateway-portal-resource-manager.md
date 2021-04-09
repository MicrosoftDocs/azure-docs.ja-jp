---
title: チュートリアル:Azure ExpressRoute - ゲートウェイを VNet (Azure portal) に追加する
description: このチュートリアルでは、Azure portal を使用して仮想ネットワーク ゲートウェイを ExpressRoute の VNet に追加する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 6c6969fdf413c4eb5e7bbcf046fc397834d6c0a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038919"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>チュートリアル:Azure Portal を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [クラシック - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

このチュートリアルでは、既存の VNet 用の仮想ネットワーク ゲートウェイを追加する手順を説明します。 この記事では、既存の仮想ネットワーク (VNet) 用の VNet ゲートウェイを追加、サイズ変更、および削除する手順を説明します。 この構成の手順は、Resource Manager デプロイ モデルを使用して作成され、ExpressRoute 構成で使用される予定の VNet 専用です。 ExpressRoute の仮想ネットワーク ゲートウェイとゲートウェイ構成設定の詳細については、[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](expressroute-about-virtual-network-gateways.md)を参照してください。 

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - ゲートウェイ サブネットを作成します。
> - 仮想ネットワーク ゲートウェイを作成します。

## <a name="prerequisites"></a>前提条件

このタスクの手順では、以下の構成参照一覧の値に基づいて VNet を使用します。 この一覧は、手順例で使用します。 参照として使用する一覧をコピーし、値を独自の値で置き換えることができます。

**構成参照一覧**

* Virtual Network 名 = "TestVNet"
* Virtual Network のアドレス空間 = 192.168.0.0/16
* サブネット名 = "FrontEnd" 
    * サブネットのアドレス空間 = "192.168.1.0/24"
* リソース グループ = "TestRG"
* 場所 = "米国東部"
* ゲートウェイ サブネット名: "GatewaySubnet"。ゲートウェイ サブネットには、常に *GatewaySubnet* という名前を付ける必要があります。
    * ゲートウェイ サブネットのアドレス空間 = "192.168.200.0/26"
* ゲートウェイ名 = "ERGW"
* ゲートウェイのパブリック IP 名 = "MyERGWVIP"
* ゲートウェイの種類 = "ExpressRoute"。ExpressRoute 構成には、この種類が必須です。

構成を開始する前に、これらの手順の[ビデオ](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)を表示できます。

> [!IMPORTANT]
> 現在、プライベート ピアリングの IPv6 サポートは **パブリック プレビュー** の段階にあります。 IPv6 ベースのプライベート ピアリングを構成して仮想ネットワークを ExpressRoute 回線に接続したい場合は、仮想ネットワークをデュアル スタックにし、[Azure VNet での IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview) に関するガイドラインに従ってください。
> 
> 

## <a name="create-the-gateway-subnet"></a>ゲートウェイ サブネットを作成する

1. [ポータル](https://portal.azure.com)で、仮想ネットワーク ゲートウェイを作成する Resource Manager 仮想ネットワークに移動します。
1. VNet の **[設定]** セクションで、 **[サブネット]** を選択し、[サブネット] 設定を展開します。
1. **[サブネット]** 設定で、 **[+ ゲートウェイ サブネット]** を選択し、ゲートウェイ サブネットを追加します。 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="ゲートウェイ サブネットを追加する":::

1. サブネットの **[名前]** には、"GatewaySubnet" という値が自動的に入力されます。 この値は、Azure がゲートウェイ サブネットとしてこのサブネットを認識するために必要になります。 自動入力される **[アドレス範囲]** の値は、実際の構成要件に合わせて調整してください。 /27 以上 (/26 や /25 など) のゲートウェイ サブネットを作成することをお勧めします。

    デュアル スタックの仮想ネットワークを使用していて、ExpressRoute 経由で IPv6 ベースのプライベート ピアリングを使用する予定の場合は、 **[IPv6 アドレス空間の追加]** をクリックし、**IPv6 アドレス範囲** の値を入力します。

    次に、 **[OK]** を選択して値を保存し、ゲートウェイ サブネットを作成します。

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="サブネットの追加":::

## <a name="create-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成する

1. ポータルの左側で、 **[リソースの作成]** を選択し、検索ボックスに「仮想ネットワーク ゲートウェイ」と入力します。 検索結果で **仮想ネットワーク ゲートウェイ** を見つけて、そのエントリを選択します。 **[仮想ネットワーク ゲートウェイ]** のページで、 **[作成]** を選択します。
1. **[仮想ネットワーク ゲートウェイの作成]** ページで、これらの設定を入力または選択します。

    | 設定 | 値 |
    | --------| ----- |
    | サブスクリプション | 正しいサブスクリプションが選択されていることを確認します。 |
    | リソース グループ | 仮想ネットワークを選択すると、リソース グループが自動的に選択されます。 | 
    | 名前 | ゲートウェイに名前を付けます。 これは、ゲートウェイ サブネットの名前付けと同じではありません。 作成するゲートウェイ オブジェクトの名前です。|
    | リージョン | 仮想ネットワークが存在する場所を指すように、 **[リージョン]** フィールドを変更します。 仮想ネットワークが存在するリージョンをこの場所が指していない場合、仮想ネットワークは [仮想ネットワークの選択] ドロップダウンに表示されません。 |
    | ゲートウェイの種類 | **[ExpressRoute]** を選択します。|
    | SKU | ゲートウェイの SKU をドロップダウンから選択します。 |
    | 仮想ネットワーク | *[TestVNET]* を選択します。 |
    | パブリック IP アドレス | **[新規作成]** を選択します。|
    | パブリック IP アドレス名 | パブリック IP アドレスの名前を指定します。 |

    > [!IMPORTANT]
    > ExpressRoute 経由で IPv6 ベースのプライベート ピアリングを使用する予定の場合は、**SKU** に対して AZ SKU (ErGw1AZ、ErGw2AZ、ErGw3AZ) を選択してください。
    > 
    > 

1. **[確認および作成]** 、 **[作成]** の順に選択して、ゲートウェイの作成を開始します。 設定が検証されて、ゲートウェイが作動します。 仮想ネットワーク ゲートウェイの作成は、完了するまでに最大で 45 分かかる場合があります。

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="[仮想ネットワーク ゲートウェイの作成] ページのフィールド":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ExpressRoute ゲートウェイが不要になった場合は、仮想ネットワーク リソース グループでゲートウェイを見つけて、 **[削除]** を選択します。 ゲートウェイが回線に確実に接続されていないようにします。

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="仮想ネットワーク ゲートウェイの削除":::

## <a name="next-steps"></a>次のステップ
VNet ゲートウェイを作成したので、VNet を ExpressRoute 回線にリンクできます。 

> [!div class="nextstepaction"]
> [仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-portal-resource-manager.md)
