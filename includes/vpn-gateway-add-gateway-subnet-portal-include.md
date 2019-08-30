---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780109"
---
ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイ サービスが使用する予約済み IP アドレスが含まれます。 ゲートウェイ サブネットを作成します。

1. ポータルで、仮想ネットワーク ゲートウェイを作成する仮想ネットワークに移動します。
2. ご自身の仮想ネットワーク ページで、 **[サブネット]** をクリックして、 **[VNet1 - サブネット]** ページを展開します。
3. **[+ゲートウェイ サブネット]** をクリックして、 **[サブネットの追加]** ページを開きます。

   ![ゲートウェイ サブネットの追加](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "ゲートウェイ サブネットの追加")
4. ご自身のサブネットの **[名前]** には、"GatewaySubnet" という必須の値が自動的に入力されます。 自動入力された **[アドレス範囲 (CIDR ブロック)]** を、次の値に合わせて調整します。

   **アドレス範囲 (CIDR ブロック)** :10.1.255.0/27

   ![ゲートウェイ サブネットの追加](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "ゲートウェイ サブネットの追加")
5. 残りの設定は、既定値の **[なし]** または **[0 個を選択済み]** のままにします。 その後、 **[OK]** をクリックして、ゲートウェイ サブネットを作成します。