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
ms.openlocfilehash: 42e983ead6f7562c6a31cf9ef4ad2d97d0ff9707
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673416"
---
1. [Azure portal](https://portal.azure.com) で、仮想ネットワーク ゲートウェイを作成する Resource Manager 仮想ネットワークを選択します。

2. 仮想ネットワークのページの **[設定]** セクションで、 **[サブネット]** を選択して **[サブネット]** ページを展開します。

3. **[サブネット]** ページで **[ゲートウェイ サブネット]** を選択して **[サブネットの追加]** ページを開きます。

   ![ゲートウェイ サブネットの追加](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "ゲートウェイ サブネットの追加")

4. サブネットの **[名前]** には、*GatewaySubnet* という値が自動的に入力されます。 この値は、Azure がゲートウェイ サブネットとしてこのサブネットを認識するために必要になります。 自動入力される **[アドレス範囲]** の値を実際の構成要件に合わせて調整し、 **[OK]** を選択してサブネットを作成します。

   ![サブネットの追加](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "サブネットの追加")
