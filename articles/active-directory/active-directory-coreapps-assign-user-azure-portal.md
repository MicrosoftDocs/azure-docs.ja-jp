---
title: "Azure Active Directory プレビューでエンタープライズ アプリケーションにユーザーまたはグループを割り当てる | Microsoft Docs"
description: "Azure Active Directory プレビューでエンタープライズ アプリケーションを選択し、このアプリケーションにユーザーまたはグループを割り当てる方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 18330aa5e0fe8e3bbad6c266f823a4969b9b8b6c
ms.lasthandoff: 11/17/2016


---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory-preview"></a>Azure Active Directory プレビューでエンタープライズ アプリケーションにユーザーまたはグループを割り当てる
Azure Active Directory (Azure AD) プレビューでは、ユーザーまたはグループを簡単にエンタープライズ アプリケーションへ割り当てることができます。 [プレビューの機能については、こちらの記事をご覧ください。](active-directory-preview-explainer.md) エンタープライズ アプリケーションを管理するには、適切なアクセス許可が必要です。 現在のプレビューでは、ディレクトリの全体管理者である必要があります。

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>エンタープライズ アプリケーションにユーザー アクセスを割り当てる方法
1. ディレクトリの全体管理者であるアカウントで [Azure ポータル](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「Azure Active Directory」と入力して、**Enter** キーを押します。
3. **[Azure Active Directory - <*ディレクトリ名*>]** ブレード (管理対象のディレクトリの Azure AD ブレード) で、**[エンタープライズ アプリケーション]** を選択します。

    ![エンタープライズ アプリケーションを開く](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. **[エンタープライズ アプリケーション]** ブレードで、**[すべてのアプリケーション]** を選択します。 管理できるアプリケーションの一覧が表示されます。
5. **[Enterprise applications - All applications (エンタープライズ アプリケーション - すべてのアプリケーション)]** ブレードで、アプリケーションを選択します。
6. ***[アプリケーション名]*** ブレード (タイトルが選択したアプリケーションの名前であるブレード) で、**[ユーザーとグループ]** を選択します。

    ![[すべてのアプリケーション] を選択する](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. ***[アプリケーション名*** **- User & Group Assignment (ユーザーとグループの割り当て)]** ブレードで、**[追加]** を選択します。
8. **[Add Assignment (割り当ての追加)]** ブレードで **[ユーザーとグループ]** を選択します。

    ![アプリケーションにユーザーまたはグループを割り当てる](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. **[ユーザーとグループ]** ブレードで一覧から 1 つまたは複数のユーザーまたはグループを選択し、ブレードの下部にある **[選択]** をクリックします。
10. **[Add Assignment (割り当ての追加)]** ブレードで **[ロール]** を選択します。 次に、**[ロールを選択する]** ブレードで、選択したユーザーまたはグループに適用するロールを選択して、ブレードの下部にある **[OK]** をクリックします。
11. **[Add Assignment (割り当ての追加)]** ブレードで、下部にある **[割り当て]** をクリックします。 割り当て対象のユーザーまたはグループに、このエンタープライズ アプリケーションで選択したロールにより定義されるアクセス許可が付与されます。

## <a name="next-steps"></a>次のステップ
* [自分のグループをすべて表示する](active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](active-directory-coreapps-remove-assignment-azure-portal.md)
* [エンタープライズ アプリケーションのユーザー サインインを無効にする](active-directory-coreapps-disable-app-azure-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](active-directory-coreapps-change-app-logo-user-azure-portal.md)

