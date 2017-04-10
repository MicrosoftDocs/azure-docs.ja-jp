---
title: "StorSimple Virtual Array ジョブの表示と管理 | Microsoft Docs"
description: "StorSimple Manager サービスの [ジョブ] ページと、それを使用して、StorSimple Virtual Array の最近のジョブと現在のジョブを追跡する方法を説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 789f7e04-7b3f-456d-be69-37ea48446e9b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d6a0d156e90bfbf16712f9093284e34edd493484


---
# <a name="use-the-storsimple-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>StorSimple Manager サービスを使用した StorSimple Virtual Array のジョブの表示
## <a name="overview"></a>概要
**[ジョブ]** ページには、StorSimple Manager サービスに接続されている仮想アレイ (オンプレミスの仮想デバイスとも呼ばれる) で開始されたジョブを表示および管理するための一元的なポータルがあります。 複数の仮想デバイスについて、実行中のジョブ、完了したジョブ、および失敗したジョブを表示できます。 結果は表形式で表示されます。 

![[ジョブ] ページ](./media/storsimple-ova-manage-jobs/ovajobs1.png)

以下のフィールドにフィルター処理を行うことで、関心のあるジョブを素早く見つけることができます。

* **状態** – すべてのジョブ、実行中のジョブ、完了したジョブ、または失敗したジョブを検索できます。
* **[開始日時] と [終了日時]** - ジョブには、日時の範囲に基づいてフィルターをかけることができます。
* **種類** – ジョブの種類には、すべて、バックアップ、復元、フェールオーバー、更新のダウンロード、または更新のインストールがあります。
* **デバイス** - ジョブは、サービスに接続されている特定のデバイスで開始されます。 フィルター選択されたジョブは、次の属性に基づいて表形式で表示されます。
  
  * **種類** – ジョブの種類には、すべて、バックアップ、復元、フェールオーバー、更新のダウンロード、または更新のインストールがあります。
  * **状態** – ジョブは、すべてのジョブ、実行中のジョブ、完了したジョブ、または失敗したジョブです。
  * **エンティティ** - ジョブは、ボリューム、共有、デバイスに関連付けることができます。 
  * **デバイス** - ジョブが開始されたデバイスの名前。
  * **開始日** - ジョブが開始された日時。
  * **進行状況** – 実行中のジョブの完了率。 完了したジョブの場合、これは常に 100% です。

ジョブの一覧は 30 秒ごとに更新されます。

## <a name="view-job-details"></a>ジョブの詳細を表示する
任意のジョブの詳細を表示するには、以下の手順を実行します。

#### <a name="to-view-job-details"></a>ジョブの詳細を表示するには
1. **[ジョブ]** ページで適切なフィルターを使用してクエリを実行し、関心のあるジョブを表示します。 完了したジョブまたは実行中のジョブを検索できます。
2. ジョブの表形式の一覧からジョブを選択します。
3. ページの下部にある **[詳細]**をクリックします。
4. **[詳細]** ダイアログ ボックスで、状態、詳細、および時間の統計情報を表示することができます。 次の図に **[バックアップ ジョブの詳細]** ダイアログ ボックスの例を示します。
   
    ![[ジョブの詳細] ページ](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>仮想マシンがハイパーバイザーで一時停止しているときにジョブが失敗する
StorSimple Virtual Array でジョブが進行しているときや、デバイス (ハイパーバイザーにプロビジョニングされた仮想マシン) が 15 分以上一時停止しているときにジョブが失敗します。 これは、StorSimple Virtual Array の時間と Microsoft Azure 時間との同期が失われてしまった場合に発生します。 次のスクリーンショットは、復元ジョブのエラーの例です。

![復元ジョブの失敗](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

これらのエラーは、ジョブのバックアップ、復元、更新、およびフェールオーバーで発生します。 仮想マシンを Hyper-V にプロビジョニングした場合、コンピューターは最終的にハイパーバイザーと同期します。 このエラーが発生したら、ジョブの再起動をお試しください。 

## <a name="next-steps"></a>次のステップ
[ローカル Web UI を使用して、StorSimple Virtual Array を管理する方法を確認します](storsimple-ova-web-ui-admin.md)。




<!--HONumber=Nov16_HO3-->


