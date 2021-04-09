---
title: StorSimple Virtual Array デバイスの概要ブレード | Microsoft Docs
description: StorSimple デバイス マネージャーのデバイスの概要ブレードと、そのブレードを使用して StorSimple Virtual Array の正常性を監視する方法について説明します。
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 1ab72788c768568366f2627055015c74028330b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005879"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>StorSimple Virtual Array に接続されている StorSimple デバイス マネージャーのデバイスの概要ブレードを使用する

## <a name="overview"></a>概要

StorSimple デバイス マネージャーのデバイス ブレードには、指定された StorSimple デバイス マネージャーに登録されている StorSimple Virtual Array の概要が表示され、こうしたデバイスに関する問題で、システム管理者による対応が必要なものが強調表示されています。 このチュートリアルでは、デバイスの概要ブレードを紹介したうえで、そのブレードの内容と機能、およびブレードで実行できるタスクについて説明します。

このデバイスの概要ブレードには次の情報が表示されます。

![デバイス ダッシュボード](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>管理

StorSimple デバイス ブレードには、StorSimple デバイスを管理するためのオプションが表示されています。 管理コマンドは、ブレードの上部と左側にあります。 これらのオプションを使用して共有やボリュームを追加するか、仮想アレイを更新またはフェールオーバーします。

要点領域では、状態、モデル、ソフトウェア バージョンなどの重要なプロパティや、アレイの **Web UI** へのリンクがキャプチャされます。 内部ネットワークでは、[ローカル Web UI](storsimple-ova-web-ui-admin.md) を直接起動して、仮想アレイを管理できます。

![デバイスの要点](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>StorSimple デバイスの概要

* **[アラート]** タイルには、仮想アレイのアクティブ アラートすべてのスナップショットが、アラートの重大度別に表示されます。 タイルをクリックすると、 **[アラート]** ブレードが表示されます。このブレードで各アラートをクリックすることで、そのアラートの詳細情報 (推奨される対応など) を確認できます。 問題が解決した場合は、アラートをクリアすることもできます。

* **[容量]** タイルには、仮想デバイスで使用できる合計ストレージに対する、その仮想デバイス全体のプロビジョニング済みプライマリ ストレージと、残りのプライマリ ストレージの容量が表示されます。 **[プロビジョニング済み]** は、使用のための準備および割り当てが完了しているストレージ容量を、 **[残り]** は、このデバイス全体でプロビジョニングできる残りの容量を示します。 **［Remaining Tiered (階層型の残り)］** の容量は、プロビジョニングできる利用可能な容量 (クラウドを含む) です。**［Remaining Local (ローカルの残り)］** の容量は、この仮想アレイに接続されたディスクに残っている容量です。

* **使用状況** グラフでは、仮想アレイで使用されているプライマリ ストレージと、過去 7 日間 (既定) のクラウド ストレージの使用状況を確認できます。 別のタイム スケールを選択するには、グラフの右上隅にある **[編集]** オプションを使用します。

* **[共有]** タイルまたは **[ボリューム]** タイルでは、デバイス内の共有数またはボリューム数の概要が状態別にグループ化されています。 タイルをクリックすると、**共有** または **ボリューム** の一覧がブレードに表示されます。それぞれのプロパティを表示または変更するには、その共有またはボリュームをクリックします。 詳細については、[共有を管理](storsimple-virtual-array-manage-shares.md)する方法または[ボリュームを管理](storsimple-virtual-array-manage-volumes.md)する方法に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
具体的には、次の方法を学習します。
- [StorSimple Virtual Array での共有の管理](storsimple-virtual-array-manage-shares.md)
    
- [StorSimple Virtual Array でのボリュームの管理](storsimple-virtual-array-manage-volumes.md)

