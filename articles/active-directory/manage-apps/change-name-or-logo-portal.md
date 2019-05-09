---
title: Azure Active Directory でエンタープライズ アプリの名前またはロゴを変更する | Microsoft Docs
description: Azure Active Directory でカスタム エンタープライズ アプリケーションの名前またはロゴを変更する方法
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62578fe037dc1c9672bd0a4cf28500c658344c53
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182920"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリの名前またはロゴを変更する
Azure Active Directory (Azure AD) で、カスタム エンタープライズ アプリの名前またはロゴを簡単に変更できます。 これらを変更するには、適切なアクセス許可が必要であり、カスタム アプリの作成者である必要があります。

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>エンタープライズ アプリケーションの名前またはロゴを変更する方法
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
3. **[Azure Active Directory - \*directoryname\*]** ウィンドウ (管理対象のディレクトリの Azure AD ウィンドウ) で、**[エンタープライズ アプリケーション]** を選択します。

    ![エンタープライズ アプリケーションを開く](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. **[エンタープライズ アプリケーション]** ウィンドウで、**[すべてのアプリケーション]** を選択します。 管理できるアプリケーションの一覧が表示されます。
5. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ウィンドウで、アプリを選択します。
6. [***appname***] ウィンドウ (選択したアプリの名前がタイトルに含まれるページ) で、**[プロパティ]** を選択します。

    ![[プロパティ] を選択する](./media/change-name-or-logo-portal/select-app.png)
7. [***appname*** - **プロパティ**] ウィンドウで、新しいロゴとして使用するファイルを参照するか、アプリ名を編集します。またはこの両方を行います。

    ![アプリケーションのロゴまたは名前プロパティを変更する](./media/change-name-or-logo-portal/change-logo.png)
8. **[保存]** をクリックします。

## <a name="next-steps"></a>次の手順
* [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションのユーザー サインインを無効にする](disable-user-sign-in-portal.md)
