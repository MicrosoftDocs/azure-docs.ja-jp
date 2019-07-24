---
title: Azure NetApp Files の容量プールを設定する | Microsoft Docs
description: ボリュームの作成先となる容量プールを設定する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 8f50b2ad34c705c8d3831d8243f136c41d750dc0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58074439"
---
# <a name="set-up-a-capacity-pool"></a>容量プールを設定する

容量プールを設定すると、そこにボリュームを作成できるようになります。  

## <a name="before-you-begin"></a>開始する前に 

あらかじめ NetApp アカウントを作成しておく必要があります。   

[NetApp アカウントを作成する](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>手順 

1. NetApp アカウントの管理ブレードに移動し、ナビゲーション ウィンドウで **[容量プール]** をクリックします。  
    
    ![容量プールに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. **[+ プールの追加]** をクリックして新しい容量プールを作成します。   
    [New Capacity Pool]\(新しい容量プール\) ウィンドウが表示されます。

3. 新しい容量プールに関して、次の情報を入力します。  
   * **Name**  
     容量プールの名前を指定します。  
     容量プールの名前は、NetApp アカウントごとに一意であることが必要です。

   * **サービス レベル**   
     このフィールドには、容量プールのターゲット パフォーマンスが表示されます。  
     容量プールのサービス レベルを指定します ([**Premium**](azure-netapp-files-service-levels.md#Premium) または [**Standard**](azure-netapp-files-service-levels.md#Standard))。

   * **サイズ**     
     購入する容量プールのサイズを指定します。        
     容量プールの最小サイズは 4 TiB です。 プールは、4 TiB の倍数のサイズで作成することができます。   
      
     ![新しい容量プール](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Click **OK**.

## <a name="next-steps"></a>次の手順 

- [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
- さまざまなサービス レベルの価格については、[Azure NetApp Files の価格ページ](https://azure.microsoft.com/pricing/details/storage/netapp/)を参照してください
- [サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)
