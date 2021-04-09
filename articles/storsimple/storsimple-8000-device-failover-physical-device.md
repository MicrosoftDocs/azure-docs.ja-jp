---
title: 別の StorSimple 8000 デバイスへのフェールオーバー、ディザスター リカバリー
description: StorSimple 8000 シリーズ物理デバイスを別の物理デバイスにフェールオーバーする方法を説明します。
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 67d6fb1f3cc359288ed942d915e186542a62b0fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017136"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>StorSimple 8000 シリーズ物理デバイスにフェールオーバーする

## <a name="overview"></a>概要

このチュートリアルでは、災害発生時に、StorSimple 8000 シリーズ物理デバイスを別の StorSimple 物理デバイスにフェールオーバーするために必要な手順について説明します。 StorSimple は、デバイス フェールオーバー機能を使用して、データセンター内のソース物理デバイスから別の物理デバイスにデータを移行します。 このチュートリアルのガイダンスは、ソフトウェア バージョン Update 3 以降を実行している StorSimple 8000 シリーズ物理デバイスに適用されます。

デバイスのフェールオーバーと災害から復旧するための使用方法の詳細については、「[Failover and disaster recovery for StorSimple 8000 series devices](storsimple-8000-device-failover-disaster-recovery.md)」(StorSimple 8000 シリーズ デバイスのフェールオーバーとディザスター リカバリー) を参照してください。

StorSimple 物理デバイスを StorSimple Cloud Appliance にフェールオーバーするには、「[Fail over to a StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)」(StorSimple Cloud Appliance にフェールオーバーする) を参照してください。 物理デバイスをそれ自体にフェールオーバーするには、「[Fail over to the same StorSimple physical device](storsimple-8000-device-failover-same-device.md)」(同じ StorSimple 物理デバイスにフェールオーバーする) を参照してください。


## <a name="prerequisites"></a>前提条件

- デバイスのフェールオーバーに関する考慮事項を必ず見直してください。 詳細については、「[Common considerations for device failover](storsimple-8000-device-failover-disaster-recovery.md)」(デバイスのフェールオーバーの一般的な考慮事項) を参照してください。

- StorSimple 8000 シリーズ物理デバイスがデータ センターにデプロイされている必要があります。 デバイスは、Update 3 以降のソフトウェアのバージョンを実行している必要があります。 詳細については、「 [オンプレミスの StorSimple デバイスのデプロイ](storsimple-8000-deployment-walkthrough-u2.md)」をご覧ください。


## <a name="steps-to-fail-over-to-a-physical-device"></a>別の物理デバイスにフェールオーバーする手順

ターゲットの物理デバイスにデバイスを復元するには、次の手順を実行します。

1. フェールオーバーするボリューム コンテナーにクラウド スナップショットが関連付けられていることを確認します。 詳細については、「[Use StorSimple Device Manager service to create backups](storsimple-8000-manage-backup-policies-u2.md)」(StorSimple デバイス マネージャー サービスを使用してバックアップを作成する) を参照してください。
2. StorSimple デバイス マネージャーに移動し、 **[デバイス]** をクリックします。 **[デバイス]** ブレードで、サービスに接続されているデバイスの一覧に移動します。
    ![デバイスの選択](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. ソース デバイスを選択し、クリックします。 ソース デバイスには、フェールオーバーするボリューム コンテナーがあります。 **[設定]、[ボリューム コンテナー]** の順に移動します。
4. 別のデバイスにフェールオーバーするボリューム コンテナーを選択します。 ボリューム コンテナーをクリックし、このコンテナー内のボリュームの一覧を表示します。 ボリュームを選択して右クリックし、 **[オフラインにする]** をクリックしてボリュームをオフラインにします。 ボリューム コンテナーのすべてのボリュームでこのプロセスを繰り返します。
5. 別のデバイスにフェールオーバーするすべてのボリューム コンテナーで前の手順を繰り返します。
6. **[デバイス]** ブレードに戻ります。 コマンド バーで **[フェールオーバー]** をクリックします。
    ![[フェールオーバー]](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png) をクリックします。
    
7. **[フェールオーバー]** ブレードで、次の手順を実行します。
   
   1. **[ソース]** をクリックします。 クラウド スナップショットに関連付けられたボリュームを含むボリューム コンテナーのみが表示されます。 表示されているコンテナーだけが、フェールオーバーの対象です。 ボリューム コンテナーの一覧から、フェールオーバーするボリューム コンテナーを選択します。 **関連付けられたクラウド スナップショットとオフラインのボリュームを含むボリューム コンテナーのみが表示されます。**

       ![ソースの選択](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. **[ターゲット]** をクリックします。 前の手順で選択したボリューム コンテナーのターゲット デバイスを、利用できるデバイスの一覧から選択します。 ソース ボリューム コンテナーを格納できるだけの十分な容量があるデバイスのみが一覧に表示されます。

        ![ターゲット デバイスの選択](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. 最後に、 **[概要]** ですべてのフェールオーバー設定を見直します。 設定を確認し多後で、選択されたボリューム コンテナーのボリュームがオフラインであることを示すチェック ボックスをオンにします。 **[OK]** をクリックします。

       ![フェールオーバー設定を確認する](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple で、フェールオーバー ジョブが作成されます。 **[ジョブ]** ブレードでフェールオーバー ジョブを監視するには、ジョブ通知をクリックします。

    フェールオーバーしたボリューム コンテナーにローカル ボリュームが含まれている場合は、コンテナー内の (階層化されたボリュームではなく) ローカル ボリュームごとに個別の復元ジョブが表示されます。 ローカル ボリュームの復元ジョブが完了するにはかなりの時間がかかるため、 フェールオーバー ジョブの方が先に完了する可能性があります。 これらのボリュームがローカルで保証されるためには、復元ジョブが完了している必要があります。

    ![フェールオーバー ジョブを監視する](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. フェールオーバーが完了したら、 **[デバイス]** ブレードに移動します。
   
   1. フェールオーバー プロセスのターゲット デバイスとして使用されたデバイスを選択します。

       ![デバイス 2 を選択する](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. **[ボリューム コンテナー]** ブレードに移動します。 すべてのボリューム コンテナーと古いデバイスのボリュームの一覧が表示されます。

       ![ターゲットのボリューム コンテナーを表示する](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>次のステップ

* フェールオーバーの実行後、必要に応じて [StorSimple デバイスを非アクティブ化または削除](storsimple-8000-deactivate-and-delete-device.md)します。
* StorSimple デバイス マネージャー サービスを使用する方法の詳細については、「[Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md)」(StorSimple デバイス マネージャーを使用して StorSimple デバイスを管理する) を参照してください。

