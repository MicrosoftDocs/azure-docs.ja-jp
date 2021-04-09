---
title: チュートリアル - StorSimple 物理デバイスを同じデバイスにフェールオーバーする
description: 災害時に、StorSimple 8000 シリーズ物理デバイスをそのデバイス自体にフェールオーバーするために必要な手順について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: 17c116194aa52a82246bcee9114824e8a918ebbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017102"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>StorSimple 物理デバイスを同じデバイスにフェールオーバーする

## <a name="overview"></a>概要

このチュートリアルでは、災害発生時に、StorSimple 8000 シリーズ物理デバイスをそのデバイス自体にフェールオーバーするために必要な手順について説明します。 StorSimple は、デバイス フェールオーバー機能を使用して、データセンター内のソース物理デバイスから別の物理デバイスにデータを移行します。 このチュートリアルのガイダンスは、ソフトウェア バージョン Update 3 以降を実行している StorSimple 8000 シリーズ物理デバイスに適用されます。

デバイスのフェールオーバーと災害から復旧するための使用方法の詳細については、「[Failover and disaster recovery for StorSimple 8000 series devices](storsimple-8000-device-failover-disaster-recovery.md)」(StorSimple 8000 シリーズ デバイスのフェールオーバーとディザスター リカバリー) を参照してください。

物理デバイスを別の物理デバイスにフェールオーバーするには、「[Fail over to a StorSimple 8000 series physical device](storsimple-8000-device-failover-physical-device.md)」(StorSimple 物理デバイスにフェールオーバーする) を参照してください。 StorSimple 物理デバイスを StorSimple Cloud Appliance にフェールオーバーするには、「[Fail over to a StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)」(StorSimple Cloud Appliance にフェールオーバーする) を参照してください。


## <a name="prerequisites"></a>前提条件

- デバイスのフェールオーバーに関する考慮事項を必ず見直してください。 詳細については、「[Common considerations for device failover](storsimple-8000-device-failover-disaster-recovery.md)」(デバイスのフェールオーバーの一般的な考慮事項) を参照してください。


## <a name="steps-to-fail-over-to-the-same-device"></a>同じデバイスにフェールオーバーするための手順

同じデバイスにフェールオーバーする必要がある場合は、次の手順を実行します。

1. デバイスにあるすべてのボリュームのクラウド スナップショットを取得します。 詳細については、「[Use StorSimple Device Manager service to create backups](storsimple-8000-manage-backup-policies-u2.md)」(StorSimple デバイス マネージャー サービスを使用してバックアップを作成する) を参照してください。
2. デバイスを工場出荷時の既定値にリセットします。 [StorSimple デバイスを工場出荷時の既定の設定にリセットする方法](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)に関するページの詳細な指示に従います。
3. StorSimple デバイス マネージャー サービスに移動し、 **[デバイス]** を選択します。 **[デバイス]** ブレードでは、古いデバイスは **[オフライン]** と表示されます。

    ![ソース デバイスはオフライン](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. StorSimple デバイス マネージャー サービスで再度デバイスを構成して登録します。 新しく登録されたデバイスには、 **[セットアップの準備が完了しました]** と表示されます。 新しいデバイスのデバイス名は古いデバイスと同じですが、デバイスが出荷時の既定値にリセットされ、再登録されたことを示すために、数字が追加されます。

    ![新しく登録され、セットアップの準備が完了したデバイス](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. 新しいデバイスで、デバイスのセットアップを完了します。 詳細については、「[手順 4.デバイスの最小セットアップを完了する](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup)」を参照してください。 **[デバイス]** ブレードで、デバイスの状態が **[オンライン]** に変わります。

   > [!IMPORTANT]
   > **最低限の構成を先に完了してください。そうしないと、DR が失敗する可能性があります。**

    ![新しく登録されたデバイスがオンライン](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. 古いデバイス (状態はオフライン) を選択し、コマンド バーの **[フェールオーバー]** をクリックします。 **[フェールオーバー]** ブレードで、ソース デバイスとして古いデバイスを選択し、ターゲット デバイスとして新しく登録したデバイスを指定します。

    ![フェールオーバーの概要](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    詳細な手順については、「別の物理デバイスにフェールオーバーする」を参照してください。

7. **[ジョブ]** ブレードから監視できるデバイスの復元ジョブが作成されます。

8. ジョブが正常に完了したら、新しいデバイスにアクセスし、 **[ボリューム コンテナー]** ブレードに移動します。 すべてのボリューム コンテナーが古いデバイスから新しいデバイスに移行されていることを確認します。

   ![ボリューム コンテナーが移行された](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. フェールオーバーが完了したら、古いデバイスを非アクティブ化してポータルから削除できます。 古いデバイス (オフライン) を選択します。右クリックして **[非アクティブ化]** を選択します。 デバイスが非アクティブ化されると、デバイスの状態が更新されます。

     ![ソース デバイスが非アクティブ化された](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. 非アクティブ化されたデバイスを選択します。右クリックして **[削除]** を選択します。 これで、デバイスがデバイスの一覧から削除されます。

    ![ソース デバイスが削除された](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>次のステップ

* フェールオーバーの実行後、必要に応じて [StorSimple デバイスを非アクティブ化または削除](storsimple-8000-deactivate-and-delete-device.md)します。
* StorSimple デバイス マネージャー サービスを使用する方法の詳細については、「[Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md)」(StorSimple デバイス マネージャーを使用して StorSimple デバイスを管理する) を参照してください。

