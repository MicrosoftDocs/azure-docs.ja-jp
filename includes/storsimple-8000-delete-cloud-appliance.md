---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ac708eb2ac79a74b8f4e09a7306a42665b3aca94
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736003"
---
#### <a name="to-delete-a-cloud-appliance"></a>クラウド アプライアンスを削除するには

1. Azure ポータルにサインインします。
2. 削除できるのは、データが格納されていない非アクティブなデバイスだけです。 最初にデバイス上のデータを削除するか、別のデバイスにボリューム コンテナー内の[データをフェールオーバー](../articles/storsimple/storsimple-8000-device-failover-cloud-appliance.md)してください。 データを削除したら、いつでもデバイスを非アクティブ化することができます。
3. StorSimple デバイス マネージャー サービス ページで **[デバイス]** をクリックし、目的のデバイスを選択します。 マウスの右ボタンをクリックして **[非アクティブ化]** を選択します。
4. デバイスが非アクティブ化されたら、そのデバイスを右クリックして **[削除]** を選択します。

    ![非アクティブ化したデバイスを選択して [削除] をクリック](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance1.png)

5. デバイス名を入力して削除を確定します。 デバイスが削除されると、デバイス一覧が更新されます。

    ![削除の確定](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance2.png)

6. デバイスが削除されると、通知が表示されます。

    ![デバイスが正常に削除されたことの通知](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance4.png)

7. デバイスの一覧が更新され、デバイスが削除されたことがわかります。

    ![更新されたデバイス一覧](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance5.png)
