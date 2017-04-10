---
title: "バックアップからの StorSimple ボリュームの復元 | Microsoft Docs"
description: "StorSimple Manager サービスの [バックアップ カタログ] ページを使用してバックアップ セットから StorSimple ボリュームを復元する方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b979782e-3184-4465-ad5f-e4516a5885d2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c507135c4db758e58d8236de4bc76435b8caf32d


---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>バックアップ セットからの StorSimple ボリュームの復元
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Overview
**[バックアップ カタログ]** ページには、手動バックアップまたは自動バックアップを実行したときに作成されたすべてのバックアップ セットが表示されます。 このページを使用すると、バックアップ ポリシーまたはボリュームのすべてのバックアップを一覧表示したり、バックアップを選択または削除したりできます。また、バックアップを使用してボリュームを復元または複製することもできます。

 ![[バックアップ カタログ] ページ](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

このチュートリアルでは、 **[バックアップ カタログ]** ページを使用してバックアップ セットからデバイスでボリュームを復元する方法について説明します。

## <a name="how-to-use-the-backup-catalog"></a>バックアップ カタログの使用方法
**[バックアップ カタログ]** ページには、バックアップ セットの選択を絞り込むことができるクエリが用意されています。 次のパラメーターに基づいて、取得するバックアップ セットをフィルター選択できます。

* **[デバイス]** - バックアップ セットが作成されたデバイス。
* **[バックアップ ポリシー]** または **[ボリューム]** - このバックアップ セットに関連付けられているバックアップ ポリシーやボリューム。
* **[開始日時]** と **[終了日時]** - バックアップ セットが作成された日付と時刻の範囲。

フィルター選択されたバックアップ セットは、次の属性に基づいて表形式で表示されます。

* **[名前]** - バックアップ セットに関連付けられているバックアップ ポリシーまたはボリュームの名前。
* **[サイズ]** - バックアップ セットの実際のサイズ。
* **[作成日時]** – バックアップが作成された日付と時刻。 
* **[種類]** - バックアップ セットの種類には、ローカル スナップショットとクラウド スナップショットがあります。 ローカル スナップショットは、デバイスにローカルに格納されているすべてのボリューム データのバックアップです。クラウド スナップショットは、クラウドに存在するボリューム データのバックアップを表します。 ローカル スナップショットは、より高速なアクセスを実現します。クラウド スナップショットは、データの回復力を実現するために選択されます。
* **[開始方法]** - バックアップは、スケジュールに従って自動的に開始することも、ユーザーが手動で開始することもできます  (バックアップ ポリシーを使用してバックアップのスケジュールを設定できます。 また、**[バックアップの実行]** オプションを使用して対話型バックアップを実行することもできます)。

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>StorSimple ボリュームをバックアップから復元する方法
**[バックアップ カタログ]** ページを使用すると、特定のバックアップから StorSimple ボリュームを復元できます。 

> [!WARNING]
> バックアップから復元すると、既存のボリュームはバックアップで置き換えられます。 その結果、バックアップが作成された時点以降に書き込まれたすべてのデータが失われます。
> 
> 

ボリュームでの復元を開始する前に、ボリュームを確実にオフラインにしてください。 最初にホスト上でボリュームをオフラインにします。その後、デバイスでもオフラインにする必要があります。 「 [ボリュームをオフラインにする](storsimple-manage-volumes.md#take-a-volume-offline)」の手順に従ってください。 バックアップ セットからボリュームを復元するには、次の手順を実行します。

### <a name="to-restore-from-a-backup-set"></a>バックアップ セットから復元するには
1. StorSimple Manager サービスのページで、 **[バックアップ カタログ]** タブをクリックします。
   
    ![[バックアップ カタログ]](./media/storsimple-restore-from-backup-set/HCS_Restore.png)
2. 次の手順に従って、バックアップ セットを選択します。
   
   1. 適切なデバイスを選択します。
   2. ボックスの一覧で、選択するバックアップのボリュームまたはバックアップのポリシーを選択します。
   3. 時間範囲を指定します。
   4. チェック マーク アイコン  ![チェック マーク アイコン](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png)  をクリックしてこのクエリを実行します。
      
      選択したボリュームまたはバックアップ ポリシーに関連付けられているバックアップが、バックアップ セットの一覧に表示されます。
3. バックアップ セットを展開して、関連付けられているボリュームを表示します。 これらのボリュームは、復元する前にホストおよびデバイス上でオフラインにする必要があります。 「 [ボリュームをオフラインにする](storsimple-manage-volumes.md#take-a-volume-offline)」の手順に従ってください。
   
   > [!IMPORTANT]
   > 最初にホスト上のボリュームをオフラインにしてから、デバイス上のボリュームをオフラインにします。 ホスト上でボリュームをオフラインにしない場合、データの破損を招く可能性があります。
   > 
   > 
4. バックアップ セットを選択します。 ページの下部にある **[復元]** をクリックします。
5. 確認を求められます。 
   
    ![確認ページ](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)
6. 復元情報を確認し、チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) をクリックします。 復元ジョブが開始されます。このジョブは、**[ジョブ]** ページに表示されます。 
7. 復元操作が完了した後、ボリュームの内容がバックアップからのボリュームで置き換えられていることを確認できます。

![ビデオ](./media/storsimple-restore-from-backup-set/Video_icon.png) **ビデオ**

StorSimple の複製機能と復元機能を使用して、削除されたファイルを回復する方法を説明したビデオについては、 [こちら](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)を参照してください。

## <a name="next-steps"></a>次のステップ
* [StorSimple ボリュームを管理する](storsimple-manage-volumes.md)方法について説明します。
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理方法](storsimple-manager-service-administration.md)




<!--HONumber=Nov16_HO3-->


