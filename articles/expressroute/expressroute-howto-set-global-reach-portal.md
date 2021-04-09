---
title: 'Azure ExpressRoute: Azure portal を使用して Global Reach を構成する'
description: この記事では、ExpressRoute 回線を相互にリンクしてオンプレミス ネットワーク間にプライベート ネットワークを構築し、Azure portal を使用して Global Reach を有効にする方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/05/2021
ms.author: duau
ms.openlocfilehash: 336bd4aaf881b7315921ef374c92a2ac95ff3c8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431317"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Azure portal を使用して ExpressRoute Global Reach を構成する

この記事は、PowerShell を使用して ExpressRoute Global Reach を構成する際に役立ちます。 詳細については、[ExpressRouteRoute Global Reach](expressroute-global-reach.md) に関するページを参照してください。

 ## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、次の条件をご確認ください。

* ExpressRoute 回線のプロビジョニング [ワークフロー](expressroute-workflows.md)を理解していること。
* ExpressRoute 回線がプロビジョニングされた状態にあること。
* ExpressRoute 回線上に Azure プライベート ピアリングが構成されていること。
* PowerShell をローカルで実行する場合は、Azure PowerShell の最新バージョンがコンピューターにインストールされていることを確認します。

## <a name="identify-circuits"></a>回線を特定する

1. ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

2. 使用する ExpressRoute 回線を特定します。 任意の 2 つの ExpressRoute 回線のプライベート ピアリング間で ExpressRoute Global Reach を有効にすることができますが、それは、それらがサポートされている国や地域に配置されている場合に限られます。 回線は別々のピアリング場所に作成されている必要があります。 

   * サブスクリプションが両方の回線を所有している場合は、以降のセクションで構成を実行するために、どちらかの回線を選択できます。
   * 2 つの回線が異なる Azure サブスクリプションに含まれている場合は、1 つの Azure サブスクリプションからの承認が必要です。 その後、もう一方の Azure サブスクリプションで構成コマンドを実行するときに承認キーを渡します。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Express Route 回線の一覧のスクリーンショット。":::

## <a name="enable-connectivity"></a>接続性の確保

オンプレミス ネットワーク間の接続を有効にします。 同じ Azure サブスクリプション内にある回線と、異なるサブスクリプションである回線には、異なるセットの手順があります。

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>同じ Azure サブスクリプション内の ExpressRoute 回線

1. **[Azure プライベート]** のピアリング構成を選択します。 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Express Route の概要ページのスクリーンショット。":::

1. **[Global Reach の追加]** を選択して、 *[Global Reach の追加]* 構成ページを開きます。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="プライベート ピアリングから Global Reach を有効にする":::

1. *[Global Reach の追加]* 構成ページで、この構成の名前を指定します。 この回線に接続する "*ExpressRoute 回線*" を選択し、 *[Global Reach subnet]\(Global Reach サブネット\)* に **/29 の IPv4** を入力します。 このサブネット内の IP アドレスを使用して、2 つの ExpressRoute 回線間の接続を確立します。 このサブネット内のアドレスは Azure 仮想ネットワークやオンプレミス ネットワークで使用しないでください。 **[追加]** を選択して、回線をプライベート ピアリング構成に追加します。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="プライベート ピアリングでの Global Reach の追加のスクリーンショット。":::

1. **[保存]** を選択して Global Reach の構成を完了します。 操作が完了すると、両方の ExpressRoute 回線を介して 2 つのオンプレミス ネットワーク間の接続があります。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="プライベート ピアリング構成の保存のスクリーンショット。":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>異なる Azure サブスクリプションの ExpressRoute 回線

2 つの回線が同じ Azure サブスクリプション内にない場合は、承認が必要になります。 次の構成では、回線 2 のサブスクリプションから承認が生成されます。 それから承認キーが回線 1 に渡されます。

1. 承認キーを生成します。

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="承認キーの生成のスクリーンショット。"::: 

   回線 2 の回線リソース ID と承認キーをメモします。

1. **[Azure プライベート]** のピアリング構成を選択します。 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="概要ページのプライベート ピアリングのスクリーンショット。":::

1. **[Global Reach の追加]** を選択して、 *[Global Reach の追加]* 構成ページを開きます。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="プライベート ピアリングでの Global Reach の追加のスクリーンショット。":::

1. *[Global Reach の追加]* 構成ページで、この構成の名前を指定します。 **[承認の利用]** チェック ボックスをオンにします。 手順 1 で生成および取得した **承認キー** と **ExpressRoute 回線 ID** を入力します。 次に、 *[Global Reach subnet]\(Global Reach サブネット\)* に **/29 の IPv4** を入力します。 このサブネット内の IP アドレスを使用して、2 つの ExpressRoute 回線間の接続を確立します。 このサブネット内のアドレスは Azure 仮想ネットワークやオンプレミス ネットワークで使用しないでください。 **[追加]** を選択して、回線をプライベート ピアリング構成に追加します。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="承認キーを使用した Global Reach の追加のスクリーンショット。":::

1. **[保存]** を選択して Global Reach の構成を完了します。 操作が完了すると、両方の ExpressRoute 回線を介して 2 つのオンプレミス ネットワーク間の接続があります。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Global Reach を使用したプライベート ピアリング構成の保存のスクリーンショット。":::

## <a name="verify-the-configuration"></a>構成を確認する

ExpressRoute 回線の構成にある *[プライベート ピアリング]* を選択して、Global Reach 構成を確認します。 正しく構成されている場合、構成は次のようになります。

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="構成されている Global Reach のスクリーンショット。":::

## <a name="disable-connectivity"></a>接続の無効化

個々の回線間の接続を無効にするには、 *[Global Reach name]\(グローバル リーチ名\)* の横にある削除ボタンを選択して、それらの間の接続を削除します。 次に、 **[保存]** を選択して操作を完了します。

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Global Reach を無効にする方法を示すスクリーンショット。":::

操作が完了すると、ExpressRoute 回線を経由したオンプレミス ネットワーク間の接続は存在しなくなります。

## <a name="next-steps"></a>次のステップ
- [ExpressRoute Global Reach について詳しく学習する](expressroute-global-reach.md)
- [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)
- [ExpressRoute 回線を Azure 仮想ネットワークにリンクする](expressroute-howto-linkvnet-arm.md)
