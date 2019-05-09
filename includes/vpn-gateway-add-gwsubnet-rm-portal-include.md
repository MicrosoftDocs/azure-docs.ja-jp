---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444222"
---
1. [Azure portal](http://portal.azure.com) で、仮想ネットワーク ゲートウェイを作成する Resource Manager 仮想ネットワークを選択します。

2. 仮想ネットワークのページの **[設定]** セクションで、**[サブネット]** を選択して **[サブネット]** ページを展開します。

3. **[サブネット]** ページで **[ゲートウェイ サブネット]** を選択して **[サブネットの追加]** ページを開きます。

   ![ゲートウェイ サブネットの追加](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "ゲートウェイ サブネットの追加")

4. サブネットの **[名前]** には、*GatewaySubnet* という値が自動的に入力されます。 この値は、Azure がゲートウェイ サブネットとしてこのサブネットを認識するために必要になります。 自動入力される **[アドレス範囲]** の値を実際の構成要件に合わせて調整し、**[OK]** を選択してサブネットを作成します。

   ![サブネットの追加](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "サブネットの追加")