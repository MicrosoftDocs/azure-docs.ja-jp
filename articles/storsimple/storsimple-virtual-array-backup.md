---
title: Microsoft Azure StorSimple Virtual Array のバックアップ チュートリアル | Microsoft Docs
description: Microsoft Azure StorSimple Virtual Array 用にスケジュールされたバックアップと手動バックアップを作成し、項目レベルの回復を実行して、削除されたファイルを仮想アレイで復元する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99c7f4ab92e4cce3937a933fec97008c281a0eae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018292"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>StorSimple Virtual Array 上の共有またはボリュームをバックアップする

## <a name="overview"></a>概要

StorSimple Virtual Array は、ファイル サーバーまたは iSCSI サーバーとして構成できる、ハイブリッド クラウド ストレージのオンプレミス仮想デバイスです。 仮想アレイを使用すると、デバイス上のすべての共有またはボリュームについて、スケジュールされたバックアップと手動バックアップを作成できます。 ファイル サーバーとして構成した場合は、項目レベルの回復もできます。 このチュートリアルでは、スケジュールされたバックアップと手動バックアップを作成し、項目レベルの回復を実行して、削除されたファイルを仮想アレイで復元する方法について説明します。

このチュートリアルは、StorSimple Virtual Array にのみ適用されます。 8000 シリーズについては、[8000 シリーズ デバイスのバックアップの作成](./storsimple-8000-manage-backup-policies-u2.md)に関するページをご覧ください

## <a name="back-up-shares-and-volumes"></a>共有やボリュームをバックアップする

バックアップにより、特定の時点の共有やボリュームを保護し、回復性を向上させながら、復元時間を最小限に抑えることができます。 StorSimple デバイスでの共有またはボリュームのバックアップは 2 つの方法、つまり、**スケジュールされた** バックアップまたは **手動** バックアップのいずれかを使用して実行できます。 これらの各方法について、以下のセクションで説明します。

## <a name="change-the-backup-start-time"></a>バックアップの開始時刻を変更する

> [!NOTE]
> このリリースでは、スケジュールされたバックアップは既定のポリシーによって作成されます。既定のポリシーは毎日決まった時刻に実行し、デバイスのすべての共有/ボリュームをバックアップします。 現時点では、スケジュールされたバックアップ用にカスタム ポリシーを作成することはできません。


StorSimple Virtual Array の既定のバックアップ ポリシーは、1 日に 1 回、指定の時刻 (22:30) に開始し、デバイスのすべての共有/ボリュームをバックアップします。 バックアップの開始時刻は変更できますが、バックアップの頻度と保有数 (保持するバックアップの数) を変更することはできません。 こうしたバックアップでは仮想デバイス全体がバックアップされます。 これが、デバイスのパフォーマンスや、デバイスにデプロイされているワークロードに影響を及ぼす可能性があるため、 このようなバックアップはピーク時を避けてスケジュールすることをお勧めします。

 既定のバックアップの開始時刻を変更するには、[Azure Portal](https://portal.azure.com/) で次の手順を実行します。

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>既定のバックアップ ポリシーの開始時刻を変更するには

1. **[デバイス]** に移動します。 StorSimple デバイス マネージャー サービスに登録されているデバイスの一覧が表示されます。 
   
    ![デバイスに移動](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. 使用しているデバイスを選択し、クリックします。 **[設定]** ブレードが表示されます。 **[管理] > [バックアップ ポリシー]** の順に移動します。
   
    ![デバイスを選択](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. **[バックアップ ポリシー]** ブレードでは、既定の開始時刻 22 時 30 分が設定されています。 新しい開始時刻は、[デバイスのタイム ゾーンでの日単位のスケジュール] で指定できます。
   
    ![バックアップ ポリシーに移動](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. **[保存]** をクリックします。

### <a name="take-a-manual-backup"></a>手動バックアップの取得

スケジュールされたバックアップだけでなく、いつでも手動 (オンデマンド) でデバイス データのバックアップを実行できます。

#### <a name="to-create-a-manual-backup"></a>手動でバックアップを作成するには

1. **[デバイス]** に移動します。 デバイスを選択し、選択された行の右端で **[...]** を右クリックします。 コンテキスト メニューから、 **[バックアップの作成]** を選択します。
   
    ![バックアップの作成に移動](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. **[バックアックの作成]** ブレードで、 **[バックアップの作成]** をクリックします。 これによりファイル サーバー上のすべての共有、または iSCSI サーバー上のすべてのボリュームがバックアップされます。 
   
    ![バックアップの開始](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    オンデマンド バックアップが開始され、バックアップ ジョブが開始されたことが表示されます。
   
    ![バックアップの開始 2](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    ジョブが正常に完了すると、再度通知されます。 バックアップ プロセスが開始されます。
   
    ![バックアップ ジョブの作成](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. バックアップの進行状況を追跡し、ジョブの詳細を確認するには、通知をクリックします。 **[ジョブの詳細]** が表示されます。
   
     ![ジョブの詳細のバックアップ](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. バックアップが完了したら、 **[管理] > [バックアップ カタログ]** の順に移動します。 デバイス上の共有 (またはボリューム) すべてのクラウド スナップショットが表示されます。
   
    ![完了したバックアップ](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>既存のバックアップを表示する
既存のバックアップを表示するには、Azure Portal で次の手順を実行します。

#### <a name="to-view-existing-backups"></a>既存のバックアップを表示するには

1. **[デバイス]** ブレードに移動します。 使用しているデバイスを選択し、クリックします。 **[設定]** ブレードで、 **[管理] > [バックアップ カタログ]** の順に移動します。
   
    ![バックアップ カタログに移動](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. フィルター処理に使用する次の条件を指定します。
   
   - **時間範囲** – **[過去 1 時間]** 、 **[過去 24 時間]** 、 **[過去 7 日間]** 、 **[過去 30 日間]** 、 **[過去 1 年]** 、 **[ユーザー設定の日付]** を指定できます。
    
   - **デバイス** – StorSimple デバイス マネージャー サービスに登録されている iSCSI サーバーやファイル サーバーの一覧から選択します。
   
   - **開始** – (バックアップ ポリシーによって) 自動的に **スケジュール** するか、(ご自身で) **手動** で開始できます。
   
     ![バックアップのフィルター処理](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. **[Apply]** をクリックします。 **[バックアップ カタログ]** ブレードに、フィルター処理されたバックアップの一覧が表示されます。 一度に表示できるバックアップ要素は 100 個だけです。
   
    ![更新されたバックアップ カタログ](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>次のステップ

[StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)の詳細を確認します。