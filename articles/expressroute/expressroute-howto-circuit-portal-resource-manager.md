---
title: 'クイックスタート: ExpressRoute を使った回線の作成と変更 - Azure portal'
description: このクイックスタートでは、Azure portal を使用して、ExpressRoute 回線の作成、プロビジョニング、確認、更新、削除、プロビジョニング解除を行う方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/21/2020
ms.author: duau
ms.openlocfilehash: b0fd844021e7398e061072d7939b782616580c1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185200"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>クイックスタート: ExpressRoute 回線の作成と変更

このクイックスタートでは、Azure portal と Azure Resource Manager デプロイ モデルを使用して ExpressRoute 回線を作成する方法について説明します。 状態の確認、および回線の更新、削除、プロビジョニング解除を行うこともできます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。
* 手順をより理解するため、開始する前に[ビデオを参照](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)できます。

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute 回線の作成とプロビジョニング

### <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

### <a name="create-a-new-expressroute-circuit"></a>新しい ExpressRoute 回線を作成する

> [!IMPORTANT]
> ExpressRoute 回線の課金は、サービス キーが発行されたときから始まります。 接続プロバイダーが回線をプロビジョニングする準備ができたら、この操作を実行します。

新しいリソースを作成するオプションを選択して、ExpressRoute 回線を作成できます。 

1. Azure portal のメニューで、**[リソースの作成]** を選択します。 次の図に示すように、 **[ネットワーク]**  >  **[ExpressRoute]** を選択します。

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="ExpressRoute 回線の作成":::

2. **[ExpressRoute]** を選択すると、 **[Create ExpressRoute]\(ExpressRoute の作成\)** ページが表示されます。 回線の **リソース グループ**、**リージョン**、**名前** を指定します。 次に、**次のステップ: 構成 >** をクリックします。

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="リソース グループとリージョンを構成する":::

3. このページで値を入力するときは、正しい SKU レベル (Local、Standard、または Premium) とデータ計測課金モデル (無制限または従量制) を必ず指定してください。

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="回線を構成する":::
    
    * **[ポートの種類]** により、サービス プロバイダーに接続するか、またはピアリングの場所で Microsoft のグローバル ネットワークに直接接続するかが決まります。
    * **[新規作成またはクラシックからインポート]** により、新しい回線を作成するか、または従来の回線を Azure Resource Manager に移行するかが決まります。
    * **[プロバイダー]** は、サービスの要求先となるインターネット サービス プロバイダーです。
    * **ピアリングの場所** とは、Microsoft とピアリングしている物理的な場所です。

    > [!IMPORTANT]
    > ピアリングの場所は、Microsoft とピアリングしている[物理的な場所](expressroute-locations.md)を示します。 この場所は "Location" プロパティに **リンクされていません**。それは、Azure Network Resource Provider が配置されている地理的な場所を参照します。 それらは関連付けられていませんが、回路のピアリングの場所と地理的に近い場所にある Network Resource Provider を選択することをお勧めします。

    * **[SKU]** によって、ExpressRoute Local、ExpressRoute Standard、または ExpressRoute Premium のどのアドオンを有効にするかが決まります。 ローカルの SKU を取得する場合は **[Local]** 、標準の SKU を取得する場合は **[Standard]** 、Premium アドオンの場合は **[Premium]** を指定できます。 SKU を変更し、プレミアム アドオンを有効にすることができます。
    > [!IMPORTANT]
    > SKU を **[Standard] または [Premium]** から **[Local]** に変更することはできません。
    
    * **[課金モデル]** により、課金の種類が決まります。 従量制課金データ プランの場合は **[従量制]** を、無制限データ プランの場合は **[無制限]** を指定できます。 課金の種類を **[従量制]** から **[無制限]** に変更することができます。

    > [!IMPORTANT]
    > 種類を **[無制限]** から **[従量制]** に変更することはできません。

    * **[Allow classic operation]\(クラシック操作を許可する\)** により、従来の仮想ネットワークを回線にリンクすることができます。

### <a name="view-the-circuits-and-properties"></a>回線とプロパティを表示する

**すべての回線を表示する**

左側のメニューで **[すべてのサービス] > [ネットワーク] > [ExpressRoute 回線]** を選択すると、作成したすべての回線を表示できます。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Expressroute 回線のメニュー":::

サブスクリプション内で作成されたすべての Expressroute 回線がここに表示されます。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Expressroute 回線の一覧":::

**プロパティを表示する**

回線を選択し、その回線のプロパティを表示できます。 回線の **[概要]** ページでは、サービス キーがサービス キー フィールドに表示されます。 プロビジョニング プロセスを完了するには、回線のサービス キーを参照し、それをサービス プロバイダーに提供します。 サービス キーは回線に固有です。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="プロパティを表示する":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>プロビジョニングのためにサービス キーを接続プロバイダーに送信する

このページの **[プロバイダーの状態]** には、サービス プロバイダー側でのプロビジョニングの現在の状態が表示されます。 **[回線の状態]** は、Microsoft 側での状態を提供します。 回線のプロビジョニング状態に関する詳細については、 [ワークフロー](expressroute-workflows.md#expressroute-circuit-provisioning-states) に関する記事を参照してください。

新しい ExpressRoute 回線を作成する場合、この回線は次の状態になります。

プロバイダーの状態: **未プロビジョニング**<BR>
回線の状態: **有効**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="プロビジョニング プロセスを開始":::

回線は、接続プロバイダーが有効にしている間、次の状態に変化します。

プロバイダーの状態: **プロビジョニング**<BR>
回線の状態: **有効**

ExpressRoute 回線を使用するには、次の状態になっている必要があります。

プロバイダーの状態: **プロビジョニング済み**<BR>
回線の状態: **有効**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>回線キーのステータスと状態を定期的に確認する

関心のある回線を選択し、その回線のプロパティを表示できます。 **[プロバイダーの状態]** が **[プロビジョニング済み]** になっていることを確認してから続行します。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="回線とプロバイダーの状態":::

### <a name="create-your-routing-configuration"></a>ルーティング構成を作成する

回線ピアリングの作成と変更の詳しい手順については、「 [PowerShell を使用した ExpressRoute 回線のルーティングの作成と変更](expressroute-howto-routing-portal-resource-manager.md) 」を参照してください。

> [!IMPORTANT]
> 次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。 サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IP VPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成して管理します。

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>ExpressRoute 回線への仮想ネットワークのリンク

次に、ExpressRoute 回線に仮想ネットワークをリンクします。 Resource Manager デプロイ モデルを使用するときは、「[ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-arm.md)」を参照してください。

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>ExpressRoute 回線の状態の取得

回線を選択し、[概要] ページを表示することで回線の状態を表示できます。

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute 回線の変更

ExpressRoute 回線の特定のプロパティは、接続に影響を与えることなく変更できます。 帯域幅、SKU、課金モデルを変更し、**[構成]** ページの従来の操作を許可することができます。 制限と制約事項の詳細については、「[ExpressRoute の FAQ](expressroute-faqs.md)」を参照してください。

ダウンタイムなく、次のタスクを実行できます。

* ExpressRoute 回線の ExpressRoute Premium アドオンを有効または無効にします。

> [!IMPORTANT]
  > SKU を **[Standard] または [Premium]** から **[Local]** に変更することはサポートされていません。

* ポートに使用可能な容量があれば、ExpressRoute 回線の帯域幅を増やします。

  > [!IMPORTANT]
  > 回線の帯域幅のダウングレードはサポートされていません。

* 課金プランを *従量制課金データ* から *無制限データ* に変更します。

  > [!IMPORTANT]
  > **無制限データ** から **従量制課金データ** への課金プランの変更はサポートされていません。

* *従来の操作の許可* を有効または無効にできます。
  > [!IMPORTANT]
  > 既存のポートの容量が不十分な場合、ExpressRoute 回線の再作成が必要になる可能性があります。 その場所に使用可能な追加の容量がない場合、回路をアップグレードすることはできません。
  >
  > 帯域幅はシームレスにアップグレードできますが、ExpressRoute 回線の帯域幅を中断なく減らすことはできません。 帯域幅をダウングレードするには、ExpressRoute 回線のプロビジョニングを解除してから、新しい ExpressRoute 回線を再度プロビジョニングする必要があります。
  >
  > Standard 回線で許可されるリソースより多くのリソースを使用する場合、Premium アドオンの無効化操作が失敗することがあります。

ExpressRoute 回線を変更するには、 **[構成]** を選択します。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="回線の変更":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute 回線のプロビジョニング解除

ExpressRoute 回線サービス プロバイダーのプロビジョニング状態が **プロビジョニング中** または **プロビジョニング済み** の場合、サービス プロバイダー側の回線のプロビジョニングを解除するには、サービス プロバイダーに連絡する必要があります。 Microsoft は、サービス プロバイダーが回線のプロビジョニング解除を完了し、通知するまで、リソースの予約と課金を続行します。

> [!NOTE]
>* プロビジョニングを解除する前に、ExpressRoute 回線から "*すべての仮想ネットワーク*" のリンクを解除する必要があります。 この操作が失敗した場合は、回線にリンクされている仮想ネットワークがないか確認してください。
>* サービス プロバイダーが回線のプロビジョニングを解除済み (サービス プロバイダーのプロビジョニング状態が **未プロビジョニング** に設定されている) の場合、回線を削除することができます。 これによって回線の課金が停止されます。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

**[削除]** アイコンを選択し、ExpressRoute 回線を削除できます。 続行する前に、プロバイダーのステータスが確実に "*未プロビジョニング*" になっているようにします。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="回線を削除する":::

## <a name="next-steps"></a>次のステップ

回線を作成したら、次の手順に移ります。

> [!div class="nextstepaction"]
> [ExpressRoute 回線のルーティングの作成と変更を行う](expressroute-howto-routing-portal-resource-manager.md)
