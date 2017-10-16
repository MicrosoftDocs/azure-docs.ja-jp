---
title: "Azure Active Directory でエンタープライズ アプリの名前またはロゴを変更する | Microsoft Docs"
description: "Azure Active Directory でカスタム エンタープライズ アプリケーションの名前またはロゴを変更する方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d01303ce-e6cb-4f3b-a4d6-ec29dfd68146
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 1345f77df1945d3fa5bc7adc185ee5e6b6c0cc3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリの名前またはロゴを変更する
Azure Active Directory (Azure AD) で、カスタム エンタープライズ アプリの名前またはロゴを簡単に変更できます。 これらを変更するには、適切なアクセス許可が必要であり、カスタム アプリの作成者である必要があります。

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>エンタープライズ アプリケーションの名前またはロゴを変更する方法
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
3. **[Azure Active Directory - <*ディレクトリ名*>]** ブレード (管理対象のディレクトリの Azure AD ブレード) で、**[エンタープライズ アプリケーション]** を選択します。

    ![エンタープライズ アプリケーションを開く](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. **[エンタープライズ アプリケーション]** ブレードで、**[すべてのアプリケーション]** を選択します。 管理できるアプリケーションの一覧が表示されます。
5. **[Enterprise applications - All applications (エンタープライズ アプリケーション - すべてのアプリケーション)]** ブレードで、アプリケーションを選択します。
6. ***[<アプリケーション名>]*** ブレード (タイトルが選択したアプリケーションの名前であるブレード) で、**[プロパティ]** を選択します。

    ![[プロパティ] を選択する](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. [***アプリケーション名*** - **プロパティ**] ブレードで、新しいロゴとして使用するファイルを参照するか、アプリケーション名を編集します。またはこの両方を行います。

    ![アプリケーションのロゴまたは名前プロパティを変更する](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ
* [自分のグループをすべて表示する](active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](active-directory-coreapps-remove-assignment-azure-portal.md)
* [エンタープライズ アプリケーションのユーザー サインインを無効にする](active-directory-coreapps-disable-app-azure-portal.md)
