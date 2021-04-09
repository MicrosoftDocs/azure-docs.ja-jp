---
title: Azure Lab Services でラボ アカウントを設定する | Microsoft Docs
description: Azure Lab Services にラボ アカウントを設定する方法と、ラボの作成者を追加する方法、ラボ アカウント内のラボで使用する Marketplace イメージを指定する方法について説明します。
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: fba4dbc5386407bd796606d86a5b7bdc7c10fd61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445068"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>チュートリアル:Azure Lab Services でラボ アカウントを設定する
Azure Lab Services では、ラボ アカウントは、それによって組織のラボが管理される中心的なアカウントとして機能します。 ラボ アカウントでは、ラボを作成する権限を他のユーザーに付与し、ラボ アカウントの管理下にあるすべてのラボに適用されるポリシーを設定します。 このチュートリアルでは、ラボ アカウントを作成する方法について説明します。 

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * ラボ アカウントを作成する
> * ユーザーをラボの作成者ロールに追加する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="create-a-lab-account"></a>ラボ アカウントを作成する
次の手順では、Azure portal を使って Azure Lab Services でラボ アカウントを作成する方法を示します。 

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから、 **[すべてのサービス]** を選択します。 **[カテゴリ]** から **[DevOps]** を選択します。 次に、 **[Lab Services]** を選択します。 **[ラボ サービス]** の横にある星印 (`*`) を選択した場合は、左側のメニューの **[お気に入り]** セクションに追加されます。 次回以降は、 **[お気に入り]** の下で **[ラボ サービス]** を選択します。

    ![[すべてのサービス] -> [ラボ サービス]](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. **[Lab Services]** ページでツール バーの **[追加]** を選択するか、またはそのページにある **[Create lab account]\(ラボ アカウントの作成\)** ボタンを選択します。 

    ![[ラボ アカウント] ページで [追加] を選択する](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. **[ラボ アカウントを作成します]** ページの **[Basics]\(基本\)** タブで、次の操作を行います。 
    1. **[Lab account name]\(ラボ アカウント名\)** に、名前を入力します。 
    2. ラボ アカウントを作成する **[Azure サブスクリプション]** を選択します。
    3. **[リソース グループ]** で、既存のリソース グループを選択するか、 **[新規作成]** を選択し、リソース グループの名前を入力します。
    4. **[場所]** で、ラボ アカウントの作成先となる場所 (リージョン) を選択します。 

        ![ラボ アカウント - [Basics]\(基本\) ページ](./media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. **[Review + create]\(レビュー + 作成\)** を選択します。
    6. 概要を確認し、 **[作成]** を選択します。 

        ![[Review + create]\(レビュー + 作成\) -> [作成]](./media/tutorial-setup-lab-account/create-button.png)    
5. デプロイが完了したら、 **[Next steps]\(次のステップ\)** を展開して、 **[リソースに移動]** を選択します。 

    ![[Lab account]\(ラボ アカウント\) ページに移動する](./media/tutorial-setup-lab-account/go-to-lab-account.png)
6. **[ラボ アカウント]** ページが表示されることを確認します。 

    ![[Lab account]\(ラボ アカウント\) ページ](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>ユーザーをラボの作成者ロールに追加する
ラボ アカウントでクラスルーム ラボを設定するには、ユーザーがラボ アカウントにおける **ラボの作成者** ロールのメンバーであることが必要です。 クラスのラボを作成するアクセス許可を教師に与えるには、教師を **ラボの作成者** ロールに追加します。

> [!NOTE]
> ラボ アカウントを作成するために使用したアカウントは、このロールに自動的に追加されます。 このチュートリアルでクラスルーム ラボの作成に同じユーザー アカウントを使用する場合、この手順はスキップしてください。 

1. **[ラボ アカウント]** ページで、 **[アクセス制御 (IAM)]** を選択し、ツール バーの **[+ 追加]** を選択してから、ツール バーの **[+ ロール割り当ての追加]** を選択します。 

    ![[アクセス制御] -> [ロールの割り当ての追加] ボタン](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **[ロールの割り当ての追加]** ページで、 **[ロール]** から **[ラボの作成者]** を選択し、ラボの作成者ロールに追加するユーザーを選択した後、 **[保存]** を選択します。 

    ![ラボ作成者の追加](./media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ラボ アカウントを作成しました。 教師としてクラスルーム ラボを作成する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クラスルーム ラボを設定する](tutorial-setup-classroom-lab.md)

