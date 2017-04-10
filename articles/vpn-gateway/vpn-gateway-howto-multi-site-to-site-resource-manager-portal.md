---
title: "Azure Portal を使用して、Resource Manager デプロイメント モデルの仮想ネットワークに複数のVPN ゲートウェイ サイト間接続を追加する方法 | Microsoft Docs"
description: "マルチサイトのサイト間接続を、既存の接続がある VPN Gateway に追加する"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9906602e0a154c2dc3d6e458179c7e9346df2852


---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>既存の VPN ゲートウェイ接続を使用してサイト間接続を VNet に追加する
> [!div class="op_single_selector"]
> * [Resource Manager - ポータル](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [クラシック - PowerShell](vpn-gateway-multi-site.md)
> 
> 

この記事では、Azure Portal を使用して、既存の接続がある VPN ゲートウェイにサイト間 (S2S) 接続を追加する方法について説明します。 この種類の構成は、一般に "マルチサイト" 構成と呼ばれます。 

この記事の説明で、既にサイト間接続、ポイント対サイト接続、またはVNet 間接続のある VNet にサイト間接続を追加できます。 接続を追加する際には、制限があります。 構成を始める前に、この記事の「[開始する前に](#before)」セクションで内容をご確認ください。 

この記事は、ルートベースの VPN Gateway のある Resource Manager デプロイメント モデルを使用して作成された VNet を対象としています。 次の手順は、ExpressRoute/サイト間の共存接続の構成には適用されません。 共存接続の詳細については、「[クラシック デプロイ モデルにおいて共存する ExpressRoute 接続とサイト間接続を構成する](../expressroute/expressroute-howto-coexist-resource-manager.md)」を参照してください。

### <a name="deployment-models-and-methods"></a>デプロイメント モデルおよび方法
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

以下の表は、この構成について新しい記事、追加のツールが利用できるようになったら更新されるものです。 記事が利用できるようになったら、表から直接リンクできるようにします。

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="a-namebeforeabefore-you-begin"></a><a name="before"></a>開始する前に
次の項目についてご確認ください。

* ExpressRoute/S2S と共存する接続の作成ではない。
* 既存の接続のある Resource Manager デプロイメント モデルを使用して作成した仮想ネットワークがある。
* VNet の仮想ネットワーク ゲートウェイがルートベースである。 PolicyBased の VPN ゲートウェイである場合は、仮想ネットワーク ゲートウェイを削除して、ルートベースとして新しい VPN ゲートウェイを作成する必要があります。
* この VNet が接続する VNet のアドレスの範囲のいずれも重複していないこと。
* 互換性のある VPN デバイスがあり、デバイスを構成できる人員がいる。 「 [VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。 VPN デバイスの構成に詳しくない場合や、オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。
* VPN デバイスの外部接続用パブリック IP アドレスがある。 この IP アドレスを NAT の内側に割り当てることはできません。

## <a name="a-namepart1apart-1---configure-a-connection"></a><a name="part1"></a>パート 1 - 接続の構成
1. ブラウザーから [Azure Portal](http://portal.azure.com) に移動します。必要であれば Azure アカウントでサインインします。
2. **[すべてのリソース]** をクリックして、リソースの一覧から **[仮想ネットワーク ゲートウェイ]** を見つけます。
3. **[仮想ネットワーク ゲートウェイ]**のブレードで、**[接続]** をクリックします。
   
    ![[接続] ブレード](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Connections blade")<br>
4. **[接続]** ブレードで、**[追加]** をクリックします。
   
    ![接続の追加ボタン](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Add connection button")<br>
5. **[接続追加]** ブレードで、次のフィールドを入力します。
   
   * **[名前]:** 作成している接続先のサイトに付ける名前です。
   * **[接続の種類]:** **[サイト間 (IPsec)]** を選択します。
     
     ![接続の追加ブレード](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection blade")<br>

## <a name="a-namepart2apart-2---add-a-local-network-gateway"></a><a name="part2"></a>パート 2 - ローカル ネットワーク ゲートウェイの追加
1. **[ローカル ネットワーク ゲートウェイ]**、***[ローカル ネットワーク ゲートウェイを選択する]*** をクリックします。 **[ローカル ネットワーク ゲートウェイの選択]** ブレードが開きます。
   
    ![ローカル ネットワーク ゲートウェイの選択](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choose local network gateway")<br>
2. **[新規作成]** をクリックして **[ローカル ネットワーク ゲートウェイの作成]** ブレードを開きます。
   
    ![ローカル ネットワーク ゲートウェイの作成ブレード](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>
3. **[ローカル ネットワーク ゲートウェイの作成]** ブレードで、次のフィールドを入力します。
   
   * **[名前]:** ローカル ネットワーク ゲートウェイ リソースに付ける名前です。
   * **[IP アドレス]:** 接続先のサイト上の VPN デバイスのパブリック IP アドレスです。
   * **[アドレス空間]:** 新しいローカル ネットワーク サイトにルーティングするアドレス空間です。
4. **[ローカル ネットワーク ゲートウェイの作成]** ブレードで **[OK]** をクリックして変更を保存します。

## <a name="a-namepart3apart-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>パート 3 - 共有キーを追加して接続を作成する
1. **[接続の追加]** ブレードで、接続の作成に使用する共有キーを追加します。 VPN デバイスから共有キーを取得するか、新しく作成して同じ共有キーを使用するよう VPN デバイスを構成します。 キーが完全に同一であることが重要です。
   
    ![共有キー](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Shared key")<br>
2. ブレード下部の **[OK]** をクリックすると、接続が作成されます。

## <a name="a-namepart4apart-4---verify-the-vpn-connection"></a><a name="part4"></a>パート 4: VPN 接続の確認
VPN 接続の確認はポータルで行えるほか、PowerShell を使って確認することもできます。

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>次のステップ
* 接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、仮想マシンの [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) を参照してください。




<!--HONumber=Nov16_HO3-->


