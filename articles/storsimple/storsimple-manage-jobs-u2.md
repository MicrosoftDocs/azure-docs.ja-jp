---
title: "StorSimple ジョブの表示と管理 | Microsoft Docs"
description: "StorSimple Manager サービスの [ジョブ] ページと、最近のバックアップ ジョブ、現在のバックアップ ジョブ、スケジュールされたバックアップ ジョブを追跡する方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: cdd3e9e8-7ccd-40a3-8c07-0ab1338c0e59
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d1d1f4d581721994fe7417ae970ad70f9a7b3368


---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs-update-2"></a>StorSimple Manager サービスを使用して StorSimple ジョブを表示および管理する (Update 2)
[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Overview
**[ジョブ]** ページには、StorSimple Manager サービスに接続されているデバイスで開始されたジョブを表示および管理するための一元的なポータルがあります。 複数のデバイスについて、スケジュールされたジョブ、実行中のジョブ、完了したジョブ、取り消されたジョブ、失敗したジョブを表示できます。 結果は表形式で表示されます。 

![[ジョブ] ページ](./media/storsimple-manage-jobs-u2/jobs.png)

以下のフィールドにフィルター処理を行うことで、関心のあるジョブを素早く見つけることができます。

* **状態** - ジョブの状態は、実行中、完了、取り消し済み、失敗、取り消し中、またはエラーありで完了が可能です。
* **[開始日時] と [終了日時]** - ジョブには、日時の範囲に基づいてフィルターをかけることができます。
* **種類** – ジョブの種類は、バックアップ、手動バックアップ、復元、複製、デバイスのフェールオーバー、ローカル固定ボリュームの作成、ボリュームの変更、更新、サポート パッケージ、仮想デバイスのプロビジョニングが可能です。
* **デバイス** - ジョブは、サービスに接続されている特定のデバイスで開始されます。
  フィルター選択されたジョブは、次の属性に基づいて表形式で表示されます。
  
  * **種類** – バックアップ、手動バックアップ、復元、複製、デバイスのフェールオーバー、ローカル固定ボリュームの作成、ボリュームの変更、更新、サポート パッケージ、仮想デバイスのプロビジョニング。
  * **状態** - 実行中、完了、取り消し済み、失敗、取り消し中、またはエラーありで完了。
  * **エンティティ** - ジョブは、ボリューム、バックアップ ポリシー、デバイスに関連付けられます。 たとえば、複製ジョブはボリュームに関連付けられますが、スケジュール済みのバックアップ ジョブはバックアップ ポリシーに関連付けられます。 デバイス ジョブは、障害復旧 (DR) または復元操作の結果として作成されます。
  * **デバイス** - ジョブが開始されたデバイスの名前。
  * **開始日** - ジョブが開始された日時。
  * **進行状況** – 実行中のジョブの完了率。 完了したジョブの場合、これは常に 100% です。

ジョブの一覧は 30 秒ごとに更新されます。

このページでは、以下のジョブ関連の操作を実行できます。

* ジョブの詳細を表示する
* ジョブを取り消す

## <a name="view-job-details"></a>ジョブの詳細を表示する
任意のジョブの詳細を表示するには、以下の手順を実行します。

#### <a name="to-view-job-details"></a>ジョブの詳細を表示するには
1. **[ジョブ]** ページで適切なフィルターを使用してクエリを実行し、関心のあるジョブを表示します。 完了済み、実行中、または取り消し済みのジョブを検索できます。
2. ジョブを選択します。
3. ページの下部にある **[詳細]**をクリックします。
4. **[ジョブの詳細]** ダイアログ ボックスで、状態、詳細、時間統計、データ統計を確認できます。
   
    ![[ジョブの詳細] ページ](./media/storsimple-manage-jobs-u2/JobDetails.png)

## <a name="cancel-a-job"></a>ジョブを取り消す
実行中のジョブを取り消すには、以下の手順を実行します。

> [!NOTE]
> ボリュームの種類の変更やボリュームの拡張などの一部のジョブは取り消しできません。
> 
> 

### <a name="to-cancel-a-job"></a>ジョブを取り消すには
1. **[ジョブ]** ページで適切なフィルターを使用してクエリを実行し、取り消しを行う実行中のジョブを表示します。
2. 該当するジョブを選択します。
3. ページの下部にある **[キャンセル]**をクリックします。
4. 確認を求められたら、 **[はい]**をクリックします。

このジョブが取り消されました。

## <a name="next-steps"></a>次のステップ
* [StorSimple バックアップ ポリシーの管理方法](storsimple-manage-backup-policies.md)。
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理方法](storsimple-manager-service-administration.md)




<!--HONumber=Nov16_HO3-->


