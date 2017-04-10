---
title: "StorSimple Manager サービスのダッシュボード - Virtual Array | Microsoft Docs"
description: "StorSimple Manager サービス ダッシュボードについて説明すると共に、サービス ダッシュボードを使用して StorSimple Virtual Array の状態を監視する方法を説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 36ac6eb4-a616-4bb1-8163-6862ac33da63
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 58aa9508e28a00efe69d0a9cbc8adb6837d085c4


---
# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>StorSimple Virtual Array の StorSimple Manager サービス ダッシュボードを使用する
## <a name="overview"></a>概要
StorSimple Manager サービスのダッシュボード ページには、StorSimple Manager サービスに接続されている StorSimple Virtual Array (StorSimple のオンプレミスの仮想デバイスまたは仮想デバイスとも呼ばれる) の概要が表示され、システム管理者が注意を必要とする内容が強調表示されます。 このチュートリアルでは、ダッシュボード ページを紹介してから、ダッシュボードの内容と機能について、さらに、このページで実行できるタスクについて説明します。

![サービスのダッシュボード](./media/storsimple-ova-service-dashboard/dashboard1.png)

StorSimple Manager サービスのダッシュボードには、次の情報が表示されます。

* ページ上部にある **グラフ** 領域では、仮想デバイスの関連メトリックを確認できます。 ここでは、すべての仮想デバイスで使用されるプライマリ ストレージと、一定期間仮想デバイスによって使用されるクラウド ストレージを確認できます。 グラフの右上隅にあるコントロールを使用して、相対的または絶対的な使用状況を指定したり、1 週間、1 か月、3 か月、または 1 年間のタイム スケールを表示したりできます。 更新コントロール ![更新コントロール](./media/storsimple-ova-service-dashboard/refresh-control.png) を使用して、グラフを更新します。
* **[使用状況の概要]** では、すべての仮想デバイスで使用可能な合計ストレージに対して、すべての仮想デバイスによってプロビジョニングされ、使用されているプライマリ ストレージの割合を示します。 **[プロビジョニング済み]** は、使用するための準備および割り当てが完了しているストレージ容量を示し、**[使用済み]** は、仮想デバイスに接続されているイニシエーターで確認された、共有やボリュームの使用状況を示し、**[最大容量]** は、すべての仮想デバイスの最大容量を示します。
* **[アラート]** 領域には、すべての仮想デバイスでアクティブになっているアラートを重大度別に分類したスナップショットが表示されます。 重大度レベルをクリックすると、 **[アラート]** ページが開き、該当する重大度レベルのアラートが表示対象となります。 **[アラート]** ページでは、個々のアラートをクリックすることで、それぞれのアラートに関する詳細情報 (推奨される対応など) を確認できます。 問題が解決した場合は、アラートをクリアすることもできます。
* **[ジョブ]** 領域には、サービスに接続されているすべての仮想デバイスで最近行われたジョブのスナップショットが表示されます。 現在進行中のジョブ、過去 24 時間に成功または失敗したジョブを確認するために使用できるリンクがあります。 
* ページの右側にある **[概要]** 領域には、サービスの状態、サービスに接続されている仮想デバイスの合計数、サービスの場所、サービスに関連付けられているサブスクリプションの詳細など、役に立つ情報が表示されます。 操作ログへのリンクもあります。 このリンクをクリックすると、完了したすべての StorSimple Manager サービス操作が一覧表示されます。 

StorSimple Manager サービスのダッシュボード ページを使用して、次のタスクを実行できます。

* サービス登録キーの取得に関するページを参照してください。
* 操作ログの表示

## <a name="get-the-service-registration-key"></a>サービス登録キーを取得する
サービス登録キーを使用して、StorSimple 仮想デバイスを StorSimple Manager サービスに登録します。これにより、Azure クラシック ポータルにデバイスが表示され、管理操作の対象となります。 キーは最初の仮想デバイスで作成され、残りの仮想デバイスと共有されます。 

**[登録キー]** (ページの下部にある) をクリックすると、**[サービス登録キー]** ダイアログ ボックスが表示されます。ここでは、現在のサービス登録キーをクリップボードにコピーすることも、サービス登録キーを再生成することもできます。

![登録キー](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

キーを再生成しても、以前に登録した仮想デバイスには影響ありません。キーを再生成した後にサービスに登録された仮想デバイスのみが影響を受けます。

サービス登録キーの取得に関する詳細は、「 [サービス登録キーを取得する](storsimple-ova-manage-service.md#get-the-service-registration-key)」をご覧ください。

## <a name="view-the-operations-logs"></a>操作ログを表示する
操作ログを表示するには、ダッシュボードの **[概要]** ウィンドウにある操作ログ リンクをクリックします。 これにより、管理サービス ページが表示されます。このページでは、フィルター機能を使用して、特定の StorSimple Manager サービスに限定したログを表示することができます。

![操作ログ](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>次のステップ
[ローカル Web UI を使用して、StorSimple Virtual Array を管理する方法](storsimple-ova-web-ui-admin.md)を確認します。




<!--HONumber=Nov16_HO3-->


