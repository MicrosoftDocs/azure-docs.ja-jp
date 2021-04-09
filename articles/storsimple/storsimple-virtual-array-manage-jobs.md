---
title: StorSimple Virtual Array ジョブの表示と管理 | Microsoft Docs
description: StorSimple デバイス マネージャー サービスの [ジョブ] ページと、そのページを使用して、StorSimple Virtual Array の最近のジョブと現在のジョブを追跡する方法を説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: d806d8a04dc1bd8547808d20c77bfec310f7dd06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992998"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>StorSimple デバイス マネージャー サービスを使用して StorSimple Virtual Array のジョブを表示する
## <a name="overview"></a>概要
**[ジョブ]** ブレードには、StorSimple デバイス マネージャー サービスに接続されている仮想アレイで開始されたジョブを、表示および管理するための一元的なポータルがあります。 複数の仮想デバイスについて、実行中のジョブ、完了したジョブ、および失敗したジョブを表示できます。 結果は表形式で表示されます。

![ジョブ ブレード](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

以下のフィールドにフィルター処理を行うことで、関心のあるジョブを素早く見つけることができます。

* **時間範囲** - 日付と時刻の範囲に基づいて、ジョブにフィルターを適用できます。
* **デバイス** - ジョブは、サービスに接続されている特定のデバイスで開始されます。 フィルター処理されたジョブは、次の属性に基づいて表形式で表示されます。
  
  * **名前** - ジョブの名前には、**すべて**、**バックアップ**、**複製**、**フェールオーバー**、**更新プログラムのダウンロード**、**更新プログラムのインストール** があります。
  * **状態** - **すべて**、**進行中**、**成功**、**失敗**、**キャンセル済み** のいずれかです。
  * **エンティティ** - ジョブは、ボリューム、共有、デバイスに関連付けることができます。
  * **デバイス** - ジョブが開始されたデバイスの名前。
  * **開始日** - ジョブが開始された日時。
  * **期間** - ジョブが実行された期間。
* **状態** – すべてのジョブ、実行中のジョブ、完了したジョブ、または失敗したジョブを検索できます。
* **種類** - ジョブの種類には、すべて、バックアップ、復元、フェールオーバー、更新プログラムのダウンロード、更新プログラムのインストールがあります。

ジョブの一覧は 30 秒ごとに更新されます。

## <a name="view-job-details"></a>ジョブの詳細を表示する
任意のジョブの詳細を表示するには、以下の手順を実行します。

#### <a name="to-view-job-details"></a>ジョブの詳細を表示するには
1. **[ジョブ]** ブレードで適切なフィルターを使用してクエリを実行し、関心のあるジョブを表示します。 完了したジョブまたは実行中のジョブを検索できます。
2. ジョブの表形式の一覧からジョブを選択します。
   
    ![ジョブ ブレード](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. ページの下部にある [**詳細**] をクリックします。
4. **[詳細]** ダイアログ ボックスで、状態、詳細、および時間の統計情報を表示することができます。 次の図に **[バックアップ ジョブの詳細]** ダイアログ ボックスの例を示します。
   
    ![Job details](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>仮想マシンがハイパーバイザーで一時停止しているときにジョブが失敗する
StorSimple Virtual Array でジョブが進行しているときや、デバイス (ハイパーバイザーにプロビジョニングされた仮想マシン) が 15 分以上一時停止しているときにジョブが失敗します。 これは、StorSimple Virtual Array の時間と Microsoft Azure 時間との同期が失われてしまった場合に発生します。 

次のエラーが表示されます: "デバイスの時刻と Microsoft Azure の時刻の差が 15 分を超えています。 ハイパーバイザーとデバイスの時刻を NTP サーバーと同期する必要があります。 接続の問題がないことを確認してください。 接続の問題のトラブルシューティングを行うには、仮想デバイスのローカル Web UI から診断テストを実行します。"

これらのエラーは、ジョブのバックアップ、復元、更新、およびフェールオーバーで発生します。 仮想マシンが Hyper-V でプロビジョニングされている場合、コンピューターは最終的にハイパーバイザーと同期します。 このエラーが発生したら、ジョブの再起動をお試しください。

## <a name="next-steps"></a>次のステップ
[ローカル Web UI を使用して、StorSimple Virtual Array を管理する方法を確認します](storsimple-ova-web-ui-admin.md)。

