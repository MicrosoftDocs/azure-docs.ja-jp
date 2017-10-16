---
title: "ExpressRoute の VNet への仮想ネットワーク ゲートウェイの追加: Portal: Azure | Microsoft Docs"
description: "この記事では、ExpressRoute の作成済みの Resource Manager VNet に仮想ネットワーク ゲートウェイを追加する方法を説明します。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: cherylmc
ms.openlocfilehash: 2bd0cf8be87937044ad515a2c6f253b1711bb2bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Azure Portal を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [クラシック - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [ビデオ - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

この記事では、既存の VNet 用の仮想ネットワーク ゲートウェイを追加する手順を説明します。 この記事では、既存の仮想ネットワーク (VNet) 用の VNet ゲートウェイを追加、サイズ変更、および削除する手順を説明します。 この構成の手順は、Resource Manager のデプロイ モデルを使用して作成され、ExpressRoute 構成で使用される予定の VNet 専用です。 ExpressRoute の仮想ネットワーク ゲートウェイとゲートウェイ構成設定の詳細については、[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](expressroute-about-virtual-network-gateways.md)を参照してください。 


## <a name="before-beginning"></a>作業を開始する前に

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
* ゲートウェイの IP 名 = "MyERGWVIP"
* ゲートウェイの種類 = "ExpressRoute"。ExpressRoute 構成には、この種類が必須です。
* ゲートウェイのパブリック IP 名 = "MyERGWVIP"

構成を開始する前に、これらの手順の[ビデオ](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)を表示できます。

## <a name="create-the-gateway-subnet"></a>ゲートウェイ サブネットを作成する

1. [ポータル](http://portal.azure.com)で、仮想ネットワーク ゲートウェイを作成する Resource Manager 仮想ネットワークに移動します。
2. VNet のブレードの **[設定]** セクションで、**[サブネット]** をクリックして [サブネット] ブレードを展開します。
3. **[サブネット]** ブレードで **[+ゲートウェイ サブネット]** をクリックして、**[サブネットの追加]** ブレードを開きます。 
   
    ![ゲートウェイ サブネットの追加](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "ゲートウェイ サブネットの追加")


4. サブネットの **[名前]** には、"GatewaySubnet" という値が自動的に入力されます。 この値は、Azure がゲートウェイ サブネットとしてこのサブネットを認識するために必要になります。 自動入力される **[アドレス範囲]** の値は、実際の構成要件に合わせて調整してください。 /27 以上 (/26 や /25 など) のゲートウェイ サブネットを作成することをお勧めします。 次に、**[OK]** をクリックして値を保存し、ゲートウェイ サブネットを作成します。

    ![サブネットの追加](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "サブネットの追加")

## <a name="create-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成する

1. ポータルで、左側の **[+]** をクリックし、検索ボックスに「Virtual Network ゲートウェイ」と入力します。 検索結果で "**仮想ネットワーク ゲートウェイ**" を探してその項目をクリックします。 **[Virtual Network ゲートウェイ]** ブレードで、下部にある **[作成]** をクリックします。 **[仮想ネットワーク ゲートウェイの作成]** ブレードが開きます。
2. **[Virtual Network ゲートウェイの作成]** ブレードで、Virtual Network ゲートウェイの値を入力します。

    ![[Virtual Network ゲートウェイの作成] ブレードのフィールド](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "[Virtual Network ゲートウェイの作成] ブレードのフィールド")
3. **[名前]**: ゲートウェイに名前を付けます。 これは、ゲートウェイ サブネットの名前付けと同じではありません。 作成するゲートウェイ オブジェクトの名前です。
4. **[ゲートウェイの種類]**: **[ExpressRoute]** を選択します。
5. **[SKU]**: ゲートウェイの SKU をドロップダウン リストから選択します。
6. **[場所]**: 仮想ネットワークの場所を指すように、**[場所]** フィールドを調整します。 対象の仮想ネットワークが存在するリージョンをこの場所が指していない場合、仮想ネットワークは [仮想ネットワークの選択] ボックスの一覧に表示されません。
7. このゲートウェイの追加先の仮想ネットワークを選択します。 **[仮想ネットワーク]** をクリックして **[仮想ネットワークの選択]** ブレードを開きます。 VNet を選択します。 VNet が表示されない場合は、実際の仮想ネットワークがあるリージョンが **[場所]** フィールドに指定されていることを確認してください。
9. パブリック IP アドレスを選択します。 **[パブリック IP アドレス]** をクリックして、**[パブリック IP アドレスの選択]** ブレードを開きます。 **[+新規作成]** をクリックして、**[パブリック IP アドレスの作成]** ブレードを開きます。 パブリック IP アドレスの名前を入力します。 このブレードで、パブリック IP アドレス オブジェクトが作成されます。このオブジェクトにパブリック IP アドレスが動的に割り当てられます。 **[OK]** をクリックして、このブレードへの変更を保存します。
10. **[サブスクリプション]**: 正しいサブスクリプションが選択されていることを確認します。
11. **[リソース グループ]**: この設定は、選択した Virtual Network によって決定されます。
12. 上記の設定を指定した後に **[場所]** を調整しないでください。
13. 設定を確認します。 ゲートウェイをダッシュボードに表示する場合は、ブレードの下部にある **[ダッシュボードにピン留めする]** を選択します。
14. **[作成]** をクリックして、ゲートウェイの作成を開始します。 設定が検証されて、ゲートウェイが作動します。 仮想ネットワーク ゲートウェイの作成は、完了するまでに最大で 45 分かかる場合があります。

## <a name="next-steps"></a>次のステップ
VNet ゲートウェイを作成したので、ExpressRoute 回線に VNet をリンクできるようになりました。 「 [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-portal-resource-manager.md)」を参照してください。