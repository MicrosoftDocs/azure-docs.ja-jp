---
title: StorSimple 8000 シリーズ デバイスのサービスの概要ブレードを使用する
description: StorSimple サービスの概要ブレードと、そのブレードを使用して StorSimple ソリューションの状態を監視する方法について説明します。
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 7b4b697b3d27b57212fc59396e1f8111e297d6cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514948"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>StorSimple 8000 シリーズ デバイスのサービスの概要ブレードを使用する

## <a name="overview"></a>概要

StorSimple デバイス マネージャーのサービスの概要ブレードには、StorSimple デバイス マネージャー サービスに接続されているすべてのデバイスの概要が表示されます。システム管理者が注意を必要とするこれらのデバイスは強調表示されます。 このチュートリアルでは、サービスの概要ブレードを紹介したうえで、ダッシュボードの内容と機能、およびこのページで実行できるタスクについて説明します。

![サービスの概要](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>管理コマンド

StorSimple のサービスの概要ブレードには、StorSimple デバイス マネージャー サービスと、このサービスに登録されている StorSimple 8000 シリーズ デバイスを管理するためのオプションが表示されています。 管理コマンドは、ブレードの上部と左側にあります。

![コマンド バー](./media/storsimple-8000-service-dashboard/service-summary2.png)

こうしたオプションを使用して、共有やボリュームの追加、StorSimple デバイスで実行されている各種ジョブの監視など、さまざまな操作を実行します。


## <a name="essentials"></a>要点

要点領域では、StorSimple デバイス マネージャーが作成されたリソース グループ、場所、サブスクリプションなど、重要なプロパティがいくつかキャプチャされます。

![要点](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>StorSimple デバイス マネージャー サービスの概要

* **[アラート]** タイルには、デバイス全体にわたるすべてのアクティブ アラートのスナップショットが、アラートの重大度別に表示されます。

    ![[アラート] タイル](./media/storsimple-8000-service-dashboard/service-summary4.png)

    タイルをクリックすると、**[アラート]** ブレードが表示されます。このブレードで各アラートをクリックすることで、そのアラートの詳細情報 (推奨される対応など) を確認できます。 問題が解決した場合は、アラートをクリアすることもできます。

    ![[アラート] タイルをクリック](./media/storsimple-8000-service-dashboard/service-summary8.png)

* **[容量]** タイルには、すべてのデバイスで使用できる合計ストレージに対する、すべてのデバイスのプロビジョニング済みプライマリ ストレージと、残りのプライマリ ストレージの容量が表示されます。 **[プロビジョニング済み]** は、使用のための準備および割り当てが完了しているストレージ容量を、**[残り]** は、すべてのデバイスでプロビジョニングできる残りの容量を示します。

    ![[容量] タイル](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **階層型の残り** の容量は、プロビジョニングできる利用可能な容量 (クラウドを含む) です。**ローカルの残り** の容量は、StorSimple 8000 シリーズ デバイスに接続されたディスクに残っている容量です。


* **使用状況** グラフでは、デバイスの関連メトリックを確認できます。 ここでは、すべてのデバイスで使用されているプライマリ ストレージと、デバイスによって使用されている過去 7 日間 (既定) のクラウド ストレージの使用状況を確認できます。 

    ![使用状況タイル](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    別のタイム スケールを選択するには、グラフの右上隅にある **[編集]** オプションを使用します。

     ![[使用量] タイルをクリック](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![グラフ データのエクスポート](./media/storsimple-8000-service-dashboard/service-summary11.png)

* **[デバイス]** タイルでは、StorSimple デバイス マネージャー内の StorSimple 8000 シリーズ デバイス数の概要が、デバイスの状態別にグループ化されています。 

    ![[デバイス] タイル](./media/storsimple-8000-service-dashboard/service-summary5.png)

    このタイルをクリックして **[デバイス]** の一覧がブレードに表示されます。各デバイスに固有の概要情報をさらに表示するには、そのデバイスをクリックします。 指定したデバイスの概要ブレードから、デバイスに固有の操作を実行することもできます。 デバイスの概要ブレードの詳細については、[デバイスの概要ブレード](storsimple-8000-device-dashboard.md)に関するページをご覧ください。

    ![[デバイス] タイルをクリック](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>アクティビティ ログを表示する

StorSimple デバイス マネージャー内で実行されるさまざまな操作を表示するには、StorSimple サービスの概要ブレードの左側にある **[アクティビティ ログ]** リンクをクリックします。 これにより **[アクティビティ ログ]** ブレードが表示され、最近実行された操作の概要を確認できます。

![アクティビティ ログ](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>次のステップ

* [StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理方法](storsimple-8000-manager-service-administration.md)

