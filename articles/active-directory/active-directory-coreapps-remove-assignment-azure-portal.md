---
title: "Azure Active Directory プレビューでエンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する | Microsoft Docs"
description: "Azure Active Directory でエンタープライズ アプリケーションからユーザーまたはグループのアクセスの割り当てを削除する方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b38a613a1f93d9c36254f7642655f716e048e9e3


---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Azure Active Directory プレビューでエンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する
Azure Active Directory (Azure AD) プレビューで、エンタープライズ アプリケーションのいずれかに割り当てられているアクセスからユーザーまたはグループを簡単に削除できます。 [プレビューの機能については、こちらの記事をご覧ください。](active-directory-preview-explainer.md) エンタープライズ アプリケーションを管理するには、適切なアクセス許可が必要です。 現在のプレビューでは、ディレクトリの全体管理者である必要があります。

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>ユーザーまたはグループの割り当てを削除する方法
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
3. **[Azure Active Directory - <*ディレクトリ名*>]** ブレード (管理対象のディレクトリの Azure AD ブレード) で、**[エンタープライズ アプリケーション]** を選択します。

    ![エンタープライズ アプリケーションを開く](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. **[エンタープライズ アプリケーション]** ブレードで、**[すべてのアプリケーション]** を選択します。 管理できるアプリケーションの一覧が表示されます。
5. **[Enterprise applications - All applications (エンタープライズ アプリケーション - すべてのアプリケーション)]** ブレードで、アプリケーションを選択します。
6. ***[アプリケーション名]*** ブレード (タイトルが選択したアプリケーションの名前であるブレード) で、**[ユーザーとグループ]** を選択します。

    ![ユーザーまたはグループを選択する](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. [***アプリケーション名*** **- ユーザーとグループの割り当て**] ブレードで、1 人以上のユーザーまたは 1 つ以上のグループを選択し、**[削除]** をクリックします。 確認メッセージが表示されたら、削除を確定します。

    ![[削除] をクリックする](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>次のステップ
* [自分のグループをすべて表示する](active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)
* [エンタープライズ アプリケーションのユーザー サインインを無効にする](active-directory-coreapps-disable-app-azure-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](active-directory-coreapps-change-app-logo-user-azure-portal.md)



<!--HONumber=Nov16_HO3-->


