---
title: ExpressRoute の承認キーを要求する
description: ExpressRoute の承認キーを要求する手順。
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 54a610c8b0f3f3fe9d3ebe39291bba7767007839
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491846"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Azure portal の **[接続性]** セクションの下にある **[ExpressRoute]** タブで **[+ Request an authorization key]\(承認キーの要求\)** を選択します。 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="ExpressRoute の承認キーを要求する方法を示すスクリーンショット。" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. これに名前を指定し、 **[作成]** を選択します。 
      
   キーの作成には約 30 秒かかることがあります。 作成された新しいキーが、プライベート クラウドの承認キーの一覧に表示されます。

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="ExpressRoute Global Reach の承認キーを示すスクリーンショット。":::
  
1. 承認キーと ExpressRoute ID を書き留めておきます。 これらは、ピアリングを行う際に使用します。  

   > [!NOTE]
   > 承認キーはしばらくすると消えるため、表示されたらすぐにコピーしてください。