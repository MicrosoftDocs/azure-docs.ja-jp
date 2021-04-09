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
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2b52ad50854092cddd7b9e79cbeebd4a83017081
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325414"
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
   * **名前**  
     容量プールの名前を指定します。  
     容量プールの名前は、NetApp アカウントごとに一意であることが必要です。

   * **サービス レベル**   
     このフィールドには、容量プールのターゲット パフォーマンスが表示されます。  
     容量プールのサービス レベルを指定します [**Ultra**](azure-netapp-files-service-levels.md#Ultra)、[**Premium**](azure-netapp-files-service-levels.md#Premium)、または [**Standard**](azure-netapp-files-service-levels.md#Standard)。

    * **サイズ**     
     購入する容量プールのサイズを指定します。        
     容量プールの最小サイズは 4 TiB です。 プールは、4 TiB の倍数のサイズで作成することができます。   

   * **QoS**   
     容量プールで **[手動]** と **[自動]** のどちらの種類の QoS を使用するかを指定します。  

     QoS の種類の詳細については、[ストレージの階層](azure-netapp-files-understand-storage-hierarchy.md)および[パフォーマンスに関する考慮事項](azure-netapp-files-performance-considerations.md)に関する記事を参照してください。  

     > [!IMPORTANT] 
     > **QoS の種類** は、永続的に **[手動]** に設定されています。 自動 QoS を使用するように手動 QoS 容量プールを変換することはできません。 ただし、手動 QoS を使用するように自動 QoS 容量プールを変換することはできます。 [手動 QoS の使用を目的とした容量プールの変更](manage-manual-qos-capacity-pool.md#change-to-qos)に関する記事を参照してください。   
     > 容量プールに手動の種類の QoS を使用するには、登録が必要です。 「[手動 QoS 容量プールを管理する](manage-manual-qos-capacity-pool.md#register-the-feature)」を参照してください。 

    ![新しい容量プール](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. **Create** をクリックしてください。

## <a name="next-steps"></a>次のステップ 

- [ストレージの階層](azure-netapp-files-understand-storage-hierarchy.md) 
- [Azure NetApp Files のサービス レベル](azure-netapp-files-service-levels.md)
- [Azure NetApp Files 価格ページ](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [手動 QoS 容量プールを管理する](manage-manual-qos-capacity-pool.md)
- [サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)
