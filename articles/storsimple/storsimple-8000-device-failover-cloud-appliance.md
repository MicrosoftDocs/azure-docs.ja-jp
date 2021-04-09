---
title: StorSimple クラウド アプライアンスへのフェールオーバーとディザスター リカバリー
description: クラウド アプライアンスに StorSimple 8000 シリーズ物理デバイスをフェールオーバーする方法を説明します。
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 18b34ff466f3935cb5cd18d46e6d26e36e756a9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91774462"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>StorSimple Cloud Appliance にフェールオーバーする

## <a name="overview"></a>概要

このチュートリアルでは、災害発生時に、StorSimple 8000 シリーズ物理デバイスを StorSimple Cloud Appliance にフェールオーバーするために必要な手順について説明します。 StorSimple は、デバイス フェール オーバー機能を使用して、データセンター内のソース物理デバイスから Azure で実行されているクラウド アプライアンスにデータを移行します。 このチュートリアルのガイダンスは、StorSimple 8000 シリーズ物理デバイスやソフトウェア バージョン Update 3 以降を実行しているクラウド アプライアンスに適用されます。

デバイスのフェールオーバーと災害から復旧するための使用方法の詳細については、「[Failover and disaster recovery for StorSimple 8000 series devices](storsimple-8000-device-failover-disaster-recovery.md)」(StorSimple 8000 シリーズ デバイスのフェールオーバーとディザスター リカバリー) を参照してください。

StorSimple 物理デバイスを別の物理デバイスにフェールオーバーするには、「[Fail over to a StorSimple physical device](storsimple-8000-device-failover-physical-device.md)」(StorSimple 物理デバイスにフェールオーバーする) を参照してください。 デバイスをそれ自体にフェールオーバーするには、「[Fail over to the same StorSimple physical device](storsimple-8000-device-failover-same-device.md)」(同じ StorSimple 物理デバイスにフェールオーバーする) を参照してください。

## <a name="prerequisites"></a>前提条件

- デバイスのフェールオーバーに関する考慮事項を必ず見直してください。 詳細については、「[Common considerations for device failover](storsimple-8000-device-failover-disaster-recovery.md)」(デバイスのフェールオーバーの一般的な考慮事項) を参照してください。

- この手順を実行する前に、StorSimple Cloud Appliance を作成して構成する必要があります。 ソフトウェア バージョン Update 3 以降を実行している場合は、DR 用の 8020 クラウド アプライアンスの使用を検討してください。 8020 モデルは、64 TB を備え、Premium Storage を使用します。 詳細については、「[StorSimple Cloud Appliance のデプロイと管理](storsimple-8000-cloud-appliance-u2.md)」を参照してください。

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>クラウド アプライアンスへのフェールオーバー手順

ターゲットの StorSimple Cloud Appliance にデバイスを復元するには、次の手順を実行します。

1.  フェールオーバーするボリューム コンテナーにクラウド スナップショットが関連付けられていることを確認します。 詳細については、「[Use StorSimple Device Manager service to create backups](storsimple-8000-manage-backup-policies-u2.md)」(StorSimple デバイス マネージャー サービスを使用してバックアップを作成する) を参照してください。
2. StorSimple デバイス マネージャー サービスに移動し、 **[デバイス]** をクリックします。 **[デバイス]** ブレードで、サービスに接続されているデバイスの一覧に移動します。
    ![デバイスの選択](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. ソース デバイスを選択し、クリックします。 ソース デバイスには、フェールオーバーするボリューム コンテナーがあります。 **[設定]、[ボリューム コンテナー]** の順に移動します。

    ![デバイス 2 を選択する](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. 別のデバイスにフェールオーバーするボリューム コンテナーを選択します。 ボリューム コンテナーをクリックし、このコンテナー内のボリュームの一覧を表示します。 ボリュームを選択して右クリックし、 **[オフラインにする]** をクリックしてボリュームをオフラインにします。

    ![デバイスの選択 3](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. ボリューム コンテナーのすべてのボリュームでこのプロセスを繰り返します。

     ![デバイスの選択 4](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. 別のデバイスにフェールオーバーするすべてのボリューム コンテナーで前の手順を繰り返します。

7. **[デバイス]** ブレードに戻ります。 コマンド バーで **[フェールオーバー]** をクリックします。

    ![[フェールオーバー] をクリック](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. **[フェールオーバー]** ブレードで、次の手順を実行します。
   
    1. **[ソース]** をクリックします。 フェールオーバーするボリューム コンテナーを選択します。 **関連付けられたクラウド スナップショットとオフラインのボリュームを含むボリューム コンテナーのみが表示されます。**
        ![ソースの選択](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. **[ターゲット]** をクリックします。 使用可能なデバイスの一覧で、ターゲットのクラウド アプライアンスを選択します。 **ソース ボリューム コンテナーを格納できるだけの十分な容量があるデバイスのみが一覧に表示されます。**

        ![ターゲット デバイスの選択](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. **[概要]** でフェールオーバーの設定を確認し、選択されたボリューム コンテナーのボリュームがオフラインであることを示すチェック ボックスをオンにします。 

        ![フェールオーバー設定を確認する](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. フェールオーバー ジョブが作成されます。 フェールオーバー ジョブを監視するには、ジョブ通知をクリックします。

    ![フェールオーバー ジョブを監視する](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. フェールオーバーが完了したら、 **[デバイス]** ブレードに戻ります。

    1. フェールオーバーのターゲットとして使用されたデバイスを選択します。

       ![デバイスの選択 5](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. **[ボリューム コンテナー]** をクリックします。 すべてのボリューム コンテナーと古いデバイスのボリュームの一覧が表示されます。

       フェールオーバーするボリューム コンテナーにローカルで固定されたボリュームがある場合は、それらのボリュームは階層化ボリュームとしてフェールオーバーされます。 ローカルで固定されたボリュームは、StorSimple Cloud Appliance ではサポートされていません。

       ![ターゲットのボリューム コンテナーを表示する](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>次のステップ

* フェールオーバーの実行後、必要に応じて [StorSimple デバイスを非アクティブ化または削除](storsimple-8000-deactivate-and-delete-device.md)します。

* StorSimple デバイス マネージャー サービスを使用する方法の詳細については、「[Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md)」(StorSimple デバイス マネージャーを使用して StorSimple デバイスを管理する) を参照してください。

