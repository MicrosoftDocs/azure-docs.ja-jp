---
title: "チュートリアル: ユーザー プロビジョニングのための Workplace by Facebook の構成 | Microsoft Docs"
description: "Azure AD から Workplace by Facebook に対してユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除する方法を説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a347eedbf5511dc83e1bc7721667441cfb87cb59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configure-workplace-by-facebook-for-user-provisioning"></a>チュートリアル: ユーザー プロビジョニングのための Workplace by Facebook の構成

このチュートリアルでは、Azure Active Directory (Azure AD) から Workplace by Facebook にユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除するために必要な手順を説明します。

## <a name="prerequisites"></a>前提条件

Azure AD と Workplace by Facebook の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workplace by Facebook でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の試用版プラン](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="assign-users-to-workplace-by-facebook"></a>Workplace by Facebook にユーザーを割り当てる

Azure AD では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Workplace by Facebook アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーおよびグループを決定します。 「[エンタープライズ アプリにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)」の手順に従って、これらのユーザーを Workplace by Facebook アプリに割り当てることができます。

>[!IMPORTANT]
>*   単一の Azure AD ユーザーを Workplace by Facebook に割り当てて、プロビジョニングの構成をテストします。 後で、追加のユーザーとグループを割り当てます。
>*   Workplace by Facebook にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Workplace by Facebook のユーザー アカウント プロビジョニング API に Azure AD を接続する手順を説明します。 プロビジョニング サービスを構成して、割り当て済みのユーザー アカウントを Workplace by Facebook で作成、更新、および無効にする方法についても説明します。 これは、Azure AD のユーザーとグループの割り当てに基づきます。

>[!Tip]
>Workplace by Facebook では SAML ベースの SSO を有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 SSO は自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Azure AD で Workplace by Facebook に対するユーザー アカウント プロビジョニングを構成する

Azure AD は、割り当てられたユーザーのアカウントの詳細を Workplace by Facebook と自動的に同期する機能をサポートしています。 この自動同期機能によって、ユーザーが初めてサインインする前に、Workplace by Facebook がユーザーのアクセスを承認するために必要なデータを取得できます。 また、Azure AD のアクセス権が取り消された場合、Workplace by Facebook からユーザーのプロビジョニングを解除できます。

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]** > **[エンタープライズ アプリ]** > **[すべてのアプリケーション]** の順に選択します。

2. SSO のために Workplace by Facebook を既に構成している場合は、検索フィールドを使用して Workplace by Facebook のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Workplace by Facebook** を検索します。 検索結果から **Workplace by Facebook** を選択してアプリケーションの一覧に追加します。

3. Workplace by Facebook のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。 

    ![Workplace by Facebook のプロビジョニングのオプションのスクリーンショット](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. **[管理者資格情報]** セクションの下で、Workplace by Facebook 管理者の**シークレット トークン**と**テナント URL** を入力します。

6. Azure Portal で、**[テスト接続]** を選択して Azure AD が Workplace by Facebook アプリに接続できることを確認します。 接続が失敗した場合、使用中の Workplace by Facebook アカウントに Team Admin アクセス許可があることを確認してください。

7. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

8. [ **保存**] を選択します。

9. [マッピング] セクションの **[Synchronize Azure Active Directory Users to Workplace by Facebook]\(Azure Active Directory ユーザーを Workplace by Facebook に同期する\)** を選びます。

10. **[属性マッピング]** セクションで、Azure AD から Workplace by Facebook に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Workplace by Facebook のユーザー アカウントとの照合に使用されます。 すべての変更をコミットするには、**[保存]** を選択します。

11. Workplace by Facebook に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

12. [ **保存**] を選択します。

自動プロビジョニングを構成する方法の詳細については、[Facebook のドキュメント](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)を参照してください。

これでテスト アカウントを作成できるようになりました。 ここで 20 分間待機し、アカウントが Workplace by Facebook に同期されたことを確認します。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [シングル サインオンの構成](active-directory-saas-facebook-at-work-tutorial.md)

