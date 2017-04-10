---
title: "StorSimple Virtual Array の非アクティブ化と削除 | Microsoft Docs"
description: "StorSimple デバイスを非アクティブ化して削除することによって、サービスからこれを削除する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: bf5ddb32-da4b-446f-ab91-215e9020e1c8
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d2008fa396405de99c527a84831a6544cdc0fc1d
ms.openlocfilehash: d62b484150a6786f0a6abbff1ca2c172e1ad43db
ms.lasthandoff: 02/28/2017


---
# <a name="deactivate-and-delete-a-storsimple-virtual-array-via-storsimple-manager"></a>StorSimple Manager を介した StorSimple Virtual Array の非アクティブ化と削除
## <a name="overview"></a>概要
StorSimple Virtual Array を非アクティブ化する場合は、デバイスとこれに対応する StorSimple Manager サービスの接続を切断します。 非アクティブ化は永続的な操作であるため、元に戻すことはできません。 非アクティブ化されたデバイスを StorSimple Manager サービスに再び登録することはできません。

次のシナリオでは、StorSimple 仮想デバイスを非アクティブ化してから削除することが必要になる場合があります。

* デバイスがオンラインの状態で、このデバイスをフェールオーバーします。 より大きなデバイスへのアップグレードを行う場合、フェールオーバーが必要になることがあります。 デバイスのデータを転送し、フェール オーバーが完了したら、デバイスを削除できます。
* デバイスがオフラインの状態で、このデバイスをフェールオーバーします。 このシナリオが考えられるのは、災害発生時にデータ センター内の障害によってプライマリ デバイスがダウンした場合です。 セカンダリ デバイスにデバイスをフェールオーバーします。 デバイスのデータを転送し、フェール オーバーが完了したら、デバイスを削除できます。
* デバイスの使用を停止してからデバイスを削除します。 

デバイスを非アクティブ化すると、ローカルに保存されていたすべてのデータにアクセスできなくなります。 クラウドに格納されているデータのみを復元できます。 非アクティブ化した後もデバイスのデータを保持する場合は、デバイスを非アクティブ化する前にすべてのデータのクラウド スナップショットを取得する必要があります。 これを行うことにより、後ですべてのデータを回復できます。

このチュートリアルでは、次の方法について説明します。

* デバイスの非アクティブ化 
* 非アクティブ化されたデバイスの削除

## <a name="deactivate-a-device"></a>デバイスの非アクティブ化
デバイスを非アクティブ化するには、次の手順を実行します。

#### <a name="to-deactivate-the-device"></a>デバイスを非アクティブ化するには
1. **[デバイス]** ページを開きます。 非アクティブ化するデバイスを選択します。
   
    ![非アクティブ化するデバイスの選択](./media/storsimple-ova-deactivate-and-delete-device/deactivate1m.png)
2. ページの下部にある **[非アクティブ化]**をクリックします。
   
    ![[非アクティブ化] をクリック](./media/storsimple-ova-deactivate-and-delete-device/deactivate2m.png)
3. 確認メッセージが表示されます。 **[はい]** をクリックして続行します。 
   
    ![非アクティブ化の確定](./media/storsimple-ova-deactivate-and-delete-device/deactivate3m.png)
   
    非アクティブ化のプロセスが開始され、完了するまでに数分かかります。
   
    ![非アクティブ化中](./media/storsimple-ova-deactivate-and-delete-device/deactivate4m.png)
4. 非アクティブ化されると、デバイスの一覧が更新されます。 
   
    ![非アクティブ化の完了](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)
   
    これで、このデバイスを削除することができます。 

## <a name="delete-the-device"></a>デバイスの削除
デバイスは、非アクティブ化してからでないと削除できません。 デバイスを削除すると、サービスに接続されているデバイスの一覧からそのデバイスが削除されます。 サービスは、削除されたデバイスをもはや管理できなくなります。 ただし、デバイスに関連付けられているデータはクラウドに残ります。 このデータには料金が発生するので注意してください。 

次のようにしてデバイスを削除します。

#### <a name="to-delete-the-device"></a>デバイスを削除するには
1. StorSimple Manager サービスの **[デバイス** ] ページで、削除する非アクティブ化済みデバイスを選択します。
   
   ![削除するデバイスの選択](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)
2. ページの下部にある **[削除]**をクリックします。
   
   ![[削除] をクリック](./media/storsimple-ova-deactivate-and-delete-device/deactivate6m.png)
3. 確認を求められます。 デバイス名を入力して、デバイスの削除を確定します。 デバイスを削除しても、デバイスに関連付けられているクラウド データは削除されないので注意してください。 チェック マーク アイコンをクリックして、操作を続行します。
   
   ![削除の確定](./media/storsimple-ova-deactivate-and-delete-device/deactivate7m.png) 
4. デバイスが削除されるまで数分かかる場合があります。 
   
   ![削除中](./media/storsimple-ova-deactivate-and-delete-device/deactivate8m.png)
   
    デバイスを削除すると、デバイスの一覧が更新されます。
   
   ![削除の完了](./media/storsimple-ova-deactivate-and-delete-device/deactivate9m.png)

## <a name="next-steps"></a>次のステップ
* StorSimple Manager サービスを使用する方法については、「 [StorSimple Manager サービスを使用した StorSimple Virtual Array の管理](storsimple-ova-manager-service-administration.md)」を参照してください。 


