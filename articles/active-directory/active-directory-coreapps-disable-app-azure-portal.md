---
title: "Azure Active Directory プレビューでエンタープライズ アプリケーションのユーザー サインインを無効にする | Microsoft Docs"
description: "Azure Active Directory で、ユーザーがサインインできないようにエンタープライズ アプリケーションを無効にする方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: c57505d037d991f3c0d720a481393ec2ae6b8069
ms.openlocfilehash: ef6ae32c1d9b2e417d2a766207575f19aa06feaa
ms.lasthandoff: 01/19/2017


---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Azure Active Directory プレビューでエンタープライズ アプリケーションのユーザー サインインを無効にする
Azure Active Directory (Azure AD) プレビューで、ユーザーがサインインできないようにエンタープライズ アプリケーションを簡単に無効にすることができます。 [プレビューの機能については、こちらの記事をご覧ください。](active-directory-preview-explainer.md) エンタープライズ アプリケーションを管理するには、適切なアクセス許可が必要です。 現在のプレビューでは、ディレクトリの全体管理者である必要があります。

## <a name="how-do-i-disable-user-sign-ins"></a>ユーザー サインインを無効にする方法
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
3. [**Azure Active Directory** -  ***<ディレクトリ名>***] ブレード (管理対象のディレクトリの Azure AD ブレード) で、**[エンタープライズ アプリケーション]** を選択します。

    ![エンタープライズ アプリケーションを開く](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. **[エンタープライズ アプリケーション]** ブレードで、**[すべてのアプリケーション]** を選択します。 管理できるアプリケーションの一覧が表示されます。
5. **[Enterprise applications - All applications (エンタープライズ アプリケーション - すべてのアプリケーション)]** ブレードで、アプリケーションを選択します。
6. ***[<アプリケーション名>]*** ブレード (タイトルが選択したアプリケーションの名前であるブレード) で、**[プロパティ]** を選択します。

    ![[すべてのアプリケーション] を選択する](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. [***<アプリケーション名>***  -  **プロパティ**] ブレードで、**[ユーザーのサインインが有効になっていますか?]** の **[いいえ]** を選択します。
8. **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ
* [自分のグループをすべて表示する](active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](active-directory-coreapps-remove-assignment-azure-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](active-directory-coreapps-change-app-logo-user-azure-portal.md)

