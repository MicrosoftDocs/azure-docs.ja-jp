---
title: Azure Lab Services でラボのスケジュールを作成する | Microsoft Docs
description: ラボ内の VM が指定した時刻に起動およびシャットダウンされるように、Azure Lab Services でラボのスケジュールを作成する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2a827c3d9f3022cb7d27ee43c9c95227c44f97e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434027"
---
# <a name="create-and-manage-schedules-for-labs-in-azure-lab-services"></a>Azure Lab Services でラボのスケジュールを作成して管理する 
スケジュールを使用すると、ラボの VM が指定した時刻に自動的に起動およびシャットダウンされるように、クラスルーム ラボを構成できます。 1 回限りのスケジュールや定期的なスケジュールを定義することができます。 クラスルーム ラボのスケジュールを作成および管理する手順を以下に示します。 

> [!IMPORTANT]
> スケジュールされている VM の実行時間は、[ユーザーに割り当てられるクォータ](how-to-configure-student-usage.md#set-quotas-for-users)にカウントされません。 クォータは、学生が VM で消費することをスケジュールされている時間以外の時間です。 

## <a name="set-a-schedule-for-the-lab"></a>ラボのスケジュールを設定する
ラボ内の VM が特定の時刻に自動的に起動または停止されるように、ラボ用にスケジュール化されたイベントを作成します。 前に指定したユーザー クォータは、このスケジュールされた時間以外に各ユーザーに割り当てられる追加時間です。 

> [!NOTE]
> 作業を開始する前に、スケジュールがラボ仮想マシンに与える影響を説明します。 
>- テンプレート仮想マシンはスケジュールに含まれていません。 
>- 割り当てられた仮想マシンのみが起動します。 つまり、エンドユーザー (受講生) が要求していないマシンは、スケジュールされた時間に起動しません。 
>- 仮想マシンはすべて（ユーザーがいるかどうかに関わらず）、ラボのスケジュールに基づいて停止されます。 

1. **[スケジュール]** ページに切り替えて、ツール バーの **[Add scheduled event]\(スケジュール化されたイベントの追加\)** を選択します。 

    ![Azure Lab Services の [スケジュール] ページを表示するスクリーンショット。スケジュールの追加ボタンが選択されています。](./media/how-to-create-schedules/add-schedule-button.png)
2. **[Event type]\(イベントの種類\)** で **[Standard]\(標準\)** が選択されていることを確認します。 VM の起動時刻のみを指定するには、 **[Start only]\(開始のみ\)** を選択します。 VM の停止時刻のみを指定するには、 **[Stop only]\(停止のみ\)** を選択します。 
7. **[Repeat]\(繰り返し\)** セクションで、現在のスケジュールを選択します。 

    ![[スケジュール] ページの [スケジュールの追加] ボタン](./media/how-to-create-schedules/select-current-schedule.png)
5. **[Repeat]\(繰り返し\)** ダイアログ ボックスで、次の手順を実行します。
    1. **[Repeat]\(繰り返し\)** フィールドに **[毎週]** が設定されていることを確認します。 
    3. **開始日** を指定します。
    4. VM を起動する **開始時刻** を指定します。
    5. VM をシャットダウンする **停止時刻** を指定します。 
    6. 指定した開始および停止時刻の **タイム ゾーン** を指定します。 
    2. スケジュールを有効にする曜日を選択します。 次の例では、月曜日から木曜日までが選択されています。 
    8. **[保存]** を選択します。 

        ![繰り返しのスケジュールを設定する](./media/how-to-create-schedules/set-repeat-schedule.png)

3. 次に、 **[Add scheduled event]\(スケジュール化されたイベントの追加\)** ページの **[メモ (省略可能)]** に、スケジュールの説明またはメモを入力します。 
4. **[Add scheduled event]\(スケジュール化されたイベントの追加\)** ページで、 **[保存]** を選択します。 

    ![週単位のスケジュール](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>カレンダーでスケジュールを表示する
次の図に示すように、スケジュールが設定されている日付と時刻をカレンダー ビューで強調表示して確認できます。

![カレンダー ビューでのスケジュール](./media/how-to-create-schedules/schedules-calendar.png)

カレンダーで現在の日付に切り替えるには、右上隅にある **[今日]** ボタンを選択します。 **左向きの矢印** を選択するとカレンダーが前の週に切り替わり、**右向きの矢印** を選択すると次の週に切り替わります。 

## <a name="edit-a-schedule"></a>スケジュールを編集する
カレンダーで強調表示されているスケジュールを選択すると、スケジュールを **編集** または **削除** するためのボタンが表示されます。 

![[Edit schedule]\(スケジュールの編集\) ページ](./media/how-to-create-schedules/schedule-edit-button.png)

**[Edit scheduled event]\(スケジュールされたイベントの編集\)** ページで、スケジュールを更新し、 **[Save]\(保存\)** を選択できます。 

## <a name="delete-a-schedule"></a>スケジュールの削除

1. スケジュールを削除するには、カレンダーで強調表示されているスケジュールを選択し、ごみ箱アイコン (削除) ボタンを選択します。

    ![ツール バーの削除ボタン](./media/how-to-create-schedules/schedule-delete-button.png)
2. **[Delete scheduled event]\(スケジュールされたイベントの削除\)** ダイアログ ボックスで、 **[Yes]\(はい\)** を選択して削除を確定します。 



## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてラボにアクセスする](how-to-use-classroom-lab.md)
