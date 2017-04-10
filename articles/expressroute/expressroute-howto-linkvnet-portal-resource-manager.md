---
title: "仮想ネットワークを ExpressRoute 回線にリンクする: Azure Portal | Microsoft Docs"
description: "このドキュメントでは、ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法の概要を示します。"
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b3c0cca9a6d5171b1248b0f463cbbb26641fc5f2
ms.openlocfilehash: a1a689dbfc35107b52f9b84f74ac8bfac0727a0e
ms.lasthandoff: 02/10/2017


---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>ExpressRoute 回線に仮想ネットワークを接続する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-linkvnet-arm.md)
> * [クラシック - PowerShell](expressroute-howto-linkvnet-classic.md)
> * [ビデオ - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> 
>  

この記事では、Resource Manager デプロイメント モデルと Azure ポータルを使用して Azure ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法について説明します。 仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかいまいません。

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>構成の前提条件
* 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、および[ワークフロー](expressroute-workflows.md)を確認してください。
* アクティブな ExpressRoute 回線が必要です。
  
  * 手順に従って、 [ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md) し、接続プロバイダー経由で回線を有効にしてください。
  * 回線用に Azure プライベート ピアリングが構成されていることを確認してください。 ルーティング手順については、 [ルーティングの構成](expressroute-howto-routing-portal-resource-manager.md) に関する記事を参照してください。
  * Azure プライベート ピアリングが構成されていることを確認します。また、エンド ツー エンド接続を有効にできるように、ネットワークと Microsoft の間の BGP ピアリングを起動しておく必要があります。
  * 仮想ネットワークと仮想ネットワーク ゲートウェイを作成し、完全にプロビジョニングします。 [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)を作成する手順 (手順 1. ～ 5. のみ) に従ってください。

* 最大 10 個の仮想ネットワークを標準 ExpressRoute 回線に接続できます。 標準 ExpressRoute 回線を使用する場合は、すべての仮想ネットワークが同じ地理的リージョンに存在する必要があります。 

* ExpressRoute Premium アドオンを有効にした場合は、ExpressRoute 回線の地理的リージョンの外部にある仮想ネットワークをリンクしたり、さらに多くの仮想ネットワークを ExpressRoute 回線にリンクしたりすることができます。 Premium アドオンの詳細については、 [FAQ](expressroute-faqs.md) を確認してください。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>同じサブスクリプション内の仮想ネットワークを回線に接続する

### <a name="to-create-a-connection"></a>接続を作成するには

> [!NOTE]
> ピアリングをレイヤー 3 プロバイダーが構成した場合、BGP の構成情報は表示されません。 回線がプロビジョニング済み状態の場合は、接続を作成できます。
>

1. ExpressRoute 回線と Azure プライベート ピアリングが正常に構成されていることを確認します。 指示に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md)し、[ルーティングを構成](expressroute-howto-routing-arm.md)します。 ExpressRoute 回線は次の図のようになります。

    ![ExpressRoute 回線のスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
   
2. ExpressRoute 回線に仮想ネットワーク ゲートウェイをリンクする接続のプロビジョニングを開始できるようになりました。 **[接続]**  >  **[追加]** をクリックし、**[接続の追加]** ブレードを開き、値を構成します。

    ![接続追加のスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  

3. 接続が正常に構成されると、接続オブジェクトにより、接続の情報が表示されます。

     ![接続オブジェクトのスクリーンショット](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

### <a name="to-delete-a-connection"></a>接続を削除するには
接続を削除するには、接続のブレードで **[削除]** アイコンを選択します。

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>別のサブスクリプション内の仮想ネットワークを回線に接続する
複数のサブスクリプションで ExpressRoute 回線を共有できます。 下図に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。

![サブスクリプション間接続](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- 大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。
- 組織内の各部門はサービスのデプロイ用に固有のサブスクリプションを使用できますが、1 つの ExpressRoute 回線を共有することで、オンプレミス ネットワークに接続し直すことができます。
- 1 つの部門 (この例では IT) で ExpressRoute 回線を所有できます。 組織内の他のサブスクリプションも ExpressRoute 回線を使用できます。

    > [!NOTE]
    > 専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになります。 すべての仮想ネットワークが同じ帯域幅を共有します。
    > 
    >

### <a name="administration"></a>管理
"回線所有者" は、ExpressRoute 回線リソースの権限のあるパワー ユーザーです。 回線所有者は、"回線ユーザー" が利用できる承認を作成できます。 回線ユーザーは、(ExpressRoute 回線とは別のサブスクリプション内にある) 仮想ネットワーク ゲートウェイの所有者です。 回線ユーザーは、承認を利用できます (仮想ネットワークごとに&1; つの承認)。

回線所有者は、承認をいつでも変更し、取り消す権限を持っています。 承認を取り消すと、アクセスが取り消されたサブスクリプションからすべてのリンク接続が削除されます。

### <a name="circuit-owner-operations"></a>回線所有者の操作

#### <a name="creating-an-authorization"></a>承認の作成

回線所有者は、承認を作成します。 その結果、回線ユーザーが各自の仮想ネットワーク ゲートウェイを ExpressRoute 回線に接続するために使用できる承認キーが作成されます。 承認は、1 つの接続に対してのみ有効です。

1. [ExpressRoute] ブレードで **[Authorizations] \(承認)** をクリックし、承認の**名前**を入力して **[保存]** をクリックします。

    ![[Authorizations]](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)

2. 構成が保存されたら、**リソース ID** と**承認キー**をコピーします。

    ![承認キー](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

#### <a name="deleting-authorizations"></a>承認の削除

接続を削除するには、接続のブレードで **[削除]** アイコンを選択します。

### <a name="circuit-user-operations"></a>回線ユーザーの操作

   > [!NOTE]
   > 回線ユーザーは、リソース ID と回線所有者が作成した承認キーを必要とします。 

#### <a name="redeeming-connection-authorizations"></a>接続承認の利用


[基本] タブで詳細を入力して **[OK]** をクリックします。

1.    **[+新規]** をクリックします。

    ![[新規] をクリック](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)

2.    Marketplace で **"接続"** を検索し、選択して **[作成]** をクリックします。

    ![接続の検索](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)

3.    **[接続の種類]** を [ExpressRoute] に設定します。


4.    [基本] ブレードで詳細を入力して **[OK]** をクリックします。

    ![[基本] ブレード](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)

5.    **[設定]** ブレードで **[Virtual Network ゲートウェイ]** を選択し、**[Redeem authorization (承認を利用する)]** チェック ボックスをオンにします。

6.    **承認キー**と**ピア回線 URI** を入力し、接続に名前を付けます。 **[OK]**をクリックします。

    ![[設定] ブレード](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)

7. **[概要]** ブレードの内容を確認し、**[OK]** をクリックします。

#### <a name="releasing-connection-authorizations"></a>接続承認の解除

ExpressRoute 回線を仮想ネットワークにリンクしている接続を削除することで、承認を解除できます。

## <a name="next-steps"></a>次のステップ
ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。

