---
title: Teams からの Azure Lab Services ユーザー リストを管理する
description: Teams からの Azure Lab Services のユーザー リストを管理する方法について説明します。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946523"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Teams からの Lab Services ユーザー リストを管理する

Teams 内にラボが作成されると ([Teams からの Lab Services ラボの開始と作成](how-to-get-started-create-lab-within-teams.md)に関する記事を参照)、ラボ ユーザー リストが自動的に設定され、チーム メンバーシップと同期されます。 所有者、メンバー、ゲストなどのチームのすべてのユーザーは、ラボ ユーザー リストに自動的に追加されます。 Azure Lab Services はチーム メンバーシップと同期され続け、24 時間ごとに自動同期がトリガーされます。 

## <a name="sync-users"></a>ユーザーの同期

教師は **[同期]** ボタンを使用して、チーム メンバーシップが更新された後に、手動同期をトリガーできます。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="ユーザーの同期":::

自動または手動の同期が完了したら、ラボが発行されているかどうかに応じて、次のことが当てはまります。

* ラボが少なくとも 1 回発行されていない場合:
    * ユーザーは、チーム メンバーシップの変更に応じて、ラボ ユーザー リストから追加または削除されます。 
* ラボが少なくとも 1 回発行されている場合は、ユーザーの追加や削除に加えて、ラボの容量が自動的に更新されます。
    * 新たにチームに追加されたものがある場合は、新しい VM が作成されます。
    * チームから削除されたユーザーがいる場合は、関連付けられている VM も削除されます。

## <a name="next-steps"></a>次のステップ

テンプレート VM が構成され、教師がテンプレートの発行を選択すると、ラボのユーザー リスト内のユーザー数に相当する数の VM が作成されます。 ラボが発行され、VM が作成されると、ユーザーはラボに自動的に登録され、Azure Lab Services への初回ログイン時に VM が割り当てられます。つまり、最初に **Azure Lab Services** アプリがあるタブにアクセスしたときです。 

テンプレート VM を発行するには、Teams Lab Services ウィンドウにアクセスし、 **[テンプレート]** タブ > **...**  ->  **[発行]** を選択します。

VM プールを管理するには、[Teams からのラボ サービスの VM プールの管理](how-to-manage-vm-pool-within-teams.md)に関する記事を参照してください。

### <a name="also-review"></a>その他の参照

次の記事をご覧ください。

- [Teams での Azure Lab Services の使用の概要](lab-services-within-teams-overview.md)
- [Teams からの Lab Services ラボの作成および概要](how-to-get-started-create-lab-within-teams.md)
- [Teams からの Lab Services スケジュールの作成](how-to-create-schedules-within-teams.md)
- [Teams からの Lab Services の VM (学生ビュー) へのアクセス](how-to-access-vm-for-students-within-teams.md)

