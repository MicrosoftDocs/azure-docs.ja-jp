---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5642533fe1015e88c3b27e83139bfd26cb0b1a53
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444229"
---
1. [ポータル](http://portal.azure.com)で、仮想ネットワーク ゲートウェイを作成する Resource Manager 仮想ネットワークに移動します。
2. VNet のページの **[設定]** セクションで、**[サブネット]** をクリックして **[サブネット]** ページを展開します。
3. **[サブネット]** ページで **[+ゲートウェイ サブネット]** をクリックして、**[サブネットの追加]** ページを開きます。 

   ![ゲートウェイ サブネットの追加](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "ゲートウェイ サブネットの追加")
4. サブネットの **[名前]** には、"GatewaySubnet" という値が自動的に入力されます。 この値は、Azure がゲートウェイ サブネットとしてこのサブネットを認識するために必要になります。 自動入力される **[アドレス範囲]** の値は、実際の構成要件に合わせて調整してください。 ルート テーブルまたはサービス エンドポイントを構成しないでください。

   ![サブネットの追加](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "サブネットの追加")
5. サブネットを作成するには、ページ下部の **[OK]** をクリックします。
