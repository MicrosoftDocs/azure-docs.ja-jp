---
title: "ExpressRoute 回線の作成および変更: Azure Portal | Microsoft Docs"
description: "この記事では、ExpressRoute 回線の作成、プロビジョニング、確認、更新、削除、プロビジョニング解除の方法について説明します。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: cherylmc;ganesr
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 4880d63223055026a4fb1a906fb15f263d2a16df
ms.lasthandoff: 03/24/2017


---
# <a name="create-and-modify-an-expressroute-circuit"></a>ExpressRoute 回線の作成と変更
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-circuit-arm.md)
> * [ビデオ - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> 
>

この記事では、Azure ポータルと Azure Resource Manager デプロイ モデルを使用して、Azure ExpressRoute 回線を作成する方法について説明します。 以下の手順では、回線の状態確認、更新、または削除およびプロビジョニング解除の方法も示します。

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。
* [Azure ポータル](https://portal.azure.com)に対するアクセスできることを確認します。
* 新しいネットワーク リソースを作成するアクセス許可があることを確認します。 適切なアクセス許可がない場合は、アカウント管理者に連絡してください。
* 手順をより理解するため、開始する前に[ビデオを確認](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)できます。

## <a name="create-and-provision-an-expressroute-circuit"></a>ExpressRoute 回線の作成とプロビジョニング
### <a name="1-sign-in-to-the-azure-portal"></a>1.Azure ポータルにサインインします。
ブラウザーから [Azure ポータル](http://portal.azure.com) に移動し、Azure アカウントでサインインします。

### <a name="2-create-a-new-expressroute-circuit"></a>2.新しい ExpressRoute 回線を作成する
> [!IMPORTANT]
> ExpressRoute 回線の課金は、サービス キーが発行されたときから始まります。 接続プロバイダーが回線をプロビジョニングする準備ができたら、この操作を実行します。
> 
> 

1. 新しいリソースを作成するオプションを選択して、ExpressRoute 回線を作成できます。 次の図に示すように、**[新規]**  >  **[ネットワーク]**  >  **[ExpressRoute]** をクリックします。
   
    ![ExpressRoute 回線の作成](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. **[ExpressRoute]** をクリックすると、**[ExpressRoute 回線の作成]** ブレードが表示されます。 このブレードに値を入力するときは、SKU レベルとデータ計測方法を正しく指定してください。
   
   * **[レベル]** によって、ExpressRoute の Standard と Premium のどちらのアドオンが有効になるかが決まります。 標準 SKU を取得する場合は **[Standard]** を、プレミアム アドオンの場合は **[Premium]** を指定できます。
   * **[データ計測方法]** によって、課金の種類が決まります。 従量制課金データ プランの場合は **[従量制]** を、無制限データ プランの場合は **[無制限]** を指定できます。 課金の種類を **[従量制]** から **[無制限]** に変更することはできますが、**[無制限]** から **[従量制]** に変更することはできません。
     
     ![SKU レベルとデータ計測方法の構成](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)

> [!IMPORTANT]
> ピアリングの場所は、Microsoft とピアリングしている[物理的な場所](expressroute-locations.md)を示すことに注意してください。 この場所は "Location" プロパティに**リンクされていません**。それは、Azure Network Resource Provider が配置されている地理的な場所を参照します。 それらは関連付けられていませんが、回路のピアリングの場所と地理的に近い場所にある Network Resource Provider を選択することをお勧めします。 
> 
> 

### <a name="3-view-the-circuits-and-properties"></a>3.回線とプロパティを表示する
**すべての回線を表示する**

左側のメニューで **[すべてのリソース]** を選択すると、作成したすべての回線を表示できます。

![回路の表示](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**プロパティを表示する**

    You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![プロパティの表示](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4.プロビジョニングのためにサービス キーを接続プロバイダーに送信する
このブレードの **[プロバイダーの状態]** は、サービス プロバイダー側でのプロビジョニングの現在の状態に関する情報を提供します。 **[回線の状態]** は、Microsoft 側での状態を提供します。 回線のプロビジョニング状態に関する詳細については、 [ワークフロー](expressroute-workflows.md#expressroute-circuit-provisioning-states) に関する記事を参照してください。

新しい ExpressRoute 回線を作成する場合、この回線は次の状態になります。

プロバイダーの状態: 未プロビジョニング<BR>
回線の状態: 有効

![プロビジョニング プロセスの開始](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

回線は、接続プロバイダーが有効にしている間、次の状態に変化します。

プロバイダーの状態: プロビジョニング中<BR>
回線の状態: 有効

ExpressRoute 回線をユーザーが使用できるように、次の状態にする必要があります。

プロバイダーの状態: プロビジョニング済み<BR>
回線の状態: 有効

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5.回線キーのステータスと状態を定期的に確認する
関心のある回線を選択し、その回線のプロパティを表示できます。 **[プロバイダーの状態]** が **[プロビジョニング済み]** になっていることを確認してから続行します。

![回線とプロバイダーの状態](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)

### <a name="6-create-your-routing-configuration"></a>6.ルーティング構成を作成する
回線ピアリングの作成と変更の詳しい手順については、「 [PowerShell を使用した ExpressRoute 回線のルーティングの作成と変更](expressroute-howto-routing-portal-resource-manager.md) 」を参照してください。

> [!IMPORTANT]
> 次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。 サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IP VPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成および管理します。
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7.ExpressRoute 回線への仮想ネットワークのリンク
次に、ExpressRoute 回線に仮想ネットワークをリンクします。 Resource Manager デプロイメント モデルを使用するときは、「 [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md) 」を参照してください。

## <a name="getting-the-status-of-an-expressroute-circuit"></a>ExpressRoute 回線の状態の取得
回線を選択することで、回線の状態を確認できます。 

![ExpressRoute 回線の状態](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

## <a name="modifying-an-expressroute-circuit"></a>ExpressRoute 回線の変更
ExpressRoute 回線の特定のプロパティは、接続に影響を与えることなく変更できます。

ダウンタイムなく、次を実行できます。

* ExpressRoute 回線の ExpressRoute Premium アドオンを有効または無効にします。
* ポートに使用可能な容量があれば、ExpressRoute 回線の帯域幅を増やします。 回線の帯域幅のダウングレードはサポートされていないことに注意してください。 
* 課金プランを従量制課金データから無制限データに変更します。 無制限データから従量制課金データへの課金プランの変更はサポートされていないことに注意してください。
* *従来の操作の許可*を有効または無効にできます。

制限と制約事項の詳細は、「 [ExpressRoute の FAQ](expressroute-faqs.md)」を参照してください。

ExpressRoute 回線を変更するには、下の図に示すように **[構成]** をクリックします。

![回線の変更](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

帯域幅、SKU、課金モデルを変更し、構成ブレード内の従来の操作を許可することができます。

> [!IMPORTANT]
> 既存のポートの容量が不十分な場合、ExpressRoute 回線の再作成が必要になる可能性があります。 その場所に使用可能な追加の容量がない場合、回路をアップグレードすることはできません。
>
> 中断せずに ExpressRoute 回線の帯域幅を減らすことはできません。 帯域幅をダウングレードするには、ExpressRoute 回線のプロビジョニングを解除してから、新しい ExpressRoute 回線を再度プロビジョニングする必要があります。
> 
> Standard 回線で許可されるリソースより多くのリソースを使用する場合、Premium アドオンの無効化操作が失敗することがあります。
> 
> 

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>ExpressRoute 回線のプロビジョニング解除と削除
**[削除]** アイコンを選択し、ExpressRoute 回線を削除できます。 以下の点に注意してください。

* ExpressRoute 回線からすべての仮想ネットワークのリンクを解除する必要があります。 この操作が失敗した場合は、回線にリンクされている仮想ネットワークがないか確認してください。
* ExpressRoute 回線サービス プロバイダーのプロビジョニング状態が**プロビジョニング中**または**プロビジョニング済み**の場合、サービス プロバイダー側の回線のプロビジョニングを解除するには、サービス プロバイダーに連絡する必要があります。 Microsoft は、サービス プロバイダーが回線のプロビジョニング解除を完了し、通知するまで、リソースの予約と課金を続行します。
* サービス プロバイダーが回線のプロビジョニングを解除済み (サービス プロバイダーのプロビジョニング状態が **未プロビジョニング**に設定されている) の場合、回線を削除することができます。 回線の課金が停止されます。

## <a name="next-steps"></a>次のステップ
回線を作成したら、次の操作を必ず実行します。

* [ExpressRoute 回線のルーティングの作成と変更を行う](expressroute-howto-routing-portal-resource-manager.md)
* [仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-arm.md)


