---
title: チュートリアル:Workplace by Facebook を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory と Workplace by Facebook の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c2e23b0d60ca242549ebf2c058ea8f44f2b1c8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187799"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>チュートリアル:Workplace by Facebook を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Workplace by Facebook にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Workplace by Facebook と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

Azure AD と Workplace by Facebook の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workplace by Facebook でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="assigning-users-to-workplace-by-facebook"></a>Workplace by Facebook へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Workplace by Facebook アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Workplace by Facebook アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>ユーザーを Workplace by Facebook に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Workplace by Facebook に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Workplace by Facebook にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="enable-user-provisioning"></a>ユーザー プロビジョニングの有効化

このセクションでは、Azure AD を Workplace by Facebook のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Workplace by Facebook で作成、更新、無効化する手順を説明します。

>[!Tip]
>Workplace by Facebook では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Azure AD で Workplace by Facebook に対するユーザー アカウント プロビジョニングを構成するには:

このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを Workplace by Facebook に対して有効にする方法について説明します。

Azure AD は、割り当てられたユーザーのアカウントの詳細を Workplace by Facebook と自動的に同期する機能をサポートしています。 この自動同期機能によって、ユーザーが初めてサインインする前に、Workplace by Facebook がユーザーのアクセスを承認するために必要なデータを取得できます。 また、Azure AD のアクセス権が取り消された場合、Workplace by Facebook からユーザーのプロビジョニングを解除できます。

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]** > **[エンタープライズ アプリ]** > **[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために Workplace by Facebook を既に構成している場合は、検索フィールドで Workplace by Facebook のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Workplace by Facebook** を検索します。 検索結果から Workplace by Facebook を選択してアプリケーションの一覧に追加します。

3. Workplace by Facebook のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。 

    ![プロビジョニング](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. **[管理者資格情報]** セクションの下で、Workplace by Facebook 管理者からアクセス トークンを入力し、テナント URL の値を `https://www.facebook.com/scim/v1/` に設定します。 Workplace のアクセス トークンを作成する方法については、こちらの[手順](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps)を参照してください。 

6. Azure Portal で、**[テスト接続]** をクリックして Azure AD が Workplace by Facebook アプリに接続できることを確認します。 接続が失敗した場合、使用中の Workplace by Facebook アカウントに Team Admin アクセス許可があることを確認してください。

7. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

8. **[保存]** をクリックします。

9. [マッピング] セクションの **[Synchronize Azure Active Directory Users to Workplace by Facebook]\(Azure Active Directory ユーザーを Workplace by Facebook に同期する\)** を選びます。

10. **[属性マッピング]** セクションで、Azure AD から Workplace by Facebook に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Workplace by Facebook のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

11. Workplace by Facebook に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

12. **[保存]** をクリックします。

自動プロビジョニングを構成する方法の詳細については、[https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers) を参照してください。

これでテスト アカウントを作成できるようになりました。 ここで 20 分間待機し、アカウントが Workplace by Facebook に同期されたことを確認します。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [シングル サインオンの構成](workplacebyfacebook-tutorial.md)
