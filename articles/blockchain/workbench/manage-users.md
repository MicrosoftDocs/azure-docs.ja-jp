---
title: Azure Blockchain Workbench でユーザーを管理する
description: Azure Blockchain Workbench でユーザーを管理する方法について説明します。
ms.date: 07/15/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: be078e7149ba008ab8b7ad44a016a0d1cf82df0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91263013"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Azure Blockchain Workbench でのユーザーの管理

Azure の Blockchain Workbench には、コンソーシアムに属するユーザーや組織を管理するためのユーザー管理機能が備わっています。

## <a name="prerequisites"></a>前提条件

Blockchain Workbench がデプロイされている必要があります。 デプロイの詳細については、[Azure Blockchain Workbench のデプロイ](deploy.md)に関するページを参照してください。

## <a name="add-azure-ad-users"></a>Azure AD ユーザーの追加

Azure Blockchain Workbench では、認証、アクセス制御、およびロールの管理に Azure Active Directory (Azure AD) が使われます。 Blockchain Workbench の Azure AD テナント内のユーザーは、認証を受け、Blockchain Workbench を使えるようになります。 ユーザーとやりとりしたり、ユーザーに対してアクションを実行するには、管理者アプリケーション ロールにユーザーを追加します。

Blockchain Workbench ユーザーをアプリケーションやロールに割り当てるには、まずそのユーザーが Azure AD テナントに追加する必要があります。 Azure AD にユーザーを追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインする
1. 右上隅で自分のアカウントを選択し、Blockchain Workbench に関連付けられた Azure AD テナントに切り替えます。
1. **[Azure Active Directory] > [ユーザー]** を選択します。 ディレクトリ内のユーザーの一覧が表示されます。
1. ディレクトリにユーザーを追加するには、 **[新しいユーザー]** を選択します。 外部ユーザーの場合は、 **[新しいゲスト ユーザー]** を選択します。
1. 新しいユーザーの必須フィールドに情報を入力します。 **［作成］** を選択します

Azure AD 内のユーザーの管理方法について詳しくは、[Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) ドキュメントをご覧ください。

## <a name="manage-blockchain-workbench-administrators"></a>Blockchain Workbench 管理者の管理

ディレクトリにユーザーを追加したら、次の手順に従って、Blockchain Workbench の管理者となるユーザーをを選択します。 **管理者** グループ内のユーザーは、Blockchain Workbench 内の **管理者アプリケーション ロール** に関連付けられます。 管理者は、ユーザーを追加したり、削除したり、ユーザーを特定のシナリオに割り当てたり、新しいアプリケーションを作成したりすることができます。

Azure AD ディレクトリ内の **管理者** グループにユーザーを追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 右上隅で自分のアカウントを選択して、Blockchain Workbench に関連付けられた Azure AD テナントに自分が属していることを確認します。
1. **[Azure Active Directory] > [エンタープライズ アプリケーション]** を選択します。
1. **[Application type]\(アプリケーションの種類\)** ドロップダウン フィルターを **[すべてのアプリケーション]** に変更し、 **[適用]** をクリックします。
1. Azure Blockchain Workbench の Azure AD クライアント アプリケーションを選択します

    ![すべてのエンタープライズ アプリケーション登録](./media/manage-users/select-blockchain-client-app.png)

1. **[ユーザーとグループ] > [ユーザーの追加]** を選択します。
1. **[割り当ての追加]** で **[ユーザー]** を選択します。 管理者として追加するユーザーを選択するか、検索します。 選択が完了したら、 **[選択]** をクリックします。

    ![割り当ての追加](./media/manage-users/add-user-assignment.png)

1. **ロール** が **管理者** に設定されていることを確認します
1. **[割り当て]** を選択します。 追加したユーザーに管理者ロールが割り当てられ、一覧に表示されます。

    ![Blockchain クライアント アプリのユーザー](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Blockchain Workbench メンバーの管理

コンソーシアムに属するユーザーや組織の管理は、Blockchain Workbench アプリケーションを使って行います。 ユーザーの追加や削除は、アプリケーションやロールに対して行うことができます。

1. ブラウザーで [Blockchain Workbench を開き](deploy.md#blockchain-workbench-web-url)、管理者としてサインインします。

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    メンバーは各アプリケーションに追加されます。 メンバーは 1 つ以上のアプリケーション ロールを持ち、コントラクトを開始したり、アクションを実行することができます。

1. アプリケーションのメンバーを管理するには、 **[アプリケーション]** ウィンドウでアプリケーション タイルを選択します。

    選択したアプリケーションに関連付けられているメンバーの数は、メンバー タイルに反映されます。

    ![アプリケーションの選択](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>アプリケーションにメンバーを追加する

1. メンバー タイルを選択して、現在のメンバーの一覧を表示します。
1. **[メンバーの追加]** を選択します。

    ![アプリケーション メンバーシップ ウィンドウのスクリーンショット。[メンバーの追加] ボタンが強調表示されています。](./media/manage-users/application-add-members.png)

1. ユーザーの名前を検索します。  Blockchain Workbench テナントに存在する Azure AD ユーザーのみが一覧に表示されます。 ユーザーが見つからない場合は、[Azure AD ユーザーを追加する](#add-azure-ad-users)必要があります。

    ![メンバーを追加する](./media/manage-users/find-user.png)

1. ドロップダウン メニューから **ロール** を選択します。

    ![ロール メンバーの選択](./media/manage-users/application-select-role.png)

1. **[追加]** を選択して、メンバーとそのロールをアプリケーションに追加します。

#### <a name="remove-member-from-application"></a>アプリケーションからメンバーを削除する

1. メンバー タイルを選択して、現在のメンバーの一覧を表示します。
1. 削除するユーザーについて、ロールのドロップダウン リストから **[削除]** を選択します。

    ![メンバーの削除](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>ロールの変更または追加

1. メンバー タイルを選択して、現在のメンバーの一覧を表示します。
1. 変更するユーザーのドロップダウン リストをクリックし、新しいロールを選択します。

    ![ロールの変更](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>次のステップ

このハウツー記事では、Azure Blockchain ワークベンチのユーザーを管理する方法について説明しました。 ブロックチェーン アプリケーションの作成方法については、次のハウツー記事に進みます。

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench でブロックチェーン アプリケーションを作成する](create-app.md)
