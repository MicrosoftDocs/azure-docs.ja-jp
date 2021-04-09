---
title: Teams からの Azure Lab Services の VM プールを管理する
description: Teams からの Azure Lab Services の VM プールを管理する方法について説明します。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946534"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Teams からの Lab Services の VM プールを管理する

仮想マシン (VM) の作成は、テンプレート VM が最初に発行されるとすぐに開始されます。 ラボ ユーザー リスト内のユーザー数に相当する VM が作成されます。 VM は、最初のログイン時に、Azure Lab Services に自動的に学生に割り当てられます。 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>テンプレートを発行して VM プールを管理する

テンプレートを発行するには、Teams Lab Services ウィンドウにアクセスし、 **[テンプレート]** タブ > **...**  ->  **[発行]** を選択します。

テンプレート VM が構成され、教師がテンプレートの発行を選択すると、ラボのユーザー リスト内のユーザー数に相当する数の VM が作成されます。 ラボが発行され、VM が作成されると、ユーザーはラボに自動的に登録され、Azure Lab Services への初回ログイン時に VM が割り当てられます。つまり、最初に **Azure Lab Services** アプリがあるタブにアクセスしたときです。 

ユーザー リストの同期がトリガーされると、チーム メンバーシップへの変更に基づいてラボの容量 (ラボ内の VM の数) が自動的に更新されます。 新しいユーザーが追加されると、新しい VM が作成され、チームから削除されたユーザーに割り当てられた VM も削除されます。 詳細については、[Teams でのユーザーの管理方法](how-to-manage-user-lists-within-teams.md)に関する記事を参照してください。 

教師は、引き続き、VM プール タブから学生の VM に直接アクセスできます。また、教師は、 **[仮想マシンプール]** タブから、または **[自分の仮想マシン]** ボタン (画面の上部または右) をクリックして、自分に割り当てられている VM にアクセスできます。 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="VM プール":::

## <a name="next-steps"></a>次のステップ

次の記事をご覧ください。

- [Teams での Azure Lab Services の使用の概要](lab-services-within-teams-overview.md)
- [Teams からの Lab Services ラボの作成および概要](how-to-get-started-create-lab-within-teams.md)
- [Teams からの Lab Services ユーザー リストを管理する](how-to-manage-user-lists-within-teams.md)
- [Teams からの Lab Services スケジュールの作成](how-to-create-schedules-within-teams.md)
- [Teams からの Lab Services の VM (学生ビュー) へのアクセス](how-to-access-vm-for-students-within-teams.md)


