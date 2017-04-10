---
title: "StorSimple Virtual Array のバックアップ チュートリアル | Microsoft Docs"
description: "StorSimple Virtual Array の共有/ボリュームをバックアップする方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: a4f55053-a664-4f7c-ba9d-0cb1fb200ff4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 9db591677fb529a72029114db8ad8a8b5d8ab260


---
# <a name="back-up-your-storsimple-virtual-array"></a>StorSimple Virtual Array をバックアップする
## <a name="overview"></a>概要
このチュートリアルは、2016 年 3 月の一般公開 (GA) リリースまたはそれ以降のバージョンを実行する Microsoft Azure StorSimple Virtual Array (StorSimple オンプレミス仮想デバイスまたは StorSimple 仮想デバイスとも呼ばれます) に適用されます。

StorSimple Virtual Array は、ファイル サーバーまたは iSCSI サーバーとして構成できる、ハイブリッド クラウド ストレージのオンプレミス仮想デバイスです。 バックアップの作成、バックアップからの復元、障害復旧が必要な場合にデバイスのフェールオーバーを実行できます。 ファイル サーバーとして構成した場合は、項目レベルの回復もできます。 このチュートリアルでは、Azure クラシック ポータルまたは StorSimple Web UI を使用して、StorSimple Virtual Array のスケジュールされたバックアップと手動バックアップを作成する方法について説明します。

## <a name="back-up-shares-and-volumes"></a>共有やボリュームをバックアップする
バックアップにより、特定の時点の共有やボリュームを保護し、回復性を向上させながら、復元時間を最小限に抑えることができます。 StorSimple デバイスでの共有またはボリュームのバックアップは&2; つの方法、つまり、**スケジュールされた**バックアップまたは**手動**バックアップのいずれかを使用して実行できます。 これらの各方法について、以下のセクションで説明します。

> [!NOTE]
> このリリースでは、スケジュールされたバックアップは既定のポリシーによって作成されます。既定のポリシーは毎日決まった時刻に実行し、デバイスのすべての共有/ボリュームをバックアップします。 現時点では、スケジュールされたバックアップ用にカスタム ポリシーを作成することはできません。
> 
> 

## <a name="change-the-backup-schedule"></a>バックアップのスケジュールを変更する
StorSimple 仮想デバイスの既定のバックアップ ポリシーは、1 日に&1; 回、指定の時刻 (22:30) に開始し、デバイスのすべての共有/ボリュームをバックアップします。 バックアップの開始時刻は変更できますが、バックアップの頻度と保有数 (保持するバックアップの数) を変更することはできません。 既定のポリシーによるバックアップでは仮想デバイス全体がバックアップされるので、ピーク時を避けてスケジュールすることをお勧めします。

既定のバックアップの開始時刻を変更するには、 [Azure クラシック ポータル](https://manage.windowsazure.com/) で次の手順を実行します。

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>既定のバックアップ ポリシーの開始時刻を変更するには
1. デバイスの **[構成]** タブに移動します。
2. **[バックアップ]** セクションで、毎日のバックアップの開始時刻を指定します。
3. **[保存]**をクリックします。

### <a name="take-a-manual-backup"></a>手動バックアップの取得
スケジュールされたバックアップだけでなく、いつでも手動で (オンデマンドで) バックアップを実行できます。

#### <a name="to-create-a-manual-on-demand-backup"></a>手動 (オンデマンド) バックアップを作成するには
1. **[共有]** タブまたは **[ボリューム]** タブに移動します。
2. ページの下部にある **[すべてバックアップ]**をクリックします。 すぐにバックアップを開始してもよいかを確認するメッセージが表示されます。 チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-ova-backup/image3.png) をクリックしてバックアップを開始します。
   
    ![バックアップの確認](./media/storsimple-ova-backup/image4.png)
   
    バックアップ ジョブが開始していることが通知されます。
   
    ![バックアップの開始](./media/storsimple-ova-backup/image5.png)
   
    バックアップ ジョブが正常に作成されたことが通知されます。
   
    ![バックアップ ジョブの作成](./media/storsimple-ova-backup/image7.png)
3. ジョブの進行状況を確認するには、 **[ジョブの表示]**をクリックします。
4. バックアップ ジョブが完了したら、 **[バックアップ カタログ]** タブに移動します。 ここには、完了したバックアップが表示されます。
   
    ![完了したバックアップ](./media/storsimple-ova-backup/image8.png)
5. フィルターで適切なデバイス、バックアップ ポリシー、および時間範囲を選択し、チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-ova-backup/image3.png)を使用します。
   
    カタログに表示されているバックアップ セットの一覧に、そのバックアップが表示されます。

## <a name="view-existing-backups"></a>既存のバックアップを表示する
既存のバックアップを表示するには、Azure クラシック ポータルで次の手順を実行します。

#### <a name="to-view-existing-backups"></a>既存のバックアップを表示するには
1. StorSimple Manager サービスのページで、 **[バックアップ カタログ]** タブをクリックします。
2. 次の手順に従って、バックアップ セットを選択します。
   
   1. デバイスを選択します。
   2. ボックスの一覧で、バックアップを表示する共有またはボリュームを選択します。
   3. 時間範囲を指定します。
   4. チェック マーク アイコン ![](./media/storsimple-ova-backup/image3.png) をクリックしてこのクエリを実行します。
      
      選択した共有またはボリュームに関連付けられているバックアップが、バックアップ セットの一覧に表示されます。

![video_icon](./media/storsimple-ova-backup/video_icon.png) **ビデオ**

StorSimple Virtual Array 上で共有を作成し、共有をバックアップして、データを復元する方法については、このビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Use-the-StorSimple-Virtual-Array/player]
> 
> 

## <a name="next-steps"></a>次のステップ
[StorSimple Virtual Array の管理](storsimple-ova-web-ui-admin.md)の詳細を確認します。




<!--HONumber=Jan17_HO5-->


