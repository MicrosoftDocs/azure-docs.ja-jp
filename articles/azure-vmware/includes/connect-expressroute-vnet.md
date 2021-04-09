---
title: ExpressRoute を仮想ネットワーク ゲートウェイに接続する
description: ExpressRoute を仮想ネットワーク ゲートウェイに接続する手順。
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 6e2e3748dbfd8d69b53dcc4c3a09809756ac48dc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494362"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. [Azure に vSphere クラスターをデプロイする](../tutorial-create-private-cloud.md)チュートリアルで作成したプライベート クラウドに移動します。 **[管理]** の下の **[接続]** を選択し、 **[ExpressRoute]** タブを選択します。

1. 承認キーをコピーします。 承認キーがない場合は作成する必要があります。 **[+ Request an authorization key]\(+ 承認キーの要求\)** を選択します。

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="認可キーをコピーします。承認キーがない場合は作成する必要があります。[+ Request an authorization key]\(+ 承認キーの要求\) を選択します。" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. 前の手順で作成した仮想ネットワーク ゲートウェイに移動し、 **[設定]** の下の **[接続]** を選択します。 **[接続]** ページで、 **[+ 追加]** を選択します。

1. **[接続の追加]** ページで、各フィールドの値を指定し、 **[OK]** を選択します。 

   | フィールド | 値 |
   | --- | --- |
   | **名前**  | 接続名を入力します。  |
   | **接続の種類**  | **[ExpressRoute]** を選択します。  |
   | **承認の利用**  | このボックスが選択されていることを確認します。  |
   | **仮想ネットワーク ゲートウェイ** | 前に作成した仮想ネットワーク ゲートウェイ。  |
   | **承認キー**  | リソース グループの [ExpressRoute] タブで承認キーをコピーし、貼り付けます。 |
   | **ピア回線の URI**  | リソース グループの [ExpressRoute] タブで ExpressRoute ID をコピーし、貼り付けます。  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="ExpressRoute を仮想ネットワークゲートウェイに接続するための [接続の追加] ページのスクリーンショット。":::

ExpressRoute 回線と仮想ネットワークの間の接続が作成されます。

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="仮想ネットワーク ゲートウェイ接続のスクリーンショット。":::