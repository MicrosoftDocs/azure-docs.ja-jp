---
title: Azure Lab Services でクラスルーム ラボのスケジュールを作成する | Microsoft Docs
description: ラボ内の VM が指定した時刻に起動およびシャットダウンされるように、Azure Lab Services でクラスルーム ラボのスケジュールを作成する方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: f607ba68563aa92797f45cf77db0575ae6802fee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385605"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボのスケジュールを作成して管理する 
スケジュールを使用すると、ラボの VM が指定した時刻に自動的に起動およびシャットダウンされるように、クラスルーム ラボを構成できます。 1 回限りのスケジュールや定期的なスケジュールを定義することができます。 クラスルーム ラボのスケジュールを作成および管理する手順を以下に示します。 

> [!IMPORTANT]
> スケジュールされている VM の実行時間は、[ユーザーに割り当てられるクォータ](how-to-configure-student-usage.md#set-quotas-for-users)にカウントされません。 クォータは、学生が VM で消費することをスケジュールされている時間以外の時間です。 

## <a name="add-a-schedule-once"></a>スケジュールを追加する (1 回)

1. **[スケジュール]** ページに切り替えて、ツール バーの **[スケジュールの追加]** を選択します。 

    ![[スケジュール] ページの [スケジュールの追加] ボタン](../media/how-to-create-schedules/add-schedule-button.png)
2. **[スケジュールの追加]** ページで、上部にある **[1 度]** オプションが選択されていることを確認します。 そうでない場合は、 **[1 度]** を選択します。 
3. **[Schedule date (required)]\(スケジュールの日付 (必須)\)** で、日付を入力するか、カレンダー アイコンを選択して日付を選択します。 
4. **[Start time]\(開始時刻\)** で、VM を起動する時刻を選択します。 停止時刻が設定されていない場合、開始時刻は必須です。 停止時刻のみを指定する場合は、 **[Remove start event]\(開始イベントを削除する\)** を選択します。 **[Start time]\(開始時刻\)** が無効になっている場合は、ドロップダウン リストの横にある **[Add start event]\(開始イベントを追加する\)** を選択して有効にします。 
5. **[Stop time]\(停止時刻\)** で、VM をシャットダウンする時刻を選択します。 開始時刻が設定されていない場合、停止時刻は必須です。 開始時刻のみを指定する場合は、 **[Remove stop event]\(停止イベントを削除する\)** を選択します。 **[Stop time]\(停止時刻\)** が無効になっている場合は、ドロップダウン リストの横にある **[Add stop event]\(停止イベントを追加する\)** を選択して有効にします。
6. **[Time zone (required)]\(タイム ゾーン (必須)\)** で、指定した開始時刻と停止時刻のタイム ゾーンを選択します。 
7. **[Notes]\(備考\)** に、スケジュールの説明やメモを入力します。 
8. **[保存]** を選択します。 

    ![1 回限りのスケジュール](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>定期的なスケジュールを追加する (週単位)

1. **[スケジュール]** ページに切り替えて、ツール バーの **[スケジュールの追加]** を選択します。 

    ![[スケジュール] ページの [スケジュールの追加] ボタン](../media/how-to-create-schedules/add-schedule-button.png)
2. **[スケジュールの追加]** ページで、上部にある **[Weekly]\(週単位\)** に切り替えます。 
3. **[Schedule days (required)]\(スケジュールする曜日 (必須)\)** で、スケジュールを有効にする曜日を選択します。 次の例では、月曜日から金曜日までが選択されています。 
4. **[From]\(開始\)** フィールドで、**スケジュールの開始日**を入力するか、または**カレンダー** ボタンを選択して日付を選択します。 このフィールドは必須です。 
5. **[Schedule end date]\(スケジュールの終了日\)** で、VM をシャットダウンする終了日を入力するか選択します。 
6. **[Start time]\(開始時刻\)** で、VM を起動する時刻を選択します。 停止時刻が設定されていない場合、開始時刻は必須です。 停止時刻のみを指定する場合は、 **[Remove start event]\(開始イベントを削除する\)** を選択します。 **[Start time]\(開始時刻\)** が無効になっている場合は、ドロップダウン リストの横にある **[Add start event]\(開始イベントを追加する\)** を選択して有効にします。 
7. **[Stop time]\(停止時刻\)** で、VM をシャットダウンする時刻を選択します。 開始時刻が設定されていない場合、停止時刻は必須です。 開始時刻のみを指定する場合は、 **[Remove stop event]\(停止イベントを削除する\)** を選択します。 **[Stop time]\(停止時刻\)** が無効になっている場合は、ドロップダウン リストの横にある **[Add stop event]\(停止イベントを追加する\)** を選択して有効にします。
8. **[Time zone (required)]\(タイム ゾーン (必須)\)** で、指定した開始時刻と停止時刻のタイム ゾーンを選択します。  
9. **[Notes]\(備考\)** に、スケジュールの説明やメモを入力します。 
10. **[保存]** を選択します。 

    ![週単位のスケジュール](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>カレンダーでスケジュールを表示する
次の図に示すように、スケジュールが設定されている日付と時刻をカレンダー ビューで強調表示して確認できます。

![カレンダー ビューでのスケジュール](../media/how-to-create-schedules/schedules-in-calendar.png)

カレンダーで現在の日付に切り替えるには、右上隅にある **[今日]** ボタンを選択します。 **左向きの矢印**を選択するとカレンダーが前の週に切り替わり、**右向きの矢印**を選択すると次の週に切り替わります。 

## <a name="edit-a-schedule"></a>スケジュールを編集する
カレンダーで強調表示されているスケジュールをダブルクリックするか、ツール バーの**鉛筆**ボタンを選択すると、 **[Edit schedule]\(スケジュールの編集\)** ページが表示されます。 このページでの設定の更新は、「[定期的なスケジュールを追加する](#add-a-recurring-schedule-weekly)」セクションで説明した **[Add schedule]\(スケジュールの追加\)** ページでの設定更新と同じです。 

![[Edit schedule]\(スケジュールの編集\) ページ](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>スケジュールの削除

1. スケジュールを削除するには、次の図のように、ツール バーのごみ箱 (削除) ボタンを選択します。

    ![ツール バーの削除ボタン](../media/how-to-create-schedules/delete-schedule-button.png)

    カレンダーでスケジュールを設定されているどの日付や時刻にでも削除ボタンを使用でき、 **[削除]** を選択します。 
2. **[Delete schedules]\(スケジュールの削除\)** ページで、 **[はい]** を選択します。

    ![スケジュールの削除の確認](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)
