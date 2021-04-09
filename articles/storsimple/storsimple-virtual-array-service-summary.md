---
title: StorSimple Virtual Array サービスの概要ブレード | Microsoft Docs
description: StorSimple デバイス マネージャーのサービスの概要ブレードと、そのブレードを使用して StorSimple Virtual Array の正常性を監視する方法について説明します。
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 31220a8f8b012e08d46195b60a26be9cfc261be5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514514"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>StorSimple Virtual Array に接続されている StorSimple デバイス マネージャーのサービスの概要ブレードを使用する
## <a name="overview"></a>概要
StorSimple デバイス マネージャーのサービスの概要ブレードには、サービスに接続されている StorSimple Virtual Array (オンプレミスの StorSimple 仮想デバイスとも呼ばれます) の概要が表示され、システム管理者による対応が必要な内容が強調表示されます。 このチュートリアルでは、サービスの概要ブレードを紹介したうえで、そのブレードの内容と機能、およびブレードで実行できるタスクについて説明します。

![サービスのダッシュボード](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>管理コマンドと要点
StorSimple の概要ブレードには、StorSimple デバイス マネージャー サービスと、このサービスに登録されている仮想アレイを管理するためのオプションが表示されています。 管理コマンドは、ブレードの上部と左側にあります。

こうしたオプションを使用して、共有やボリュームの追加、仮想アレイで実行されている各種ジョブの監視など、さまざまな操作を実行します。

要点領域では、StorSimple デバイス マネージャーが作成されたリソース グループ、場所、サブスクリプションなど、重要なプロパティがいくつかキャプチャされます。

## <a name="storsimple-device-manager-service-summary"></a>StorSimple デバイス マネージャー サービスの概要
* **[アラート]** タイルには、仮想デバイス全体にわたるすべてのアクティブ アラートのスナップショットが、アラートの重大度別に表示されます。 タイルをクリックすると、**[アラート]** ブレードが表示されます。このブレードで各アラートをクリックすることで、そのアラートの詳細情報 (推奨される対応など) を確認できます。 問題が解決した場合は、アラートをクリアすることもできます。
* **[容量]** タイルには、すべての仮想デバイスで使用できる合計ストレージに対する、すべての仮想デバイスのプロビジョニング済みプライマリ ストレージと、残りのプライマリ ストレージの容量が表示されます。 **[プロビジョニング済み]** は、使用のための準備および割り当てが完了しているストレージ容量を、**[残り]** は、すべての仮想デバイスでプロビジョニングできる残りの容量を示します。 **階層型の残り** の容量は、プロビジョニングできる利用可能な容量 (クラウドを含む) です。**ローカルの残り** の容量は、仮想アレイに接続されたディスクに残っている容量です。
* **使用状況** グラフでは、仮想デバイスの関連メトリックを確認できます。 ここでは、すべての仮想デバイスで使用されているプライマリ ストレージと、仮想デバイスによって使用されている過去 7 日間 (既定) のクラウド ストレージの使用状況を確認できます。 別のタイム スケールを選択するには、グラフの右上隅にある **[編集]** オプションを使用します。
* **[デバイス]** タイルでは、StorSimple デバイス マネージャー内の仮想アレイ数の概要が、デバイスの状態別にグループ化されています。 このタイルをクリックして **[デバイス]** の一覧がブレードに表示されます。各デバイスに固有の概要情報をさらに表示するには、そのデバイスをクリックします。 指定したデバイスの概要ブレードから、デバイスに固有の操作を実行することもできます。 デバイスの概要ブレードの詳細については、[デバイスの概要ブレード](storsimple-virtual-array-device-summary.md)に関するページをご覧ください。

## <a name="view-the-activity-logs"></a>アクティビティ ログを表示する
StorSimple デバイス マネージャー内で実行されるさまざまな操作を表示するには、StorSimple サービスの概要ブレードの左側にある **[アクティビティ ログ]** リンクをクリックします。 これにより **[アクティビティ ログ]** ブレードが表示され、最近実行された操作の概要を確認できます。

![アクティビティ ログ](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>次のステップ
[ローカル Web UI を使用して、StorSimple Virtual Array を管理する方法](storsimple-ova-web-ui-admin.md)を確認します。

