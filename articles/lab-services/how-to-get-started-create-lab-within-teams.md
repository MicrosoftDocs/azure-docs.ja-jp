---
title: Teams 内での Azure Lab Services ラボの概要と作成
description: Teams 内での Azure Lab Services ラボの概要と作成について説明します。
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433973"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Teams 内での Lab Services ラボの概要と作成

この記事では、Teams に **Azure Lab Services** アプリを追加する方法と、MS Teams 環境にラボを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、自分のチームに仮想マシンを使用してラボを設定します。 ラボ アカウントにラボを設定するには、ラボ アカウントにおいて、あなたは所有者、ラボの作成者、または共同作成者ロールのメンバーである必要があります。 ラボ アカウントを作成するために使用したアカウントは、所有者ロールに自動的に追加されます。 そのため、ラボの作成には、ラボ アカウントの作成に使用したユーザー アカウントを使用できます。

Teams 内で Azure Lab Services を使用する際の一般的なワークフローは次のとおりです。

1. ユーザーが Azure portal で[ラボ アカウントを作成](tutorial-setup-lab-account.md#create-a-lab-account)します。
1. [ラボ アカウントの作成者が、他のユーザー](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)を **ラボの作成者** ロールに追加します。 たとえば、教師が自分の担当するクラスのラボを作成できるよう、ラボ アカウントの作成者または管理者が **ラボの作成者** ロールに教師を追加します。
1. 次いで、教師がラボの作成、テンプレート VM の事前構成を行い、チームの全員に VM をプロビジョニングするためにラボを公開します。
1. ラボが公開されると、Teams(SSO) の **Azure Lab Services** アプリを含むタブをクリックするか、[Lab Web サイト](https://labs.azure.com)にアクセスすることによって、チーム メンバーシップ リスト上のすべてのユーザーが Azure Lab Services に初めてログインするときに、VM が割り当てられます。 ユーザーはその後 VM を使用して、クラスでの作業や宿題を行えるようになります。

> [!IMPORTANT]
> Azure Lab Services は、ラボ アカウントが Teams と同じテナントで作成されている場合にのみ、Teams 内で使用できます。

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>チームに Azure Lab Services アプリをタブとして追加する

チーム所有者が、自分の Teams チャネルに **Azure Lab Services** アプリを直接追加すると、チーム内のすべてのユーザーがそのアプリを使用できるようになります。 以下の 3 つの手順に従ってください。

1. アプリを追加する Teams チャネルに移動し、 **+** を選択してタブを追加します。 
1. タブ オプションから **Azure Lab Services** を検索し、このアプリを追加します。 

    > [!NOTE]
    > チームのラボは、チームの **所有者** のみが作成できます。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="タブを追加する":::
1. このチームのラボの作成に使用する Lab Services アカウントを選択します。 

    Azure Lab Services では、シングル サインオンを使用し、[Azure Lab Services の Web サイト](https://labs.azure.com)からアクセス権を持つすべてのラボ アカウントをプルします。 

    Teams と同じテナントに属する、**所有者**、**共同作成者**、または **作成者** のアクセスがあるアカウントが表示されます。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="ALS へようこそ":::
1. **[保存]** を押すと、タブがチャンネルに追加されます。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="作成された ALS タブ":::

    これで、お使いのチャネルから **[Azure Lab Services]** タブを選択し、次の記事に従ってラボの管理を開始できます。

ラボ アカウントの選択後、チーム所有者はチームのラボを作成できるようになります。 ラボを作成する一連のすべての作業およびラボ レベルのすべてのタスクを、Teams 内で実行できるようになります。 ユーザーは、同じチーム内に複数のラボを作成できます。ラボ アカウント レベルで適切なアクセス権があるチーム所有者には、そのチームに関連付けられているラボのみが表示されます。

## <a name="next-steps"></a>次の手順

Teams 内にラボを作成すると、チーム メンバーシップが使用され、ラボのユーザー リストが自動入力および同期されます。 所有者、メンバー、ゲストなどのチームのすべてのユーザーは、ラボ ユーザー一覧に自動的に追加されます。 Azure Lab Services はチーム メンバーシップと同期され続け、自動での同期は 24 時間ごとにトリガーされます。 詳細については、次の情報を参照してください。

[Teams 内での Lab Services ユーザー一覧の管理](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>関連項目

また、次の記事を参照してください。

- [Teams 内での Azure Lab Services の使用の概要](lab-services-within-teams-overview.md)
- [Teams 内でのラボ VM プールの管理](how-to-manage-vm-pool-within-teams.md)
- [Teams 内でのラボ スケジュールの作成と管理](how-to-create-schedules-within-teams.md)
- [Teams 内での VM へのアクセス – 学生側のビュー](how-to-access-vm-for-students-within-teams.md)
- [Teams 内でのラボの削除](how-to-delete-lab-within-teams.md)
