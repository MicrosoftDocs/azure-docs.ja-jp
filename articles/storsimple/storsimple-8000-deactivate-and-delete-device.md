---
title: "StorSimple 8000 シリーズ デバイスを非アクティブ化して削除する | Microsoft Docs"
description: "StorSimple デバイスを非アクティブ化して削除することによって、サービスからこれを削除する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 3c00867a29cf8343a57e74e2aabe3971ae6837af
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2017
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>StorSimple デバイスの非アクティブ化と削除

## <a name="overview"></a>概要

この記事では、StorSimple デバイス マネージャー サービスに接続されている StorSimple デバイスを非アクティブ化して削除する方法について説明します。 この記事のガイダンスは、StorSimple Cloud Appliance を含む StorSimple 8000 シリーズ デバイスにのみ適用されます。 StorSimple Virtual Array を使用している場合は、「[StorSimple Virtual Array の非アクティブ化と削除](storsimple-virtual-array-deactivate-and-delete-device.md)」に移動してください。

非アクティブ化すると、デバイスとこれに対応する StorSimple デバイス マネージャー サービスの接続が切断されます。 StorSimple デバイスをサービスから切り離したいことがあります (デバイスを交換またはアップグレードする場合や、StorSimple が不要になった場合など)。 その場合は、削除する前に、デバイスを非アクティブ化する必要があります。

デバイスを非アクティブ化すると、デバイスにローカルに保存されていたすべてのデータにアクセスできなくなります。 復元できるのは、クラウドに保存されていた、デバイスに関連付けられているデータだけです。

> [!WARNING]
> 非アクティブ化は永続的な操作であるため、元に戻すことはできません。 出荷時の既定の設定にリセットしない限り、非アクティブ化されたデバイスを StorSimple デバイス マネージャー サービスに登録することはできません。
>
> 出荷時の設定にリセットするプロセスにより、お使いのデバイスにローカルに保存されているすべてのデータが削除されます。 したがって、デバイスを非アクティブ化する前に、すべてのデータのクラウド スナップショットを作成する必要があります。 このクラウド スナップショットを使用すると、すべてのデータを後で回復できます。

このチュートリアルを読むと、次のことができるようになります。

* デバイスを非アクティブ化してデータを削除する。
* デバイスを非アクティブ化してデータを保持する。

> [!NOTE]
> StorSimple 物理デバイスまたはクラウド アプライアンスを非アクティブ化する前に、デバイスに依存しているクライアントとホストを停止するか削除してください。


## <a name="deactivate-and-delete-data"></a>データの非アクティブ化と削除

デバイスを完全に削除し、デバイスのデータを保持しない場合は、以下の手順を実行します。

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>デバイスを非アクティブ化してデータを削除するには

1. デバイスを非アクティブ化する前に、デバイスに関連付けられているすべてのボリューム コンテナー (およびボリューム) を削除する必要があります。 ボリューム コンテナーを削除できるのは、関連付けられているバックアップを削除した後だけです。

    > [!NOTE]
    > StorSimple 物理デバイスまたはクラウド アプライアンスを非アクティブ化する前に、デバイスから削除されたボリューム コンテナーからのデータが実際に削除されることを確認します。 クラウドの使用量グラフを監視することができ、削除したバックアップのためドロップ クラウド使用率を確認するときに進むことができます、デバイスを非アクティブ化します。 デバイスを非アクティブ化と、このドロップが発生する前に、データは、ストレージ アカウントに孤立したし、料金を計上します。

2. 次のようにデバイスを非アクティブ化します。
   
   1. StorSimple デバイス マネージャー サービスに移動し、**[デバイス]** をクリックします。 **[デバイス]** ブレードで、非アクティブ化するデバイスを選択します。右クリックして **[非アクティブ化]** をクリックします。

        ![StorSimple デバイスを非アクティブ化する](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. **[非アクティブ化]** ブレードで、デバイス名を入力して確認した後、**[非アクティブ化]** をクリックします。 非アクティブ化のプロセスが開始されます。この処理は数分かかります。

        ![StorSimple デバイスを非アクティブ化する](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. 非アクティブ化を行った後は、デバイスを完全に削除できます。 デバイスを削除すると、サービスに接続されているデバイスの一覧からそのデバイスが削除されます。 サービスは、削除されたデバイスをもはや管理できなくなります。 次のようにしてデバイスを削除します。
   
   1. StorSimple デバイス マネージャー サービスに移動し、**[デバイス]** をクリックします。 **[デバイス]** ブレードで、削除する非アクティブ化されたデバイスを選択します。右クリックして **[削除]** をクリックします。

        ![StorSimple デバイスを非アクティブ化する](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. **[削除]** ブレードで、デバイス名を入力して確認した後、**[削除]** をクリックします。 削除は、完了するまで数分かかります。

        ![StorSimple デバイスを非アクティブ化する](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. 削除が正常に完了すると、通知が表示されます。 デバイスの一覧も、削除を反映するように更新されます。

## <a name="deactivate-and-retain-data"></a>データの非アクティブ化と保持

デバイスを削除するだけで、データは保持する場合は、以下の手順を実行します。

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>デバイスを非アクティブ化してデータを保持するには
1. デバイスを非アクティブ化します。 すべてのボリューム コンテナーとデバイスのスナップショットが残ります。
   
   1. StorSimple デバイス マネージャー サービスに移動し、**[デバイス]** をクリックします。 **[デバイス]** ブレードで、非アクティブ化するデバイスを選択します。右クリックして **[非アクティブ化]** をクリックします。

         ![StorSimple デバイスを非アクティブ化する](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. **[非アクティブ化]** ブレードで、デバイス名を入力して確認した後、**[非アクティブ化]** をクリックします。 非アクティブ化のプロセスが開始されます。この処理は数分かかります。

         ![StorSimple デバイスを非アクティブ化する](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. これで、ボリューム コンテナーと、関連付けられているスナップショットをフェールオーバーできます。 手順については、「 [StorSimple デバイスのフェールオーバーと障害復旧](storsimple-8000-device-failover-disaster-recovery.md)」を参照してください。
3. 非アクティブ化とフェールオーバーを行った後、デバイスを完全に削除できます。 デバイスを削除すると、サービスに接続されているデバイスの一覧からそのデバイスが削除されます。 サービスは、削除されたデバイスをもはや管理できなくなります。 デバイスを削除するには、次の手順を完了します。
   
   1. StorSimple デバイス マネージャー サービスに移動し、**[デバイス]** をクリックします。 **[デバイス]** ブレードで、削除する非アクティブ化されたデバイスを選択します。右クリックして **[削除]** をクリックします。

       ![StorSimple デバイスを非アクティブ化する](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. **[削除]** ブレードで、デバイス名を入力して確認した後、**[削除]** をクリックします。 削除は、完了するまで数分かかります。

       ![StorSimple デバイスを非アクティブ化する](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. 削除が正常に完了すると、通知が表示されます。 デバイスの一覧も、削除を反映するように更新されます。

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>クラウド アプライアンスを非アクティブ化して削除する

StorSimple Cloud Appliance の場合、ポータルから非アクティブ化すると、仮想マシンとプロビジョニング時に作成されたリソースの割り当てが解除されて削除されます。 非アクティブ化したクラウド アプライアンスを以前の状態に復元することはできません。

![StorSimple Cloud Appliance を非アクティブ化する](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

非アクティブ化すると次のアクションが実行されます。

* StorSimple Cloud Appliance がサービスから削除されます。
* StorSimple Cloud Appliance 用の仮想マシンが削除されます。
* StorSimple Cloud Appliance 用に作成された OS ディスクとデータ ディスクが削除されます。
* プロビジョニング中に作成されたホステッド サービスと Virtual Network は保持されます。 これらのエンティティを使用していない場合は、手動で削除する必要があります。
* StorSimple Cloud Appliance によって作成されたクラウド スナップショットは保持されます。

クラウド アプライアンスが非アクティブ化された後、デバイスの一覧から削除できます。 非アクティブ化されたデバイスを選択します。右クリックして **[削除]** をクリックします。 デバイスが削除されると StorSimple による通知が表示され、デバイスの一覧が更新されます。

## <a name="next-steps"></a>次のステップ

* 非アクティブ化されたデバイスを出荷時の既定値に復元するには、「 [デバイスの出荷時設定へのリセット](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)」をご覧ください。
* 技術的な詳細については、 [Microsoft サポートに問い合わせ](storsimple-8000-contact-microsoft-support.md)てください。
* StorSimple デバイス マネージャー サービスを使用する方法の詳細については、「[Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md)」(StorSimple デバイス マネージャーを使用して StorSimple デバイスを管理する) を参照してください。

