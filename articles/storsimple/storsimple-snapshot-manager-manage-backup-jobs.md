---
title: StorSimple Snapshot Manager のバックアップ ジョブ | Microsoft Docs
description: スケジュールされたバックアップ ジョブ、現在実行中のバックアップ ジョブ、および完了したバックアップ ジョブの表示し、管理するためのStorSimple Snapshot Manager MMC スナップインの使用方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 3c26a84e32a17cba83b5ca895f146e561072fa62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998194"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>StorSimple Snapshot Manager を使用したバックアップ ジョブの表示と管理

## <a name="overview"></a>概要
**[スコープ]** ウィンドウ内の **[ジョブ]** ノードは、対話的に、または構成済みポリシーに従って開始した **[スケジュール済み]**、**[過去 24 時間以内]**、および **[実行中]** のバクアップ タスクを表示します。 

このチュートリアルでは、スケジュール済みのバックアップ ジョブ、最近のバックアップ ジョブ、および現在実行中のバックアップ ジョブに関する情報を表示するための **[ジョブ]** ノードの使用方法について説明します  (ジョブとこれに対応する情報の一覧は、 **[結果]** ウィンドウに表示されます)。さらに、表示されているジョブを右クリックすると、使用可能な操作を一覧で示すコンテキスト メニューを表示できます。

## <a name="view-scheduled-jobs"></a>スケジュールされたジョブの表示
スケジュールされたバックアップ ジョブを表示するには、以下の手順を実行します。

#### <a name="to-view-scheduled-jobs"></a>スケジュールされたジョブを表示するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。 
2. **[スコープ]** ウィンドウで、**[ジョブ]** ノードを展開し、**[スケジュール済み]** をクリックします。 以下の情報が **[結果]** ウィンドウに表示されます。
   
   * **[名前]** – スケジュールされたスナップショットの名前
   * **[次の実行]** - 次のスケジュールされたスナップショットの日時
   * **[最後の実行]** - 最も最近スケジュールされたスナップショットの日時
     
     > [!NOTE]
     > 1 回だけのスナップショットの場合、**[次の実行]** と **[最後の実行]** は同じになります。
     
     ![スケジュールされたバックアップ ジョブ](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. 特定のジョブで追加の操作を実行するには、 **[結果]** ウィンドウにあるジョブ名を右クリックしてメニュー オプションから選択します。

## <a name="view-recent-jobs"></a>最近のジョブの表示
バックアップを表示し、過去 24 時間以内に完了したジョブを復元するには、以下の手順を実行します。

#### <a name="to-view-recent-jobs"></a>最近のジョブを表示するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. **[スコープ]** ウィンドウで、**[ジョブ]** ノードを展開し、**[最後の 24 時間]** をクリックします。 **[結果]** ウィンドウに、過去 24 時間のバックアップ ジョブが表示されます (最大 64 件)。 **[結果]** ウィンドウに次の情報が表示されます (指定した **[表示]** オプションによって異なります)。
   
   * **[名前]** – スケジュールされたスナップショットの名前。
   * **[開始済み]** – スナップショットが開始した日時。
   * **[停止済み]** – スナップショットが完了または停止した日時。
   * **[経過時間]** – **[開始済み]** から **[停止済み]** までの時間。
   * **[状態]** – 最近完了したジョブの状態。 **[成功]** はバックアップが正常に作成されたことを示します。 **[失敗]** はジョブが正常に実行されなかったことを示します。
   * **[情報]** – 失敗の理由が表示されます。
   * **[処理バイト数 (MB)]** – ボリューム グループで処理されたデータ量 (MB 単位) 
     
     ![過去 24 時間に実行したジョブ](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. 特定のジョブで追加の操作を実行するには、 **[結果]** ウィンドウにあるジョブ名を右クリックしてメニュー オプションから選択します。
   
    ![ジョブを削除する](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>現在実行中のジョブの表示
現在実行中のジョブを表示するには、以下の手順を実行します。

#### <a name="to-view-currently-running-jobs"></a>現在実行中のジョブを表示するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. **[スコープ]** ウィンドウで、**[ジョブ]** ノードを展開し、**[実行中]** をクリックします。 指定した **[表示]** オプションに基づき、**[結果]** ウィンドウに次の情報が表示されます。
   
   * **[名前]** – スケジュールされたスナップショットの名前。
   * **[開始済み]** – スナップショットが開始した日時。
   * **[チェックポイント]** – バックアップの現在のアクション。
   * **[状態]** – 完了率。
   * **[経過時間]** – バックアップが開始してから経過した時間 
   * **[平均スループット (MB)]** – 処理の合計所要時間に対する処理されたデータの合計バイト数の比率 (MB 単位)。
   * **[処理バイト数 (MB)]** – 処理されたデータの合計バイト数 (MB 単位)。
   * **[書き込みバイト数 (MB)]** – 書き込まれたデータの合計バイト数 (MB 単位)。 これには、データとメタデータが含まれるので、通常は処理バイト数よりも大きくなります。
     
     ![現在実行中のジョブ](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. 特定のジョブで追加の操作を実行するには、 **[結果]** ウィンドウにあるジョブ名を右クリックしてメニュー オプションから選択します。

## <a name="next-steps"></a>次のステップ
* [StorSimple Snapshot Manager を使用した StorSimple ソリューションの管理](storsimple-snapshot-manager-admin.md)方法を確認する。
* [StorSimple Snapshot Manager を使用したバックアップ カタログの管理方法](storsimple-snapshot-manager-manage-backup-catalog.md)

