---
title: StorSimple デバイス マネージャー サービスでデバイスの概要を使用する
description: StorSimple デバイス マネージャー サービスのデバイスの概要と、ストレージ メトリックと接続されているイニシエーターを表示してシリアル番号と IQN を確認する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 34bbf4d723e46663efe77560245db74d9dea1fc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017153"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスでデバイスの概要を使用する

## <a name="overview"></a>概要
StorSimple デバイスの概要ブレードでは、特定の StorSimple デバイスの概要情報を確認できます。これは、Microsoft Azure StorSimple ソリューションに含まれるすべてのデバイスの情報を確認できるサービスの概要ブレードとは対照的です。

デバイスの概要ブレードには、特定の StorSimple デバイス マネージャーに登録されている StorSimple 8000 シリーズ デバイスの概要が表示され、システム管理者による対応が必要なデバイスの問題が強調表示されます。 このチュートリアルでは、デバイスの概要ブレードを紹介したうえで、そのブレードの内容と機能、およびブレードで実行できるタスクについて説明します。

このデバイスの概要ブレードには次の情報が表示されます。

![デバイスの概要ブレード](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>管理コマンド バー

StorSimple デバイス ブレードには、StorSimple デバイスを管理するためのオプションが表示されています。 管理コマンドは、ブレードの上部と左側にあります。 これらのオプションを使用して、共有とボリュームの追加や、デバイスの更新とフェールオーバーなどを行います。

![管理コマンド バー](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>要点

[要点] 領域には、状態、モデル、ターゲット IQN、ソフトウェア バージョンなどの一部の重要なプロパティが表示されます。 

![デバイスの要点](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>監視

* **[アラート]** タイルには、デバイスのすべてのアクティブ アラートのスナップショットが、アラートの重大度別に表示されます。

    ![[アラート] タイル](./media/storsimple-8000-device-dashboard/device-summary4.png)

    タイルをクリックすると、 **[アラート]** ブレードが表示されます。このブレードで各アラートをクリックすることで、そのアラートの詳細情報 (推奨される対応など) を確認できます。 問題が解決した場合は、アラートをクリアすることもできます。

    ![[アラート] タイルをクリックする](./media/storsimple-8000-device-dashboard/device-summary10.png)

* **[状態と正常性]** タイルには、デバイスの状態を含むハードウェア コンポーネントの正常性に関する洞察が表示されます。 デバイスの状態は、[オフライン]、[オンライン]、[非アクティブ化]、または [セットアップの準備が完了しました] が可能です。

    ![[状態と正常性] タイル](./media/storsimple-8000-device-dashboard/device-summary5.png)

* **[ボリューム]** タイルには、デバイス内のボリューム数の概要が状態別にグループ化されて表示されます。

    ![[ボリューム] タイル](./media/storsimple-8000-device-dashboard/device-summary6.png)

    タイルをクリックすると、 **[ボリューム]** ブレードが開き、ボリュームが一覧表示されます。ボリュームのプロパティを表示または変更するには、個々のボリュームをクリックします。
    
    ![[ボリューム] タイルをクリックする](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    詳細については、[ボリュームの管理](storsimple-8000-manage-volumes-u2.md)に関する記事を参照してください。

* **使用状況** グラフで、デバイスで使用されているプライマリ ストレージとクラウド ストレージの過去 7 日間 (既定) の使用状況を確認できます。

     ![使用状況タイル](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     別のタイム スケールを選択するには、グラフの右上隅にある **[編集]** オプションを使用します。

     ![使用状況グラフを編集する](./media/storsimple-8000-device-dashboard/device-summary12.png)

     このグラフでは、総プライマリ記憶域 (ホストによってデバイスに書き込まれたデータの量) と、一定の期間にわたってデバイスで使用された総クラウド ストレージ容量に関するメトリックを確認できます。
  
     このコンテキストでは、"*プライマリ ストレージ*" はホストによって書き込まれたデータの総量を指し、ボリュームの種類ごとに分けることができます。*プライマリ階層型記憶域* には、ローカルに保存されたデータとクラウドに階層化されたデータが含まれます。 "*プライマリ ローカル固定ストレージ*" には、ローカルに保存されたデータだけが含まれます。 一方、*クラウド ストレージ* は、クラウドに保存されているデータの総量の測定値です。 このストレージには、階層化されたデータとバックアップが含まれます。 クラウドに保存されているデータが重複除去および圧縮されているのに対して、プライマリ ストレージは、データが重複除去および圧縮される前のストレージ使用量を示しています (この 2 つの数値を比較して、圧縮率を把握できます)。プライマリ ストレージとクラウド ストレージに表示されるデータ量は、両方とも設定した追跡頻度に基づいています。 たとえば、頻度を 1 週間とすると、このグラフには前週の日別のデータが表示されます。

     ある期間にわたって使用されたクラウド ストレージの量を表示するには、 **[使用されているクラウド ストレージ]** オプションを選択します。 ホストによって書き込まれたストレージの合計を表示するには、 **[PRIMARY TIERED STORAGE USED (使用されているプライマリ階層化ストレージ)]** と **[PRIMARY LOCALLY PINNED STORAGE USED (使用されているプライマリ ローカル固定ストレージ)]** を選択します。 
     詳細については、「[StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの監視](./storsimple-8000-monitor-device.md)」を参照してください。


* **[容量]** タイルには、デバイスにプロビジョニング済みのプライマリ ストレージの容量と残りのプライマリ ストレージの容量が表示されます。これらは、デバイスが使用できるストレージの総容量に対する数値です。 **[プロビジョニング済み]** は、使用のための準備および割り当てが完了しているストレージ容量を、 **[残り]** は、このデバイス全体でプロビジョニングできる残りの容量を示します。 

    ![使用状況タイル 2](./media/storsimple-8000-device-dashboard/device-summary8.png)

    容量が階層化ストレージとローカル固定ボリュームにどのようにプロビジョニングされているかを確認するには、このタイルをクリックします。 **[Remaining Tiered]\(残りの階層型容量\)** は、クラウドを含めてプロビジョニングするために使用できる容量です。 **[Remaining Local]\(残りのローカル容量\)** は、このディスクに接続されているディスクに残っている容量です。

    ![使用状況グラフをクリックする](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>次のステップ
* [StorSimple Manager サービスの概要ブレード](storsimple-8000-service-dashboard.md)を参照します。
* [StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)の詳細