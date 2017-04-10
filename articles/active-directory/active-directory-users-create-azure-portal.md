---
title: "Azure Active Directory プレビューに新しいユーザーを追加する | Microsoft Docs"
description: "Azure Active Directory で新しいユーザーを追加する方法やユーザー情報を変更する方法を説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: f0e7890ba4c7ef281558886f8cb8bcc429944f06
ms.openlocfilehash: 9c0c15c0bf876110fcf9dd01a7930d3b226dfbc4
ms.lasthandoff: 02/15/2017


---
# <a name="add-new-users-to-azure-active-directory-preview"></a>Azure Active Directory プレビューに新しいユーザーを追加する
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-users-create-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-create-users.md)
>
>

この記事では、Azure Active Directory (Azure AD) プレビューに組織内の新しいユーザーを追加する方法について説明します。 [プレビューの機能については、こちらの記事をご覧ください。](active-directory-preview-explainer.md)

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

   ![ユーザー管理を開く](./media/active-directory-users-create-azure-portal/create-users-user-management.png)
3. **[ユーザーとグループ]** ブレードで、**[すべてのユーザー]** を選択し、**[追加]** をクリックします。

   ![[追加] をクリックする](./media/active-directory-users-create-azure-portal/create-users-add-command.png)
4. **名前**や**ユーザー名**など、ユーザーの詳細を入力します。 ユーザー名のドメイン名の部分は、既定の初期ドメイン名の "foo.onmicrosoft.com"、または検証済みの非フェデレーション ドメイン名 ("contoso.com" など) である必要があります。
5. このプロセスの完了後、ユーザーに提供できるように、生成されたユーザー パスワードをコピーするか、メモしておきます。
6. 必要に応じて、ユーザーの **[プロファイル]** ブレード、**[グループ]** ブレード、または **[Directory role (ディレクトリ ロール)]** ブレードを開き、情報を入力します。 ユーザーおよび管理者のロールの詳細については、「 [Azure AD での管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。
7. **[ユーザー]** ブレードで **[作成]** をクリックします。
8. ユーザーがサインインできるように、新しいユーザーに生成されたパスワードを安全に配布します。

### <a name="next-steps"></a>次のステップ
* [外部ユーザーの追加](active-directory-users-create-external-azure-portal.md)
* [新しい Azure Portal でのユーザーのパスワードのリセット](active-directory-users-reset-password-azure-portal.md)
* [ユーザーの勤務先情報の変更](active-directory-users-work-info-azure-portal.md)
* [ユーザー プロファイルの管理](active-directory-users-profile-azure-portal.md)
* [Azure AD でのユーザーの削除](active-directory-users-delete-user-azure-portal.md)
* [Azure AD でのロールへのユーザーの割り当て](active-directory-users-assign-role-azure-portal.md)

