---
title: "Azure Active Directory でエンタープライズ アプリからユーザーまたはグループの割り当てを削除する | Microsoft Docs"
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
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 02f122acfb53c2107e2b0af66c6195aa127a2c77
ms.contentlocale: ja-jp
ms.lasthandoff: 05/05/2017

---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリからユーザーまたはグループの割り当てを削除する
Azure Active Directory (Azure AD) で、エンタープライズ アプリのいずれかに割り当てられているアクセスからユーザーまたはグループを簡単に削除できます。 エンタープライズ アプリを管理するには、適切なアクセス許可が必要です。また、ディレクトリの全体管理者である必要があります。

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

