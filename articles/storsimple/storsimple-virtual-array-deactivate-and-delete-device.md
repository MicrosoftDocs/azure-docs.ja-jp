---
title: Microsoft Azure StorSimple Virtual Array を非アクティブ化および削除する | Microsoft Docs
description: StorSimple デバイスを非アクティブ化して削除することによって、サービスからこれを削除する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 84fd1a2d3d0dc4ce6960469ea3212b8ca1d4d07c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023025"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>StorSimple Virtual Array の非アクティブ化と削除

## <a name="overview"></a>概要

StorSimple Virtual Array を非アクティブ化する場合は、デバイスとそれに対応する StorSimple デバイス マネージャー サービスの接続を切断します。 このチュートリアルでは、次の方法について説明します。

* デバイスの非アクティブ化 
* 非アクティブ化されたデバイスの削除

この記事の情報は、StorSimple Virtual Array にのみ適用されます。 8000 シリーズについては、[デバイスを非アクティブ化または削除](./storsimple-8000-deactivate-and-delete-device.md)する方法に関するページをご覧ください。

## <a name="when-to-deactivate"></a>非アクティブ化するタイミング

非アクティブ化は永続的な操作であるため、元に戻すことはできません。 非アクティブ化されたデバイスを、StorSimple デバイス マネージャー サービスに再登録することはできません。 次のシナリオでは、StorSimple Virtual Array の非アクティブ化と削除が必要になる可能性があります。

* **計画されたフェールオーバー**: デバイスがオンラインの状態で、このデバイスをフェールオーバーします。 より大きなデバイスにアップグレードする場合、そのデバイスのフェールオーバーが必要になる可能性があります。 データの所有権が転送され、フェールオーバーが完了したら、ソース デバイスは自動的に削除されます。
* **計画されていないフェールオーバー**: デバイスがオフラインの状態で、そのデバイスをフェールオーバーする必要があります。 このシナリオは、データセンターで障害が発生し、プライマリ デバイスがダウンしている場合、災害時に発生する可能性があります。 セカンダリ デバイスにデバイスをフェールオーバーします。 データの所有権が転送され、フェールオーバーが完了したら、ソース デバイスは自動的に削除されます。
* **停止**: デバイスの使用を停止する必要があります。 それには、デバイスを非アクティブ化してから削除します。 デバイスを非アクティブ化すると、ローカルに保存されているデータにはアクセスできなくなります。 アクセスして回復できるのは、クラウドに保存されているデータだけです。 非アクティブ化した後もデバイスのデータを保持する場合は、デバイスを非アクティブ化する前にすべてのデータのクラウド スナップショットを取得する必要があります。 このクラウド スナップショットを使用すると、すべてのデータを後で回復できます。

## <a name="deactivate-a-device"></a>デバイスの非アクティブ化

デバイスを非アクティブ化するには、次の手順を実行します。

#### <a name="to-deactivate-the-device"></a>デバイスを非アクティブ化するには

1. サービスで、**[管理] > [デバイス]** の順に移動します。 **[デバイス]** ブレードで、非アクティブ化するデバイスをクリックして選択します。
   
    ![非アクティブ化するデバイスの選択](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. **[Device dashboard (デバイス ダッシュボード)]** ブレードで **[詳細]** をクリックし、一覧から **[非アクティブ化]** を選択します。
   
    ![[非アクティブ化] をクリック](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. **[非アクティブ化]** ブレードでデバイス名を入力し、**[非アクティブ化]** をクリックします。 
   
    ![非アクティブ化の確定](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    非アクティブ化のプロセスが開始されます。この処理は数分かかります。
   
    ![非アクティブ化中](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. 非アクティブ化すると、デバイスの一覧が更新されます。
   
    ![非アクティブ化の完了](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    これで、このデバイスを削除することができます。

## <a name="delete-the-device"></a>デバイスの削除

デバイスを削除するには、最初に非アクティブ化する必要があります。 デバイスを削除すると、サービスに接続されているデバイスの一覧からそのデバイスが削除されます。 サービスは、削除されたデバイスをもはや管理できなくなります。 ただし、デバイスに関連付けられているデータはクラウドに残ります。 このデータは課金されます。

デバイスを削除するには、次の手順を実行します。

#### <a name="to-delete-the-device"></a>デバイスを削除するには

1. StorSimple デバイス マネージャーで、**[管理] > [デバイス]** の順に移動します。 **[デバイス]** ブレードで、削除する非アクティブ化されたデバイスを選択します。
2. **[Device dashboard (デバイス ダッシュボード)]** ブレードで、**[詳細]**、**[削除]** の順にクリックします。
   
   ![削除するデバイスの選択](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. **[削除]** ブレードで、削除を確定するデバイスの名前を入力し、**[削除]** をクリックします。 デバイスを削除しても、デバイスに関連付けられているクラウド データは削除されません。 
   
   ![削除の確定](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. 削除が開始されます。この処理は数分かかります。
   
   ![削除中](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    デバイスを削除したら、更新されたデバイスの一覧を表示できます。

## <a name="next-steps"></a>次のステップ

* フェールオーバーの方法については、[StorSimple Virtual Array のフェールオーバーとディザスター リカバリー](storsimple-virtual-array-failover-dr.md)に関するページをご覧ください。

* StorSimple デバイス マネージャー サービスを使用する方法の詳細については、[StorSimple デバイス マネージャー サービスを使用した StorSimple Virtual Array の管理](storsimple-virtual-array-manager-service-administration.md)に関するページをご覧ください。